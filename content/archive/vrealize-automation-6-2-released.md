---
title: 'vRealize 6.2 releases'
date: Wed, 10 Dec 2014 16:06:35 +0000
draft: false
tags: ['6.2', 'code stream', 'vRA', 'vRealize', 'vRealize Automation', 'vRO']
---

VMware released version 6.2 of a lot of the vRealize familiy products, vRA is one of them. I'm not going to cover all new features here, you can find them in the [release notes](https://www.vmware.com/support/vcac/doc/vrealize-automation-62-release-notes.html). One feature I really like is this one:

*   Ability to edit custom properties for published applications in the service catalog.

This feature means we can now have user input for IaaS Custom properties on Application blueprint catalog items. I used to create some fairly complicated ASD workflows to work around this so this new feature makes life a lot easier for those customers using Application Services. A couple other features worth mentioning or those:

*   Schedule date and time for the reconfigure operation.
*   Supports configurable email templates.

VMware also released a new product: [vRealize Code Stream.](https://www.vmware.com/support/pubs/vrcs-pubs.html) This is VMwares take on a Continuous delivery tool. Its is connecting existing tools an repositories and provides the use the ability to create pipelines containing tasks. Interaction with external systems is handled by vRO (former vCO).