---
title: 'What is Cloud foundry?'
date: Thu, 06 Apr 2017 09:00:18 +0000
draft: false
tags: ['Cloud Foundry', 'Cloud Foundry']
---

I recently did a talk at the Dutch VMUG UserCon in which showed how it it is to deploy software by using Cloud foundry. I recently mentioned it in a blog [post](http://automate-it.today/right-tool-job/) as well. I also published a [whi![](https://plugins.cloudfoundry.org/images/cloud-foundry-logo.png)tepaper](http://www.automate-it.today/the-why-what-and-how-of-automation/) on automation in which I mention Cloud Foundry. But I realized that in the VMware community Cloud Foundry might no t be very well known and understood. Instead of telling you all to "just google it", in this post I'll try to answer the question "what is Cloud Foundry?"

So what is Cloud Foundry?
-------------------------

Let me start out with a quote from the Cloud foundry website: "Cloud Foundry is the industry standard cloud application platform that abstracts away infrastructure so you can focus on app innovation".  So Cloud Foundry is a platform that can run your cloud apps for you. What does that mean? It means Cloud Foundry (CF for short) is a collection of software that together forms a platform on top of your infrastructure. Developers can use the platform to deploy their software using simple command line tools or an API. The infrastructure and platform admins don't have have knowledge about the software being deployed nor do they have to be involved in the deployment of the software.

What does it do for you?
------------------------

The CF platform takes care of everything: A command line utility takes the source code of an app or even the compiled binary, uploads it to the platform. Then the platform will compile the code as needed. Then it will create a so called "droplet" which is conceptually similar to a Docker image. Everything that is needed to run the app is included in the droplet. So if you upload a war file it will automatically include a tomcat server, if you upload node.js code it wil include the node.js runtime. When the droplet is ready to run CF will start it in it's "elastic runtime". But it doesn't stop there. When it's deployed CF will monitor the app and when it crashes it will automatically restart it. It can monitor the load on the application and automatically scale it out or in as needed. Developers can also manually scale their application with a few keystrokes.

Opensource
----------

Cloud Foundry is completely opensource and has a very active community. There are commercial distributions out there but nothing stops you from running the opensource version. For free :). CF support multiple platforms: vSphere, vCloud, AWS, Google Cloud and even Bare metal through RackHD!

But why?
--------

So Why would you want to use Cloud Foundry?

1.  It makes the life of infrastructure operators (cloud Ops) really easy. When you're using VMware's vRealize Automation you're probably used to writing tons of scripts and workflows just to make automated software deployment work. When you use Cloud Foundry you don't have to do any of that.
2.  It makes the life of your developers very easy. They don't have to tell infra operators how to deploy their software. They can simply do it themselves by running "cf push".
3.  It enables a clear separation between DevOps and Cloud Ops. Developers can now deploy, run and operate their own app without bothering the infra team. The infra team on the other hand doesn't have to spent time understanding and deploying the developers apps. So with CF it's possible to have a Cloud Ops team that is responsible for the platform and the developer teams are not only responsible for developing their app but cat employ true DevOps. CF Gives them the tools to manage their own app in production.

Show me!
--------

Want to see it for yourself? You can use a public Cloud Foundry platform if you want to try to deploy some code on it: [run.pivotal.io](http://run.pivotal.io/) If you're more interested in the platform itself checkout the [Pivotal website](https://network.pivotal.io/). Pivotal provides a distribution of Cloud Foundry which makes it really easy to get going. Fair warning: you need a decent amount of disk and memory resources to run the whole platform.