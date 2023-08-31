---
title: 'VMworld news: vRealize Automation 7'
date: Tue, 13 Oct 2015 11:54:17 +0000
draft: false
tags: ['VMworld', 'VMworld Europe', 'VMworld Europe 2015', 'VMworld2015', 'vRA', 'vRA7', 'vRealize', 'vRealize Automation', 'vRealize Automation']
---

I'm currently at VMworld Barcelona where this morning VMware announced the new version of vRA. It's going to be called vRA7. As far as I know it's still not GA (general availability)  but the Beta program is in full swing. This morning I attended a a vExpert deepdive session hosted by @virtualJad. Here is an overview of some of the new features:

*   Simplified architecture You no longer need 24 machines for an enterprise deployment.
*   Simplified installation: From download to up and running in 20 minutes. All wizard driven so anybody can run a vRA PoC without assistance from PSO or other consultants.
*   One converged blueprint. No difference between IaaS and ASD (which is now called XaaS by the way) and application blueprint
*   Blueprint designer is now a beautiful canvas which allows for visio style drag and drop design.
*   Nested blueprints: You can use other blueprints in your blueprint (nice!)
*   Eventbroker: instead of having a few workflow stub we can now create policies that define when to kickoff a workflow. There are 60 different lifecycle events to which you can attach workflows. And each event has multiple stages (pre, event, after).

This event broker makes the product so much more extensible than what it currently is. The possibilities are almost endless. The other nice thing about this is that it is policy driven and defined by the vRA admin. So extensibility is now no longer part of the workflows. This means you can give the workflow designer to an application architect while still making sure that important IPAM or CMDB workflow is kicked off with each deployment. The application architect can consume XaaS workflows to extend his own blueprint. In summary: really cool stuff, you'll be reading lots more about it here in the coming months. I know, I know, I haven't blogged in a while but I promise you'll see some good vRA 7 stuff here on this blog!