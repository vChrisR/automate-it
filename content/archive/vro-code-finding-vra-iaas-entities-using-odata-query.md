---
title: 'vRO Code - Finding vRA IaaS entities using OData query'
date: Mon, 21 Mar 2016 11:00:30 +0000
draft: false
tags: ['entities', 'odata', 'vRA', 'vRealize Automation', 'vRealize Orchestrator', 'vRO']
---

![odata logo](http://www.odata.org/assets/OData-logo-e1393393068350.png) As I've explained in an [earlier blog](http://www.automate-it.today/automating-vra-vcac-using-vco-part-1-split-brain/ "Automating vRA (vCAC) using vRO – Split Brain") the vRA API is split into two parts: CAFE and Iaas. This post is about the latter which still contains a lot of the vRA entities. When working with those entities you regularly need to find them first. One of the methods for finding vRA Iaas entities is using an odata query.  The example I'll be using is finding all reservations for a specific business group. First thing you'll need is a script object or action with these inputs:

*   iaasHost (type: vCAC:vCACHost)
*   businessGroup (type: vCACCAFE:BusinessGroup)

The code below will find all reservations for the given Business Group```
var hostid = iaasHost.id;
var modelName = "ManagementModelEntities.svc";

reservations = vCACEntityManager.readModelEntitiesBySystemQuery(hostid, modelName, "HostReservations", "ProvisioningGroup/GroupID eq guid'"+ businessGroup.id + "'");
```The magic is in the readModelEntitiesBySystemQuery() method. The first two arguments are the iaas host id and the model name. The model name is basically always "ManagementModelEntities.svc". The third argument is where it gets interesting. This indicates the entity set name. Which is basically a database table. In this case we're searching in the "HostReservations" table. The fourth argument specifies the OData system query you want to execute. When you start using this it might be a good idea to read up a little on Odata. For example [here](http://www.odata.org/documentation/odata-version-2-0/uri-conventions/). specifically chapter 4 is interesting in this case. Back to our example: The thing we want to filter on is the ID of the business Group. A Business Group was previously called a provisioning group. So whenever you're looking for business groups in the IaaS Part of vRA it's called a provisioning group. In Odata speak it looks like this:ProvisioningGroup/GroupID . Now we need to match that ID to the ID of our business group.  searching for "equal to" is indicated by the eq . We can retrieve the business group ID from the businessGroup.id attribute. Now, this id is actually a guid. And when using a guid in in OData we need to tell OData that it's a guid: guid'<guid goes here' And that's all there is to it. One line of code gives you a filtered list of reservations. You an use the same method to look for network resrvations, reservation polices and so on. The OData query will differ slightly depending on what you want to find.