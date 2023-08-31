---
title: 'VMware Cloud Native Master Specialist Exam'
date: Wed, 05 Feb 2020 10:50:55 +0000
draft: false
tags: ['Certification', 'ITQ', 'Kubernetes', 'Kubernetes', 'VMware', 'VMware Cloud Native Master Specialist']
---

I recently passed the VMware Cloud Native Master Specialist Exam. The what? yeah.... Let me explain. By passing this exam you'll earn a badge called "Cloud Native Master Specialist". And according to VMware:

> Earning this badge will certify that the successful candidate has the knowledge and skills necessary to architect a Kubernetes-based platform supported by complimentary technologies from the Cloud Native ecosystem for continuous delivery of applications.

So you'll get a badge, not a "certification". Not sure what the difference is though... The most important reason why I needed to pass the exam is that VMware requires a certain number of people with said badge in a company seeking to earn the Cloud Native Master Service Competency. Read more about MSC [here](https://www.vmware.com/master-services.html). Since there is not a log of information about the exam out there I'll give a quick overview of it here.

Exam Pre-reqs
-------------

The only pre-req for this exam is the CKA certification. I posted about the CKA exam [earlier](http://automate-it.today/cka-exam-tips/). You don't need a VCP certification as most other VMware exams do. Nor do you need any formal training.

Exam Format
-----------

The exam is a multiple choice exam. I think I got 67 Questions, not sure if that's always the same. You have 100 minutes to answer all of them.

Knowledge needed
----------------

All exam topics are listed in [this blueprint pdf](https://www.vmware.com/content/dam/digitalmarketing/vmware/en/pdf/certification/vmw-ms-cloud-native-2019.pdf). To summarize you'll need to the following skills to pass the exam:

*   Be comfortable with k8s. Since you passed the CKA exam before doing this one you'll probably be fine. It won't hurt to also look into the CKAD training materials or even pass the CKAD exam. In summary make sure you know about: deployments, pods, replicaSets, priorityClasses, networkPoicies, affinity, RBAC, auditPolicies and all that good stuff.
*   As mentioned in the blueprint you need some knowledge about a few other products as well:
    *   [Valero](https://github.com/vmware-tanzu/velero). This is a backup solution for k8s. Watching [this video](https://www.youtube.com/watch?v=8skHGzUBZ-Q) is probably sufficient to pass the exam. Playing around with it yourself will definitely help memorize details better.
    *   [Sonobouy](https://github.com/vmware-tanzu/sonobuoy). This is a tool to run conformance tests on a k8s cluster. Watch [this video](https://www.youtube.com/watch?v=8QK-Hg2yUd4) for more details. That will probably give you enough info to pass the exam. But again, I recommend trying it out for yourself if you have never used it.
    *   [OpenPolicyAgent](https://www.openpolicyagent.org/). OPA gives you policy based control over eehhh... basically everything if you want to. For the exam you need to know how it can be used with k8s.
*   Some very basic AWS knowledge can come in handy. And I mean really basic: If you know what regions, AZs and VPC are you'll probably be fine.
*   You don't need any "Traditional" VMware knowledge at the moment. I say "traditional" because 2 out of the 3 tools mentioned above are now part of VMwares portfolio as well.
*   Know about flannel, its limitations and alternatives.
*   Take some time to learn about podSecurityPolicies if you haven't done so already. Start with this [video.](https://www.youtube.com/watch?v=-t0JUMSzmV0&feature=emb_title)
*   Docker build [best practices](https://cloud.google.com/blog/products/gcp/7-best-practices-for-building-containers)