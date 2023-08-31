---
title: 'Running vRA 6.2 without Windows'
date: Thu, 18 Dec 2014 11:30:15 +0000
draft: false
tags: ['Configuration', 'LDAP', 'openLDAP', 'vRA', 'vRA 6.2', 'vRealize Automation', 'vRealize Automation (vCAC)']
---

After the release of vRA 6.2 I wanted to setup a small lab on my laptop. I really didn't feel like creating a windows machine and getting SQL and AD up and running. So I tried running vRA 6.2 without windows. If you leave windows out of the vRA installation you'll still end up with a useable vRA environment. I'll go into what's working and what's not in a minute. Let me explain how I build the environment first.

Building the Windows less vRA environment
-----------------------------------------

_Below is a brief description of the deployment of the vRA components I used. I left DNS out of this because I just used local hosts files. That works fine for a lab. Obviously I don't recommend any of this in a production scenario._ The first thing you'll need is an authentication source. In every installation I have seen this is Microsoft Active Directory. But vRA also supports open ldap so that's the obvious way around AD. I used the [turnkey openldap appliance](http://www.turnkeylinux.org/openldap). After download, just open it in VMware workstation, start it and answer the few questions the setup asks you. Then open a browser and go to the IP address for the ldap vm. After logging in your screen should look something like this: [![openldap](http://automate-it.today/wp-content/uploads/2014/12/openldap-300x166.png)](http://automate-it.today/wp-content/uploads/2014/12/openldap.png) To create a new user account navigate to the users ou and click "Create new entry here", then select "Generic: User account" and fill out the form. If you're really lazy you can just use the existing admin user as a user in vRA. I also tried using the LDAP groups in vRA but somehow that doesn't work for me so I'm just using a single user account from the LDAP. After the ldap is setup you need to deploy the vRA identity appliance. After it's deployed just start it, answer the questions and then go to the configuration page. configure the identity appliance as you would normally do, just don't configure any AD stuff.  Once the identity appliance is up and running you deploy and configure the vRA (vCAC) appliance. No special configuration here. When everything is installed and configured you should be able to connect to you vRA instance through the url: https://<vra appliance ip>/vcac. Login using the administrator@vsphere.local account. On the tenant tab click "Add Tenant". [![add-tenant-gen](http://automate-it.today/wp-content/uploads/2014/12/add-tenant-gen-300x122.png)](http://automate-it.today/wp-content/uploads/2014/12/add-tenant-gen.png) give the tenant a name and url and click "Submit and next". [![add-tenant-idstores](http://automate-it.today/wp-content/uploads/2014/12/add-tenant-idstores-300x106.png)](http://automate-it.today/wp-content/uploads/2014/12/add-tenant-idstores.png) Now click "Add Identity store" [![add-id-store](http://automate-it.today/wp-content/uploads/2014/12/add-id-store-300x119.png)](http://automate-it.today/wp-content/uploads/2014/12/add-id-store.png) Connect to your ldap as shown above. The IP address is the IP address of the open LDAP appliance we deployed earlier. After the Identity store is configured the next step is to add a Tenant admin. [![tenant-admins](http://automate-it.today/wp-content/uploads/2014/12/tenant-admins-300x55.png)](http://automate-it.today/wp-content/uploads/2014/12/tenant-admins.png) As you can see in the screenshot you'll be unable to add any infrastructure administrators. This is because all IaaS functionality in vRA is delivered through the IaaS windows components which we did not install. Now logout and go to the tenant url for the tenant you just created: https://<vra host>/vcac/org/<tenant url>. You should be able to login with the tenant administrator account you configured in the previous step.  

What doesn't work?
------------------

*   As I wrote above, LDAP groups don't seem to work. I don't know if this is a vRA issue or an issue with the turnkey openLDAP appliance. I'll need to investigate this further.
*   Any IaaS feature. This includes:
    *   Machine Blueprints
    *   Machine Reclemation
    *   Business Groups (although they are in the CAFE API but definitely not in the GUI)
    *   Fabric Groups
    *   Reservations / Reservation Policies
    *   Actually the whole "Infrastructure" tab is not available.

[![tabs](http://automate-it.today/wp-content/uploads/2014/12/tabs-300x29.png)](http://automate-it.today/wp-content/uploads/2014/12/tabs.png)

What works?
-----------

*   All ASD features
*   The catalog
*   Approval policies
*   Tenants
*   Branding

![adminscreen](http://automate-it.today/wp-content/uploads/2014/12/adminscreen-300x151.png)
-------------------------------------------------------------------------------------------

Conclusion
----------

Setting up vRA without any windows components is really easy. Mostly because you can skip all the IIS, vCAC manager, DEM and other stuff that comes with it. It also doesn't require a lot of resources. For me it runs fine a my laptop (i7, 16GB RAM, SSD). You are only able to use the ASD part of vRA but that is actually pretty powerfull. You'll just have to do without the IaaS stuff. That doesn't mean you can't deploy any virtual machines, you can actually deploy them using an ASD workflow. By the way: I have a feeling the performance of vRA is a lot better without the IaaS parts. But I have no numbers to support this.