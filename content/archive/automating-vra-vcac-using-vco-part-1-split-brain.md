---
title: 'Automating vRA (vCAC) using vRO - Split Brain'
date: Mon, 23 Feb 2015 10:59:47 +0000
draft: false
tags: ['API', 'Automation', 'vRA', 'vRealize Automation', 'vRealize Automation (vCAC)', 'vRealize Orchestrator', 'vRealize Orchestrator (vCO)', 'vRO']
---

Recently I have done some work on automating vRA (vCAC) using vRO (vCO). This meant I had to dive into the vCAC APIs. The bad news is that this felt like diving into a pool of dark muddy water. The good news is that I'm still alive, my headache is gone and I'll try to capture some of the things I learned on this blog.

Split brain
-----------

In this post I'll start out with an introduction to the vCAC APIs. Yes, plural. Not just one API. vCAC ahem... VRA is actually not just one product, it's two products which are loosely coupled and sold as one. The first product is the vRA Appliance also known as CAFE. This is a new product that was introduced with vCAC verion 6.0. It is developed in Java (springsource), runs on linux, uses postgres as a data persistence layer, seems to use a micro services architecture , supports multi-tenancy and provides a REST API. But there also is the old product that was originally developed at CreditSuise, spun off as DynamicOps and then acquired by VMware. It was sold as vCAC 5.x, is developed in .net, uses an MS SQL back-end, runs .net workflows, has no notion of multi-tenancy and provides an OData API. This part is usually called the Iaas Part. The two products are also reflected in two separate vCO ahem... vRO Plugins. Although you download and install just one package there are really two plugins installed. One is called VCAC and has the description “vCloud Automation Center Infrastructure Administration plug-in for vCenter Orchestrator” the other one is called CAFE and is described as “vCloud Automation Center plug-in for vCenter Orchestrator”. Confusing. Right? So let's clear things up: **CAFE** is the virtual appliance. All new features are developed in CAFE. So anything that was added since 6.0 runs on the appliance and can be used from the REST API. On top of that some functionality was moved to the appliance. Functionality running in CAFE in version 6.1 includes:

*   Business Groups and Tenants
*   Advanced Service Designer
*   The Catalog
*   Resource Actions
*   Approval policies
*   Notifications

So if you want to automate anything regarding any of these features you'll need the CAFE plugin which talks to the REST API running on the virtual appliance. **IaaS** is the name of everything that's not on the appliance. It is the reason you need a windows server to run vRA, not just the appliance. This windows server (or multiple servers) runs the old DynamicOps Software with some modifications. Features provided by this part of vRA include:

*   Virtual Machine Blueprints
*   Machine Prefixes
*   Provisioning Groups (Maps to Business Groups in CAFE, GUI only knows Business Groups in the current version)
*   Reservations
*   VirtualMachines (vCAC VM objects which map to vSphere/vCloud VMs or even physical machines)

If you want to automate any of the above you'll need to use the vCAC plugin or the Odata API. If you're note familiar with Odata APIs there is something you should know: It's not an actual API. It's just a representation of the database. There is no application logic behind it, just database constraints. This means that creating new things (called entities) is rather difficult. You have to figure out all the links between different database tables yourself. I'll try to dive into this deeper in another blog post. There another peculiarity I want to point out: there is no multi-tenency in the IaaS part. This means that a lot of items from the IaaS part (for example: machine prefixes) are shown to all tenants!

Touchpoints
-----------

The fact that vRA basically has a split brain provides some challenges when automation things in vRA. For Example: You'll have to create a blueprint in the IaaS part but when you want to publish it you have to create a catalog item in the CAFE part of the product. Which brings me to the last part of this post. As I said before the two product are loosely coupled. The actual touchpoints are not documented. Or at least I couldn't find anything. But after spending a lot of hour trying to find out how to autmate the publishing of blueprint I found these touchpoints between both APIs:

*   The Business Group ID in CAFE is identical to the Provisioning Group ID in IaaS. If you create a Business Group in the REST API then vRA also creates the ProvisioningGroup in IaaS for you.
*   The catalog actually consists of three catalogs. More on this later. One of the catalogs is the provider catalog. Each provider manages its own provider catalog. IaaS is on of the providers. Somehow CAFE knows where to find certain provides IDs. Not sure where to find or set that mapping.
*   Every Catalog Item has a providerBinding attribute. This contains the bindingId. This binding ID is the blueprint ID (virtualMachineTemplateID) from the IaaS Part. This is how vRA figures out which blueprint to deploy when you request a catalog Item.
*   A Resource Operation has bindingId which maps the CAFE action to the IaaS action (like powerOn a VM for example)