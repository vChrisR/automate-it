---
title: 'vCAC 6.1 released'
date: Tue, 09 Sep 2014 15:07:04 +0000
draft: false
tags: ['vRealize Automation']
---

Today VMware released vCAC version 6.1. You can read all about the new features in the [release notes.](https://www.vmware.com/support/vcac/doc/vcloud-automation-center-61-release-notes.html) [![vcac-splash](http://automate-it.today/wp-content/uploads/2014/07/vcac-splash-700x325-300x139.jpg)](http://automate-it.today/wp-content/uploads/2014/07/vcac-splash-700x325.jpg) A few features stand out immediately:

*   Migrating a vCloud Automation Center version 5.2.1 or 5.2.2 deployment to version 6.1
*   Upgrading from vCloud Automation Center 6.0 to 6.1

Really VMware? The new feature is that we can migrate or upgrade to the new version? I can't wait to see what will be in the next release... upgrading to 6.2 maybe? Then there is this one:

*   Improvements in Reporting

That was actually there in 5.2 then removed in 6.0 but the database stored procedures were still there. So now they put back the widget in the interface which actually shows the reporting. I love it..... But wait... there is more:

*   Application Services (Formerly VMware vCloud Application Director)

yep, a rename. Exciting. All ranting aside. There are some noteworthy new features. These ones for example:

*   NSX for vSphere Integration

But: "All NSX for vSphere and vCloud Networking and Security integration is now through vCenter Orchestrator (vCO) plug-in, including previously released and new functionality." So yeah... The free vCO plugin makes this all possible. Not the expensive vCAC product itself. Ok, ok I'll really stop ranting now. Because I really like the way this product is headed. VMware is leaving all the horrible .net stuff and moving all the executing onto vCO. And as you probably know I'm a huge vCO fan. So the relay good news is this:

*   Advanced Service Designer includes the following updates
    *   State-Aware resource actions that allow a service architect to define how resource actions such as Discard, Provision, or Change, affect the lifecycle of a resource.
    *   Any IaaS resource type can be extended with Advanced Service Designer resource actions.
    *   Feedback is provided to the user on the state of their requests.