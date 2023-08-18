---
title: 'How to Automate: vCO'
date: Thu, 22 May 2014 10:00:51 +0000
draft: false
tags: ['Automation', 'Infra Automation', 'vCenter Orchestrator', 'vRealize Orchestrator', 'vRealize Orchestrator (vCO)']
---

In my [previous](http://automate-it.today/automate-powercli/) “How to automate” blog I [wrote](http://automate-it.today/automate-powercli/) a little bit about PowerCLI. I concluded that piece with the statement that it might not be the right tool for the job if you're looking to automate IT operations processes. Today We'll take a quick glance at vCO to see if that is a better fit for the job. So what is vCO? vCO = vCenter Orchestrator. It is an automation tool which allows you to develop workflows by dragging and dropping pre-build parts or by creating your own objects using JavaScript.

The Upsides
-----------

The good news is that if you run vSphere you already have vCO. It actually gets installed with your vCenter server. But there is also a virtual appliance available or you can install it on a saperate windows machine. There are no additional license costs, it's included with vSphere. So I'll call that big upside. Another upside is the fact that vCO is a workflow tool. For some people this might be a downside though. Especially if you are very used to writing PowerCLI scripts. For those people there is actually a plug-in which enables you to run PowerCli scripts from vCO. The upside of vCO being a workflow tool however is that it is easy to keep things organized in a library. The workflows kind of document themselves because you can use meaningfull names for elements in the workflow, insert descriptions or even add “sticky notes” to workflows. It is also very easy to re-use actions and workflows you created before so the longer you work with vCO the faster you can develop new workflwos. There are also a lot of plug-ins available for vCO. This makes it really easy to interact with other systems like vCAC, Infoblox, ServiceNow and so on. The last upside is, in my opinion, the fact that all actions and scriptable object in vCO are actually JavaScript. Depending on previous experience JS is probably pretty easy to learn, the syntax looks like PHP and C and you'll be using very basic instructions most of the time.

The Downsides
-------------

The community around vCO is probably smaller then the one around PowerCLI. This could make finding solutions and pre-build scripts and workflows a bit harder. Of course there is this blog to help improve that :). Another downside might be the fact that everything is basically JavaScript. Wait.... what? Didn't I just say that's an upside? Again, that depends on your current skill set. It might be a bit hard to learn JS if you're a hardcore PowerCLI guy. There are no “|” and “$” in Javascript. Overall it seems that the learning curve for vCO is quite steep. This is probably caused by the fact that the interaction between different steps in the workflows is a little different than what you're used to when you just use scripts or any other programming language for that matter.

The Right Tool for the job?
---------------------------

In my opinion a workflow tool is the right tool for the job when you are automating processes. Because that's exactly what these tools were build for. When you are automating a vSphere or vCloud environment it makes perfect sense to learn and use vCO. On the other hand, if you're trying to just get a table of VMs with the number of vCPU and RAM size you might want to consider using PowerCLI. But if you're running that script every day it makes sense to call it from a workflow which send it to you by e-mail and then schedule the workflow for a daily run.