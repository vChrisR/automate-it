---
title: 'How to Automate: PowerCLI'
date: Wed, 14 May 2014 10:15:04 +0000
draft: false
tags: ['Automation', 'Infra Automation', 'PowerCLI', 'PowerCLI', 'PowerShell', 'VMware', 'vSphere']
---

After writing my blog posts about the [why](http://www.automate-it.today/automate-2/) and the [what](http://www.automate-it.today/automate/) of automation I decided to write a bit about how to automate. This series of posts won't go into much technical detail but rather offer some pointers to help you choose the right tool for the job. When you think of automating tasks in a vSphere environment the first tool you probably think about it PowerCLI. For all less technical readers out there: VMware vSphere PowerCLI is a command line interface tool for automating vSphere tasks.

The Upsides
-----------

PowerCLI has the backing of a huge community. There are also plenty of books on the subject. On top of that there are a lot of command lets available from all kind of vendors so it integrates easily with a lot of products. Thanks to the huge community it is pretty easy to learn PowerCLI or to build your scripts by just recycling code that can be found online and in books. Another upside is that it is object oriented. This makes passing around information and monipulating the information pretty easy

The Downsides
-------------

The object oriented nature of PowerCLI could also be a downside depending on where you're coming from. If you are used to writing bash scripts then some of the PowerCLI syntax may seem familiar but handling objects can be confusing. Another disadvantage in my opinion is that you need a windows machine to run powershell. Me being a linux guy I really don't feel a need to run any windows machine. Especially in lab environments where you can get away with running vCenter VA instead of the windows installation. The last disadvantage is not specific for powerCLI but applies to scripts in general: Simple tasks are easy to automate but more complex tasks or long processes will result in huge scripts or piles of scripts which are hard to understand and very hard to troubleshoot and modify if needed.

The right tool for the job?
---------------------------

So is powerCLI the right tool for your job? Of course this depends on what the job is. I think it's the right tool if you're automating vSphere administration tasks. In that case it can make your life easier and save time. But if you try to automate whole IT Operations processes I highly doubt if this is the right tool you. More on that in a future blog post.