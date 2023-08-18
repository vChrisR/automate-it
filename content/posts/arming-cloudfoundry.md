---
title: 'ARMing Cloudfoundry'
date: Tue, 23 Oct 2018 13:50:11 +0000
draft: false
tags: ['ARM', 'ARM64', 'Cavium', 'CF summit', 'CF Summit Basel 2018', 'Cloud Foundry', 'Cloud Foundry', 'ITQ', 'packet.net']
---

Earlier this year Ruurd Keizer and I presented Cloud foundry Diego cells running on raspberry Pi. For the CF Summit in Basel we decided the scale things up a bit. This CF Summit we presented a complete Cloud foundry foundation running on a 96 core enterprise ARM machine. If you want to see our talk and demo you can checkout the recording of our session at CF Summit: \[embed\]https://www.youtube.com/watch?v=OJqv7JqLaQo\[/embed\]

But Why?
--------

There is more than one answer to this question. First of all we wanted to learn more about the inner workings of CF. That's why we started porting it to the Raspberry Pi in the first place. We also needed something cool to talk about at CF Summit :). The reason to port it to ARM for me personally is mainly an obsession with efficiency in general and energy efficiency specifically. The IT industry is one, if not the, largest energy user on the planet. ARM CPUs are known for their better power/performance ratio compared to x86 chips. So I think it would be a good thing if more companies started using ARM CPUs. Of course in reality they won't be used to lower electricity use but to up the density of the datacenters. The last reason for choosing ARM is that ARM is currently winning market share in enterprise and HPC datacenters. So the ability to run CF on both 86 and on ARM will increase the target market for CF.

Heavy Metal
-----------

To build this proof of concept we used [packet.net](http://packet.net). They are basically a cloud which offers bare metal machines. This means the experience is very cloud like but you don't get a VM or a container but you'll get a real physical machine. One of the machines packet.net offers is a 2x Cavium ThunderX machine. Each thunderX CPU has 48 cores so this machine has 96 ARM64 cores in total! packet.net runs an opensource initiative called [WorksOnArm](https://www.worksonarm.com/) one of their projects allows you to use terraform to deploy openstack on ARM on packet.net. You can find the git repo [here](https://github.com/WorksOnArm/OpenStackWorksOnArm). We used this to deploy openstack. I would have preferred to use vSphere just because I am fimiliar with that. VMware did announce ARM support for vSphere but it was not available for us yet when we started this project. Luckily terraform made my life easy so I didn't have to waste too much time setting that up.

But does it work?
-----------------

You'll have to watch the video above to see the results :). But yes! it does work. Here are the steps involved in making it work:

1.  Build an ARM64 stemcell. This included modifying the BOSH agent to accept an EFI partition.
2.  search/replace all golang and other binaries in all BOSH releases involved in deploying CF
3.  One minor code change
4.  Create an ARM64 stack
5.  Create an ARM64 buildpack

That's it. We did it in about a month including learning and setting up openstack and preparing for the talk. What we did not get to work was Credhub. Somehow kept failing to start and we ran out of time to fix it. I'm pretty sure it's fixable though.

Now what?
---------

During the talk we did a shout out to the community to consider officially supporting multi-CPU architectures in Cloud foundry. We clearly see an upwards trend in the adoption of ARM CPUs in datacenters. There are also lots of people and companies who would be happy to consume a bit less energy and use cheaper machines at the same time. We would be happy to put some effort into upstreaming our work and taking this a step further. But or spare time is very limited and would need resources (as in metal but also time) to do this. So if there is any (ARM) vendor out there that wants to sponsor this effort.... let me know.