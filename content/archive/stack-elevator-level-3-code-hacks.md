---
title: 'Stack Elevator - Level 3: Code Hacks'
date: Mon, 16 Apr 2018 10:00:00 +0000
draft: false
tags: ['BOSH', 'CF Summit Boston 2018', 'Cloud Foundry', 'Cloud Foundry', 'Diego', 'garduan', 'ITQ', 'metron', 'PiFoundry', 'Raspberry Pi', 'seccomp']
---

This is the 5th post in a series about porting Cloud Foundry to the Raspberry Pi. This time we will look into what needed to be done to the actual CF source code to make it run on 32bit ARM CPUs.

Compile Time errors
-------------------

_All modifications were done on Garden-runc version 1.11.1. Code might have changes since then._ After modifying the garden-runc release I tried BOSH deploying it and was welcomed by some nice compile errors:```
\# code.cloudfoundry.org/grootfs/base\_image\_puller/unpacker​
base\_image\_puller/unpacker/tar\_modtime\_linux.go:23:32: cannot use utimeOmit (type int64) as type int32 in field value​

# code.cloudfoundry.org/grootfs/store/filesystems​
store/filesystems/filesystems.go:30:17: invalid operation: statfs.Type != fsType (mismatched types int32 and int64)​
make: \*\*\* \[all\] Error 2​
```As you can see grootfs is the culprit here. Let´s take a look at the first error. This is what is on line 22-25 of tar\_modtime\_linux.go:```
ts := \[\]syscall.Timespec{
		syscall.Timespec{Sec: 0, Nsec: utimeOmit},
		syscall.NsecToTimespec(modTime.UnixNano()),
	}
```So it tries to assign utimeOmit which is defined as int64 on line 12 of the same file: const utimeOmit int64 = ((1 << 30) - 2) but this fails because the syscall.Timespace.Nsec field is declared as int32 according to the compiler. So let's take a look at the Timepsec definition in the Golang [documentation](https://golang.org/pkg/syscall/#Timespec):```
type Timespec struct {
        Sec  int64
        Nsec int64
}
```yep, looks like int64. So now let's take a look at the Timespec definition in the [source](https://golang.org/src/syscall/syscall_linux_arm.go) of the unix package for ARMv7:```
type Timespec struct {
	Sec  int32
	Nsec int32
}
```Aha! so on 32bit ARM Nsec is actually an int32. Lets take take another look at the utimeOmit variable declaration: const utimeOmit int64 = ((1 << 30) - 2) . The number that is put into the variable can actually fit into a 32bit int. So the fix for this one is very simple: const utimeOmit int32 = ((1 << 30) - 2) Unfortunately this fix now makes the code incompatible with x86 so I can't do a pull request for this change. It is possible to use build tags but that would mean separating this part of the code into a different file. Maybe I'll do just that when I find the time. The second compile error comes down to a similar problem: In grootfs/store/filesystems/filesystems.go statfs\_t from the syscall package is used. statfs\_t.Type is int64 on x86 but it is int32 on ARMv7. This time I just cast the statfs\_t.Type to int64 when compared to fsType which only happens on line 30 of the mentioned file:```
if int64(statfs.Type) != fsType {
```

Runtime Errors
--------------

That was all for the compile time errors. Not too bad imho. But we're not out of the woods yet... The stuff has to run as well. Unfortunately most of the 32/64 bit incompatibilities were found at runtime. For who is interested I'll discuss all of them below, or at least all that I can remember and made notes on.

### idmapper

garden comes with its own id mapper. It maps user and group IDs on the host to other IDs in the containers. It also tries to determine the maximum uid/guid number it can use and then starts handing out IDs starting at the highest number and counting down. To determine the maximum ID the id mapper parses /proc/self/uid\_map. Here is the bit of code that does this (from idmapper/max\_valid\_uuid.go):```
var container, host, size int
		if \_, err := fmt.Sscanf(scanner.Text(), "%d %d %d", &container, &host, &size); err != nil {
			return 0, ParseError{Line: scanner.Text(), Err: err}
		}
```So it uses an int to load the size. unfortunately the number does not fit into an int32. The type used is an int but an int on ARMv7 is 32bit and on x64 is 64bit. So this code works fine on x86 CPUS but not in a Raspberry. My first attempt at fixing this issue was simply to change the var type to int64. But this lead me into a very deep rabbit hole mismatched types. I think I spent at least a week, maybe more in that rabbit hole and I still did not see the end of it. Having the feeling I lost track I made the only sensible decision: Back out and start over. After some more thinking I solved (Well.. hacked) it for now by introducing an extra variable of int64 type, then converting it to int. If the number is below 0 is overflows apparently and I just set a predefined maximum. Here is the code:```
var container, host, size int
		var size64 int64
		if \_, err := fmt.Sscanf(scanner.Text(), "%d %d %d", &container, &host, &size64); err != nil {
			return 0, ParseError{Line: scanner.Text(), Err: err}
		}

		if int(size64) < 0 {
			size = 32768
		} else {
			size = int(size64)
		}
```Now that I changed the max ID I also had to change idmapper/cmd/mapper.go to keep the ID within bounds.

### Guardian

It's not a whole lot of text about the ID mapper but It took me quite a long time to get to a working solution. After fixing the ID mapper garden-runc did finally deploy successfully. yay! So time to try and run a container. I used the gaol command line utility for testing so I could keep the feedback cycle very short. If you've reading my blog posts in this series you'll see a recurring theme by now.... "Run it, fail, try fix, fail, sepnt long time finding the real fix, run it.... and so on". So it was the same here: garden runs fine but could not start a container. Or actually it looked like it started a process in a container but was then shutdown immediately. 2 weeks later...... I'll spare you all the research I did and all the leads I followed. I can't even remember halve of them but it did include recompiling kernels and what not. But it turned out to be the seccomp policy that killed my containers. In the policy were some references to x86\_something. I did a brief attempt to change that into ARM\_something. Did not work. My solution is on line 733 of guardian/guardiancmd/command.go:```
unprivilegedBundle.Spec.Linux.Seccomp = nil
```yep.... no policy. Guess that's pretty insecure.... Oh well :)

### nstar

ok technically this is part of guarding but it is an external executable written in C instead of Go so I'll grant it its own chapter in this post. Also this is a bit of a deviation from the chronological order I have been writing in so far. We only encountered nstar when we were finally ready to deploy CF apps. But since it's part of the garden-runc release and its a runtime error I'll discuss this now. nstar is short for namespace tar. Unclear to me what it exactly does but it does some magic with namespaces so you're able to tar bit into and out of a container. At something like that. Please correct me if I'm wrong here. When I finally got to the point that I was able to start an actual CF app garden threw this error at me:```
{"timestamp":"1519480532.570663691","source":"guardian","message":"guardian.api.garden-server.stream-in.failed","log\_level":2,"data":{"destination":"/tmp/app","error":"stream-in: nstar: error streaming in: exit status 1. Output: execveat: Invalid argument\\n","handle":"4c53c0bf-4eb3-47f1-8f47-051baea50518","session":"1.1.5","user":"vcap"}}
```turns out _execveat _is a syscall. An a rather new one so it is not recognized by the C compiler. To tell the compile what to do there is this define in the C code: #define EXECVEAT\_CODE 322 . A quick chat with Ruurd and some googling for syscall codes later I discovered that the syscall code for _execveat_ is different on ARM cpus. It's even different between 64 and 32bit x86 CPUs. So here is the changed code: #define EXECVEAT\_CODE 387

### Metron

After I got garden-runc to work I tried deploying it as part of an existing CF foundation. That means it is deployed together with the other releases I mentioned in the [previous post](https://wp.me/p4Cjyf-gO). That all worked out pretty well except metron kept crashing on me every 2 seconds or so. It threw this error:```
panic: runtime error: invalid memory address or nil pointer dereference​
\[signal SIGSEGV: segmentation violation code=0x1 addr=0x4 pc=0x128b8\]​
​
goroutine 39 \[running\]:​
sync/atomic.addUint64(0x10c1a3f4, 0x1, 0x0, 0x10c1a420, 0x10c2a030)​
        /var/vcap/data/packages/golang1.9.4/c657140a594039ee81f353af371225533c09be56/src/sync/atomic/64bit\_arm.go:31 +0x4c​
code.cloudfoundry.org/loggregator/metricemitter.(\*Counter).Increment(0x10c1a3e0, 0x1, 0x0)
```The golang docs gave me the solution:

> “On both ARM and x86-32, it is the caller's responsibility to arrange for 64-bit alignment of 64-bit words accessed atomically. The first word in a variable or in an allocated struct, array, or slice can be relied upon to be 64-bit aligned.”

What that quote told me to do was to put all struct elements that are operated upon by the atomic package to the top of the deceleration.  So here is an example of an original struct declaration:```
type ManyToOne struct {​
   buffer     \[\]unsafe.Pointer​
   alerter    Alerter​
 **writeIndex uint64​
   readIndex  uint64​**
}
```And here is my version:```
type ManyToOne struct {​
   **writeIndex uint64​
   readIndex  uint64​**
   buffer     \[\]unsafe.Pointer​
   alerter    Alerter​
}
```I was surprised but that actually works! I did a search through the metron code looking for usage of the atomic package and just changed all involved structs. Problem solved! pfew... that was it for the code hacks. If you're still with me you earned my respect :)