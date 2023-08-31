---
title: 'vRealize Orchestrator 7 Release'
date: Fri, 18 Dec 2015 14:49:38 +0000
draft: false
tags: ['ORchestrator', 'vRealize Orchestrator', 'vRO', 'vRO7']
---

As you're probably aware by now VMware [released](http://pubs.vmware.com/Release_Notes/en/vra/vrealize-automation-70-release-notes.html?src=vmw_so_vex_mande_12 "vRA7") vRealize Automation 7 today. I already discussed vRA 7 before and there are a lot of blogs out there writing about it. What I didn't blog about before is that with vRA7 VMware also [released](http://pubs.vmware.com/Release_Notes/en/orchestrator/vrealize-orchestrator-70-release-notes.html "vRO7") vRealize Orchestrator 7.  Since vRO is the magic sauce that makes vRA work anyways I'm far more interested in the vRO release. So I quickly downloaded the ova (yep, no windows installer anymore), imported the ova into VMware workstation and connected to it. All in under 7 minutes :). I read on another blog that there now is an HTML 5 client. But nope.... only the control center is HTML5.  The client used to develop workflows is still the same Java contraption. But it supports reconnecting now. Which is very nice because that means you don't loose any work when a vRO server stops responding. Other than that I couldn't find any improvements in the client. Still no decent IDE like environment to write your scripts in, no improvements to the api explorer as far as I can see. I'll digg deeper in the coming week and let you know what I find. Other new features:

*   Centralized Server administration.
*   Easy cluster configuration.
*   Easy workflow troubleshooting and runtime metrics.
*   Enhanced log monitoring, log persistency and added ability to export logs for a particular workflow run.
*   Direct correlation of system properties and workflow performance through the embedded JMX console integration.
*   Significant Orchestrator client improvements, including a workflow tagging UI, client reconnect options and enhanced search capabilities.