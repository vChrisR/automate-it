---
title: 'Beyond automated deployment'
date: Mon, 01 May 2017 09:00:30 +0000
draft: false
tags: ['BOSH', 'Cloud Foundry', 'Concourse', 'day 2', 'deployment', 'ITQ', 'operations', 'platform', 'vRA', 'vRO']
---

I have been involved in quite a lot of automation projects over the last five years. All of them centered around VMware vRealize Automation and vRealize Orchestrator. During these projects customers throw all kinds of challenges at me. Most of which I can solve. Over the years however I found two challenges that go beyond automated deployment which I can't really solve using vRA/vRO:

1.  If you update a vSphere template, how do you make sure all machines deployed from that template are also updated?
2.  If you change a blueprint, how do you make sure those changes are also made to existing deployments from that blueprint?

The answer two both really is: you can't. Not If you're using vRA/vRO. Dont' get me wrong. I'm not trying to bash these products here. It's just a result of how these products are designed and how they work. In my opinion both problems boil down to the fact that in vRA blueprints you define the initial state of a deployment, not the desired state. So if you deploy a blueprint you get whatever was specified in that blueprint. Which is fine initially. But if you change the blueprint or update the template, nothing will be changed on the existing deployments. The other way around is true as well: If you change/damage your deployment, vRA won't come in and fix it for you. Now this seems obvious and not a big problem. After all: getting deployment times down from weeks to minutes using automation tools is a pretty good improvement in its own right. But if you think about it for a minute you'll realize that when you have automated deployment, now you need to spent the rest of your days automating day 2 operations. After all the tool isn't doing it for you. For example you'll have to introduce a tool which manages patches and updates on existing deployments. You also need to figure out a way to keep your template up-to-date, preferable automated. And if somebody breaks his deployment you need to spent time fixing it. Now, if you've been following my blog recently you probably already guessed the solution to this problem: [BOSH](http://bosh.io) :). Here are four reason why BOSH makes your life as a platform operator easier:

1.  In BOSH a template is called a stemcell and stemcells are versioned. You don't have to make you own, up-to-date versions of CentOS and Ubuntu stemcells are available online at [bosh.io](http://bosh.io).
2.  When you're using BOSH, software is installed on stemcells by using BOSH releases. Which are versioned, available online and actively maintained.
3.  A BOSH deployment defines a desired state. So if a VM disappears BOSH will just re-create it, re-install the software and attach the persistent disk. Also, when you update the deployment manifest to use a newer stemcell version, BOSH will just swap out the current OS disk with the new one in a few seconds and everything will still work afterwards.
4.   All these parts can be pushed through a [Concourse](http://concourse.ci) Pipeline! The pipeline will even trigger automatically when a new stemcell version, release version or deployment manifest version is available. Below is a screenshot of a very simple pipeline I build. This pipeline keeps both the software and the OS of my redis server up-to-date without me ever touching anything.

[![](http://www.automate-it.today/wp-content/uploads/2017/04/Screenshot-from-2017-04-29-14-23-39-300x76.png)](http://www.automate-it.today/beyond-automated-deployment/screenshot-from-2017-04-29-14-23-39/)

You can find the source files for this pipeline [here](https://github.com/vChrisR/pipelinedemos). In real life you 'd probably would want to add a few steps to this pipeline. First you deploy it to a test environment, then do some automated tests and then push it into production.

In summary: If you're using BOSH not only do you get all the goodness of versioning and desired state config, it also enables you to employ Continuous Deployment for all your servers and software. You can even test new versions automatically so you don't have to spent all your time just keeping your platform up-to-date.