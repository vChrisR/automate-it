---
title: 'Kubernetes opinions'
date: Thu, 06 Feb 2020 10:50:24 +0000
draft: false
tags: ['BOSH', 'cloudfoundry', 'ITQ', 'Kubernetes', 'Kubernetes', 'opinionated']
---

I have recently been working with and learning about kubernetes quite a lot.  I have also spent a lot of time with Cloudfoundry and BOSH over the last few years so I can't help myself and compare these different products. Let me start of by saying that BOSH, Kubernetes and Cloudfoundry are complementing each other in terms of functionality. I'll probably write a separate post on that. I want to use this post to go into they way these products are build and used. Namely opinionated vs non-opinionated.

Opinionated
-----------

Both BOSH and Cloudfoundry are both heavily opinionated products/platforms. This means that if you want to run an application on CF you'll have to adhere to those opinions. More specifically: you'll need to built a 12 factor app. If you deploy Cloudfoundry you'll always use the same log aggregator and until recently you always used the same container runtime and scheduler. Also, the authentication is always done by the same product and the permission system is built-in and always the same. This means that it doesn't matter on which CF platform you run your app, it will always look very similar. Same goes for BOSH. If you want to deploy software using BOSH you need to put it into a BOSH release. Which is a predefined way of deploying software. BOSH has opinions on where to put your scripts, where to put your binaries and config files and how to start and monitor the application. If you want stuff done differently then you'll have to look for another product. The CF ecosystem also has an opinion on how you operate the platform. The role of operator and user is always clearly defined.

Non-Opinionated
---------------

I see Kubernetes as a non-opinionated solution. Most of the components are plug-able, you are forced to bring your own container runtime and there the networking solution if even worse. Also, You can run whatever you like on top of k8s as long as it comes as an OCI image. But the latter is hardly an opinion since an image can literally contain anything. There is also no clear distinction between operators and users of the software either. It seems like every project I do that involves k8s ends up in very long discussion about where the platform operators responsibilities stops and where the platform user responsibilities start. The tooling doesn't make this any easier. For example: You use the same cli tool to both update an app and remove a worker node and there are barely any usable predefined roles in the system.

You get an opinion and you get an opinion and....
-------------------------------------------------

The problem with the fact that a platform in and of itself does not have an opinion is that people using the platform will start making up their own opinions. Then they fight each other about it. Here are a few examples:

*   Kubernetes is intended to run [12 factor workloads](https://blog.octo.com/en/the-twelve-factors-kubernetes/), don't run stateful applications on it
*   Kubernetes on it's own isn't friendly to run 12 factor micro-services. You need [extra magic](https://ahmet.im/blog/knative-better-kubernetes-networking/) to make that happen
*   It's fine to run databases on k8s
*   Managing state on k8s is horrible
*   Each dev team should have its own k8s cluster
*   Use namespaces, PSPs and something like OPA to build a secure multi-tenant cluster
*   You won't need backups, just redeploy the cluster, r-run the deploy pipelines and you're fine
*   Here is a [backup tool](https://github.com/vmware-tanzu/velero) bacuse you need backups
*   What's you opinion? Leave a comment or hit me up on twitter.

My Opinion
----------

So all this has upsides and downsides. In my opinion :). It is great that everybody can build a platform to their liking and use it as they see fit. It is also great that there is a huge ecosystem around this (Keeps a lot of people in a job). At the same time it means you have to build a platform out of kubernetes, just deploying k8s itself is not enough. So now you have to form your own opinion, or even worse your (enterprise) organization has to form its opinion. Then you have to build and maintain the platform. I'm sure a lot of readers will now respond with "you don't run your own k8s, you just use one of the cloud offerings". And that is certainly a good point but even then you'll end up in discussions around what you should run on k8s, who should have access and so on. In the end I prefer a platform that is clearly opinionated. It just saves time and makes life easier. You'd think that this implies I do not like Kubernetes and you might be right. I think Kubernetes is definitely cool tech and it may have a bright future. Just not as an application platform. Having said that, the application platform might run on top of some non-opinionated infrastructure. But that's for another post.