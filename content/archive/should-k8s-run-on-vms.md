---
title: 'Should K8s run on VMs?'
date: Mon, 10 Feb 2020 14:15:23 +0000
draft: false
tags: ['Bare-metal', 'ITQ', 'Kubernetes', 'Kubernetes', 'VM']
---

This morning I saw the VMware twitter account [tweet](https://twitter.com/VMware/status/1226718455782617088) a link to [this](https://neonmirrors.net/post/2020-01/why-k8s-on-vms/) blog post from [Chip Zoller](https://twitter.com/chipzoller). It's about why it's better to run Kubernetes on VMs as opposed to running it on base-metal.

I think the post is a good read so before you proceed please checkout Chips [post](https://neonmirrors.net/post/2020-01/why-k8s-on-vms/) first. That being said, I think there are some arguments in there that are not 100% valid. I'll give you the TL;DR and then go through each point in Chips post.

TL;DR;
------

If you're running k8s on bare-metal you'll miss out on some infra level features you are used to when running on vSphere. My point is that there are alternatives and that in some cases you don't need those feature at all (like HA) because cloud native apps handle those things in the application layer.

The only valid point I see is that VMs still provide a far better isolation than containers.

Let's dive in
-------------

**No cloud provider, no storage/network options**

This statement is somewhat true. But only if you refuse to go outside your vSphere Comfort Zone. of course if you run on bare-metal you can't automatically deploy an Amazon ELB. But the same is true if you run on vSphere. If you're running on vSphere you'll need to add NSX-T to get that same functionality. And guess what... NSX-T runs [fine](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.4/installation/GUID-1B199883-EAED-45B2-B920-722EF4A54B33.html) on physical machines, not just in vSphere environments.

For storage you could easily use NFS or Ceph or ScaleIO or whatever works for you. Saying that vSphere already provides the storage for you is not true. You'll have to maintain the storage solution vSphere is using anyways. Even if it's vSAN. So imho it does not matter if that somebody in your company is maintaining storage for vSphere or for bare-metal kubernetes. Obviously if you run on a public cloud you don't have to worry about that.

**No fleet management, node scale. Automating bare metal very difficult.**

well... ever looked at [packet.net](https://www.packet.com/)? Apart from that this statement is very true. Especially for on-prem hardware.

**Hardware issues, firmware, drivers, tuning. Linux distro compatibility.**

The main take away being this quote: "_On virtual machines, the precise physical details are abstracted away from you as the hypervisor presents a set of consistent, virtualized resources_" But eehh... your vSphere layer has to run somewhere right? So you'll still have manage the firmware and all that, it's just someone else's problem. But with on-prem hardware it is still your companies problem. Of course there are solution out there that make life easier (uch.. VxRail) but why couldn't a company (Dell?) come up with a similar offering for bare-metal k8s?

**No built-in monitoring for bare metal, not too many solutions out there for that.**

This might be a valid point. Although I'm pretty sure Prometheus can monitor most stuff. Just slap the node exporter on all your machines anre you're done. You'll probably do that in a virtual environment as well anyways so there is no added "cost" here.

**Procurement process and installation. Heterogeneous hardware.**

VMs do not run on thin air. They run on hardware. So given the same amount of hardware resources for a bare-metal vs VM environment the installation and procurement process should not be any different.

**Persistent storage on bare metal way more difficult.**

This point is redundant. See first point.

**Config management**

Well... which config management tool do you use for you VMs? It's not much different than that I'd think. Puppet, Chef, Ansible work great with bare-metal. Only if you're talking about BOSH and maybe terraform you might have a point. There is a solution out there to manage physical machines with BOSH but not many people are using it. Still possible though.

**Patching process on hardware, must kill workloads**

If you path your K8s worker VMs you must kill workload as well! So by using vSphere VMs the only thing you're avoiding is having to shutdown workers just for firmware upgrades.

**Security isolation and multi-tenancy. Blast radius.**

This! This is the real reason to run on VMs. I can't think of any better reason. Another way to achive the same would be to spin up a bare-metal cluster fore each tenant. But that would lead to a lot of resource waste and management overhead.

**No dynamic resource utilization (DRS)**

100% true. But I would be curious to see what actually happens with load distribution in a bare-metal cluster. IF your apps scale horizontally you'll end up distributing the load equally among the worker nodes. So you might nod need DRS.

**No failure recovery of hardware.**

Yes it takes 5 minutes for k8s to detect a lost node. But that should not matter. Because you're supposed to run cloud native apps on k8s. This means that availability is taken care of in the application layer, not on infra layer. The whole point of cloud native is to assume the infra is unreliable and can fail at any moment.

**No extra availbility features.**

huh? k8s has a ton of availability features. you can configure your pods to not run on the same node or in the same AZ? There are (anti) affinity rules and taints and toleration. I do agree with the fact that there is probably no orchestration layer that will bring back a physically failed node. But again, you could use BOSH for that, it's probably not the easiest thing in the world but definitely possible.

**Availability of snapshots.**

You could have your hardware boot from SAN and then make SAN based snapshots.