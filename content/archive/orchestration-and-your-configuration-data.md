---
title: 'Orchestration and your configuration data'
date: Mon, 14 Mar 2016 11:00:33 +0000
draft: false
tags: ['Business Logic', 'Configuration', 'vRA', 'vRealize Orchestrator', 'vRO']
---

As this is my first blog post at automate-it.today I would like to start off with something less-technical and have a monologue about the Orchestration and your configuration data. This first post will be the first in a series of four; the following post will be more technical and describing the possibilities, technical implementation and their pro's and con's. First off, what is configuration/automation data? In short: the data that supports your  automation in terms of decision making and logic. A good example of this data would be the network configuration of virtual machines: Most enterprises segment their networks into functional (security) zones. Depending on the zone the virtual machine is connected to (in some cases multiple) networks. This would not be a hard scenario to automate right? But what if you would add a level of complexity in this model? Oh wait.. Make that two. What if you would have to deal with sub-zones and multiple business units? Your decision logic would probably become a lot harder as you need to build something completely modular that fits the whole enterprise. The example given is a conservative one as we see a growth in network virtualization and micro-segmentation, but the basic idea is that you will need something to easily store your configuration data in a relational fashion. As a VMware PSO consultant I have worked on multiple automation/orchestration projects in the past and for some reason it's always a struggle to store your configuration/automation data. The main argument being that there is no room to invest in a new tool or there is a lack of resources (in any form) for a SQL database. Another argument is that the configuration data and its holder should be maintained, who is going to take the operational responsibilities? Lets try to dismantle the arguments above by using our example and make some worst case scenario assumptions on what the current implementation would look like. [![Configuration Data Network](http://automate-it.today/wp-content/uploads/2016/03/Configuration-data.jpg)](http://automate-it.today/wp-content/uploads/2016/03/Configuration-data.jpg)  

*   Each business unit has three functional (security) zones
*   Each functional (security) zone has three sub-zones
*   Each sub-zone has three networks
*   The configuration data is stored in objects in a workflow
*   Each object represents one network

**An example of the assumption above in code**```
var availableNetworks = new Array();

var network1 = {
		businessUnit 	: "Business Unit A",
		functionalZone 	: "Functional Zone 1",
		subZone 		: "Sub-Zone 1",
		networkName 	: "Network 1"
}

availableNetworks.push(network1);

var network2 = {
		businessUnit 	: "Business Unit A",
		functionalZone 	: "Functional Zone 1",
		subZone 		: "Sub-Zone 1",
		networkName 	: "Network 2"
}

availableNetworks.push(network2);

var network3 = {
		businessUnit 	: "Business Unit A",
		functionalZone 	: "Functional Zone 1",
		subZone 		: "Sub-Zone 1",
		networkName 	: "Network 3"
}

availableNetworks.push(network3);

```And to obtain the correct network objects we would run a script similar to this:```
var selectedNetworks = new Array();

selectedNetworks = availableNetworks.filter(function (networkConfig) {
	if (networkConfig.businessUnit == "Business Unit A" && networkConfig.functionalZone == "Functional Zone 1" && networkConfig.subZone == "Sub-Zone 1") {
		return networkConfig; 
	}
});

```A small calculation makes that we have 27 scripted network objects for each business unit. that means a total of 216 lines for the network selection only and with this we have nothing fancy or dynamic. The operational procedure for adding a new business unit would mean that additional network objects should be added to the same script. If the operations engineer would make one mistake (think of an additional comma in the code) impact on the production environment cannot be ruled out.

#### **So what are your possibilities?**

Depending on the use-case, the size of your implementation, the amount and complexity of the data there are a few options available. These options will be discussed in a more technical manner is the following posts in this series: Orchestrator and your configuration data part 2 - SQL database Orchestrator and your configuration data part 3 - CouchDB or similar Orchestrator and your configuration data part 4 - Configuration elements and conclusion **Happy reading!**