---
title: 'My personal VMworld news highlights'
date: Thu, 28 Aug 2014 14:48:11 +0000
draft: false
tags: ['docker', 'OCP', 'project fargo', 'VMworld', 'vmworld US 2014', 'VMworld US 2014']
---

The 11th edition of the annual [VMworld](http://www.vmworld.com) conference is taking place this week in San Francisco. I am not there this year but I have been following the new coming from the event closely. There are a lot of blogs out there about all the obvious highlights of the event. Like the new NSX version, vCloud Air, EVO, VMware Workspace and the rebranding of all management tools to vRealize. However, there are a few announcements which really caught my attention. So here are my personal VMworld news highlights

Containers
----------

![](https://d3oypxn00j2a10.cloudfront.net/0.8.0/img/nav/docker-logo-loggedout.png) Containers are a way to run multiple applications isolated from each other on the same OS. So it's like virtualization inside the OS instead of underneath the OS. This technology has been in use by Google for years. It is their primary way of deploying applications. So it's not a new technology but still it wasn't used a lot outside the big web companies. But that is changing rapidly with the introduction of Docker. Docker makes application containers portable. Very much like x86 virtualization made systems portable. One could argue that containers render virtual machines obsolete. But in many cases combining VMs and containers will be the best solution. As Kit Colbert put it in [this session](http://vmware.mediasite.com/mediasite/Play/b20c3afcaa1146f68950c143925db1131d?catalog=30d9dd2f-c6cf-4290-8e8c-c1846a96a056): VMs are used for security and multitenancy and Containers are used for reproducibility. VMware recognized the value of containers and at VMworld they [announced](http://cto.vmware.com/vmware-docker-better-together/) that they will be working with Docker to integrate in in their product lines. As you can read in [this](http://www.zdnet.com/vmware-buys-into-docker-containers-7000032947/) article VMware will be using docker in the future to deliver their own software. They will also be collaborating with Docker on opensource projects. I think this is a great development. Application deployment can be difficult and VMware has currently now technology to make it any easier. Apart from Application Director maybe but that's just a glorified script launcher, hardly a new technology. docker will make the lifes of those responsible for deploying applications a lot easier.

Project Fargo
-------------

Project fargo is also called VM fork. And that exactly describes what it is: It is a technology which makes it possible to fork a running VM. In other words, spin up a copy of a running VM without having to boot anything. Combine this with containerized applications and you're able to scale out an application in a second. You can read a bit more about it [here](http://cto.vmware.com/vmware-docker-better-together/).

Open compute project
--------------------

![](http://regmedia.co.uk/2011/10/27/open_compute_logo.jpg) VMware [announced](http://www.opencompute.org/blog/vmware-joins-open-compute-project-as-gold-member/) that they are joining the [Open Compute Project](http://www.opencompute.org/). I have written about OCP [before](http://itq.nl/open-compute-project-efficient-computing-everyone/) and I am still following the project closely. I really like the hardware designs because of their efficiency. Now VMware support vSphere 5 on both the AMD and Intel compute nodes. This is good news for are service providers out there running on vSphere 5. The OCP hardware is a lot cheaper and more energy efficient than any other server, which means they'll be able to offer better value for money.