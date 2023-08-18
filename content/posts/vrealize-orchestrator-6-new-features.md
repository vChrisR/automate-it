---
title: 'vRealize Orchestrator 6.0 New Features'
date: Wed, 17 Dec 2014 14:53:40 +0000
draft: false
tags: ['default error handler', 'Erro', 'error handler', 'switch', 'vRealize Orchestrator', 'vRealize Orchestrator (vCO)', 'vRO', 'witch case']
---

With the release of vRealize Automation 6.2 VMware also released vRealize Orchestrator 6.0. In this post I'll explain the new features. You'll find vRO 6.0 on the vRA appliance. There is no stand-alone virtual appliance or installable version for vRO 6.0 at this point in time. Sources tell me this will be released with vRO version 6.0.1. vRA 6.2 is build to work with vRO 5.5.2 so if you want to use an external vRO server that's the version you'll be using. Unfortunately that means you'll miss out on these new features of vRO 6.0:

Switch
------

If you're familiar with javascript or any other scripting language you have probably used the switch case statement before. It selects a code block based in the value of a variable. Orchestrator already supported this inside scripts but now there is a switch element you can drag into a workflow. This way you can fork your workflow into different flows based on the value of a variable. This is what it looks like in a workflow schema:   [![switch-element](http://automate-it.today/wp-content/uploads/2014/12/switch-element-300x204.png)](http://automate-it.today/wp-content/uploads/2014/12/switch-element.png) The picture below shows the configuration of the Switch element: [![switch-config](http://automate-it.today/wp-content/uploads/2014/12/switch-config-300x143.png)](http://automate-it.today/wp-content/uploads/2014/12/switch-config.png) ou can add rules to the switch element by clicking in the green plus icon. For each rule you can select a variable (in this case “someVariable”), the matching operator (Equels, contains, match and Defined) and for some operators the value to match to. These switch rules work like firewall rules, only the first match is used. That's why you can move the rules up and down to change the order of the rules.

Default error handler
---------------------

Another new element is called “Default error handler” . When you drag it into the schema it looks like this: [![default-error-handler](http://automate-it.today/wp-content/uploads/2014/12/default-error-handler.png)](http://automate-it.today/wp-content/uploads/2014/12/default-error-handler.png) It is actually not connected to anything in you schema. It will be executed when an unhandled exception occurs in the workflow. Added to the workflow from the previous example it looks like this: [![error-handler](http://automate-it.today/wp-content/uploads/2014/12/error-handler1-300x266.png)](http://automate-it.today/wp-content/uploads/2014/12/error-handler1.png) As you can see in the schema the default error handler allows you to run certain actions whenever an error occurs in the workflow that is not handled by any other error handler. So this gets rid of all the red lines to one error handler. Neat!