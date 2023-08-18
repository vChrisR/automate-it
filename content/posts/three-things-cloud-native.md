---
title: '3 things you must learn to start your cloud native journey'
date: Thu, 13 Feb 2020 10:55:00 +0000
draft: false
tags: ['12 factor', 'Cloud Native', 'Cloud Native', 'git', 'ITQ', 'Kubernetes']
---

In a [previous](https://automate-it.today/what-cloud-native-is-all-about/) post a talked about what cloud native is all about. In this post I'll share three topics you should master if you want to start on your own cloud native journey.

developer mindset
-----------------

Managing infrastructure and platforms is more and more becoming similar to developing software. Therefore it is important to use the same mindset and technologies that software developers use to build and release software reliably into production.

How is infrastructure becoming similar to software? Partly because of infrastructure as code. This means that you no longer configure your infra through a GUI or CLI but only using text files. A few examples of tools being used are [Terraform](https://www.terraform.io/), [BOSH](https://bosh.io/docs/) and [Kubernetes](https://kubernetes.io/).

Now that all config is stored in text files you can simply handle these files in similar ways as you'd handle application code. You can now version it, review it, test it and then promote it to various environments. This workflow is identical to how software developers work. This in turn means you can now use the same tools to automate this process. [Concourse](https://concourse-ci.org/) is a good example of a pipeline tool that is use by both devs and infra/ops people.

Another reason to adopt developer ways of working is that it increases both reliability and agility. If you pipeline all your config changes and enforce automated tests in your pipeline you can makes changes in production with more confidence. If you have more confidence you'll make changes quicker and more often. On top of that the quality of the changes will be higher because you tested everything. Which should result in way less production outages.

git
---

Now that we have established that it is important to think and act like a developer there is one skill you MUST learn pretty early on: [git](https://git-scm.com/downloads)

git is a code versioning system. You have probably heard of [github](https://github.com/) which is one of the most well known public git services. So why not get started today and create your own github account, then go [here](https://try.github.io/) and learn how to use it.

You don't need to know everything about git to start using it. Here a a few pointers to get you started:

*   git is actually a local versioning system. So you'll need git installed on your machine.
*   files are stored in a repo. Which is basically a directory. Create you first repo like so: `mkdir myfirstrepo; cd myfirstrepo; git init`
*   If your code is going to be on github just create a new repo on github, then run `git clone <repo url>` on you machine.
*   To commit new files or changed files into the repo run: git add .; git commit -m "commit comment" this will commit the changes to you local repo.
*   If you now want to push them to a remote like github run `git push`

See? git is easy. wel.. until you get into the non-easy stuff. But you rarely have to go into a lot of depth. A few more advanced things you want to know about:

*   [branches](https://learngitbranching.js.org/)
*   [tags](https://git-scm.com/book/en/v2/Git-Basics-Tagging)

Oh and if you screwed up a single file and want to unscrew it: `git checkout master </path/to/screwed/file>`

12 factor apps
--------------

Cloud native apps are designed quite different than traditional app. Understanding how they work is very important when you design and build platforms to support these apps.

For an app to be considered "cloud native" is to be a so called to [12 factors](https://12factor.net/) app. I won't go into all 12 factors in this post but I want to highlight a few a them and briefly discuss why it is important to know these if you're designing and operating a cloud native platform

**factor 4 - Backing services**: Treat backing services as attached resources.

Backing services like databases and message buses will be consumed as an attached resource. For platform ops this means that you'll have to provide a way to consume and attach these service. For example: if you're operating Cloud Foundry this means you'll have to deploy a service broker for each backing service the platform users will consume. It does not necessarily mean that you'll have to build and operate the backing service itself. Although in some cases you might be forced to doing so.

**factor 6 - Processes**: Execute the app as one or more stateless processes

The word I want to zoom in on here is "stateless". Processes are supposed to be stateless. Which means that if a process is killed you don't loose any data. Likewise, if a client connected to you app is load balanced to one instance but a moment later load balanced to another instance the client should not loose any data. For example: The contents of a shopping cart of a webshop should not be stored in server side memory but either client side (cookies) or in a shared backing service like redis.

From an operations point of view this means that you don't have to be as careful when doing maintenance or updates. The apps running on the platform a re designed to be killed without loosing data. As long as you have multiple instances running and you don't kill all the app instances you'll be fine.

This also has implications for your infra design. Specifically from a vSphere point of view it might mean that you won't need VMware HA.Availability is taken care of at the platform and application layer.

**factor 8 - Concurrency**: Scale out via the process model

Concurrency means that the app is designed in a way that you can run multiple instances of it simultaneously. By doing this you make sure you can always scale-out your app (as opposed to scaling up). It also makes sure you can have more than1 instance running for HA purposes.