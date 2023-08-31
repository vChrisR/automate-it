---
title: 'What is BOSH?'
date: Wed, 12 Apr 2017 09:00:20 +0000
draft: false
tags: ['BOSH', 'Cloud Foundry', 'Cloud Foundry', 'vRA']
---

In a [previous post](http://www.automate-it.today/what-is-cloud-foundry/) I went into what Cloud Foundry is and why you'd want to use it. What I didn't go into was some of the magic behind the scenes. For infra minded people like myself this part might be even more exciting than the platform itself.  The thing that makes Cloud Foundry so robust and portal is called BOSH. So What is BOSH? ![](https://upload.wikimedia.org/wikipedia/en/4/41/Bosh-logo.png) BOSH is a recursive acronym for "BOSH Outer Shell". But that doesn't tell you much about what it does. The [bosh.io](http://bosh.io/) website explains: "BOSH is an open source tool for release engineering, deployment, lifecycle management, and monitoring of distributed systems."

What does BOSH do?
------------------

It's kinda hard to put BOSH in a certain box like "cloud management platform" or "software deployment tool". BOSH does a lot of things: It deploys virtual machines, but it's not strictly a virtual machine deployment tool. It deploys software but it's not just a software deployment tool and last but not least it also monitors but it's definitely not a monitoring tool. It's something better. BOSH deploys versioned software into a running infrastructure. The software needs a VM to run on so BOSH also deploys a VM. Once software is deployed it's important that it keeps running. So BOSH also monitors the software and automatically heals the application when needed. If you accidentally delete a VM that's part of a software deployment, BOSH will automatically redeploy the VM, install the software and rejoin the cluster.

BOSH components and concepts
----------------------------

A BOSH installation consists of the following componenst:

*   BOSH Director: This is what you could call the "BOSH Server". It is the main part of the software that is responsible for orchestrating deployments and acting on health events.
*   BOSH Agent: This is a piece of software that runs on every VM deployed by BOSH. It is responsible for all the tasks that happen inside the VM.
*   CPI: The Cloud Provider Interface is a component that implements an API which enables BOSH to communicate with different types of infrastructure. There are CPIs for vSphere, vCloud, Google cloud, AWS and even for rackHD if you want to deploy to phyisical hardware. The CPI basically translates what BOSH wants to do to the specific cloud platform you want to deploy to.

When working with BOSH you'll use the following constructs:

*   Stemcell: This is a bare bones virtual machine image that includes a BOSH agent. It's a zip file with some descriptor fields and a machine image. Stemcells are platform specific. So there are stemcells for AWS, vSphere and so on. In the case of a vSphere stemcell you'll simply find a VMDK packaged in a zip. You can download [publicly available stemcells](http://bosh.io/stemcells) but you can also build your own if you want to.
*   Release: A BOSH release a a bundle of everything that is needed to deploy a specific application excluding the virtual machine templates. So it includes all runtimes, shared libraries and scripts that are needed to get the application running on a stemcell. There are [public releases](http://bosh.io/releases) for a lot of opensource software including Cloud foundry.
*   Manifest: This is a YAML file that describes how stemcells and releases will be combined into a deployment. It describes the desired state. If you're familiar with vRealize Automation, this is basically a blueprint.
*   Deployment: A deployment is basically the execution of a manifest. A deployment can contain many machines. When deploying, BOSH uses the manifest to determine the desired state. If you running the deployment BOSH will determine the current state and will do what is necessary to get to the desired state. This is contrary to what vRealize Automation does. When you change a vRA blueprint, that does not change any of the deployments. But if you change a BOSH manifest and run deploy again for that manifest BOSH will implement whatever changes you made to the desired state.

Can I try it?
-------------

Start out with [bosh.io](http://bosh.io/) The documentation is quite good but the learning curve can be a bit steep. I hope to give you some pointers on how to get it running in another blog post soon.