---
title: 'vRO code - Finding all items in a Business Group'
date: Wed, 16 Mar 2016 11:00:55 +0000
draft: false
tags: ['cafe', 'fitler', 'query', 'subtenant', 'vRA', 'vRealize Automation', 'vRealize Orchestrator', 'vRO']
---

I recently needed to figure out how many items are deployed for a specific vRA Business Group. Items includes virtual machines but also anything else a user can deploy. That seemed an easy task but finding all items in a business group turned out to be a bit harder than expected. So to save others the trouble of figuring out how this works I'll share my code here. You can copy-paste the code below directly to a vRO action.```
var cafeHost = vCACCAFEEntitiesFinder.getHostForEntity(businessGroup);

service = cafeHost.createCatalogClient().getCatalogConsumerResourceService();

var filter = new Array();
filter\[0\] = vCACCAFEFilterParam.substringOf("organization/subTenant/id", vCACCAFEFilterParam.string(businessGroup.id));
var query = vCACCAFEOdataQuery.query().addFilter(filter);
var odataRequest = new vCACCAFEPageOdataRequest(1, 10000, query);
resources = service.getResourcesList(odataRequest);

return resources;
```tip: whenever the API explorer tells you you need to pass a variable of type org.springframework.data.domain.PAgeable you can use vCACCAFEPageOdataRequest. You can stop reading here if you want but I need to rant a little :). When I tried to figure out how to filter what the vRA API returns I first turned to the vRA 6.x REST API Documentation. That did contain some pointers but wasn't much help. Then I found [Sergio Sanchez' post](https://communities.vmware.com/blogs/sergioatvmware/2014/06/25/how-to-filter-vcac-catalog-resources-by-owner-with-the-vcac-plug-in-for-vco) on the VMware communities forum. That example worked for getting items for a certain user so that was a good start. To filter based on username you use the filter: "owners/ref". I compared that to the API docs and it all made sense. The owners object exists in the return JSON and it has an attribute called "ref". So all I had to do was using the "organization" object and filter on the "subtenantRef" attribute? Right? Wrong! Somehow there is no relation to the object and attribute names in the resource JSON and the filter criteria. But luckily one of my colleagues pointed me in the right direction. Turns out there are a few examples in the vRA 7 API documentation that described what I needed. But that's still just an example. How to figure out the filter criteria yourself still remains a mistery.