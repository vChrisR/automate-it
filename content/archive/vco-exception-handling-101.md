---
title: 'vCO exception handling 101'
date: Thu, 11 Sep 2014 10:28:50 +0000
draft: false
tags: ['exception', 'Exception handling', 'handling', 'red line', 'vRealize Orchestrator', 'vRealize Orchestrator (vCO)']
---

I come across quite a few workflow that look like the screenshot below. Since I don't see the point in doing it like this I thought I'd do a quick vCO exception handling 101. [![exception](http://automate-it.today/wp-content/uploads/2014/09/exception-300x137.jpg)](http://automate-it.today/wp-content/uploads/2014/09/exception.jpg) In the screenshot above you'll notice the red lines pointing to the red exclamation mark. The red lines are followed when an unhandled exception occurs in the the action, workflow or script. But just pointing the exception to an exception exit of the workflow makes no sense at all! (until somebody convices me otherwise...). It just makes the workflow look cluttered in my opinion. Believe it or not, the workflow below will handle any exception in exactly the same way as the workflow above. [![no-exception](http://automate-it.today/wp-content/uploads/2014/09/no-exception-300x138.jpg)](http://automate-it.today/wp-content/uploads/2014/09/no-exception.jpg) See? That's a lot les cluttered. And the "corner" in the workflow isn't even necessary, it could have just been one straight line of workflow items. If there is an exception the workflow will fail at the point of the error which makes troubleshooting easy. So when do you use the red lines? Whenever you want to handle the error in some way. So don't point a red line to a red exclamation mark but to a part of the workflow that handles the error. The example below shows a retry loop. I use this quite often for workflows that connect to other systems. You know, connections fail sometimes for whatever reason so you better retry a few time and sleep a bit in between. If you run out of retries then you throw the exception. [![retry](http://automate-it.today/wp-content/uploads/2014/09/retry-300x181.png)](http://automate-it.today/wp-content/uploads/2014/09/retry.png) By the way: Make sure you put the sleep after the decision, not in front of it. Why wait and then discover you are out of retires anyways? That's it for now. More on exceptions later.