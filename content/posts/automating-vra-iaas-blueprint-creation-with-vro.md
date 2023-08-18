---
title: 'Automating vRA IaaS Blueprint creation with vRO'
date: Thu, 21 May 2015 18:35:42 +0000
draft: false
tags: ['Blueprint', 'Blueprint creation', 'IaaS', 'Template', 'vRA', 'vRealize Automation', 'vRealize Automation (vCAC)', 'vRealize Orchestrator', 'vRealize Orchestrator (vCO)', 'vSphere']
---

In some situations it might be very convenient to be able to automatically create vRA (vCAC) Iaas blueprints. However, since the IaaS part of vRA uses an OData API doing so is not a trivial task. OData is basically just a representation of the IaaS database. So there is no logic in front of it and thus no way to tell the API: "Create a blueprint for this VM please". Previsously I talked a bit [about the vCAC API](http://automate-it.today/automating-vra-vcac-using-vco-part-1-split-brain/). In this post I'll get more practical and explain the steps involved when automating vRA IaaS blurptint creation with vRO.

Entities
--------

All objects in the IaaS OData API are called entities. It doesn't matter if it is a blueprint, a host or a build profile, everything is an entity. So in order to create a new blueprint we have to create a new entity. Which is done with the following line of code:```
var createdEntity = vCACEntityManager.createModelEntity(vCACServer.id, "ManagementModelEntities.svc", "VirtualMachineTemplates", parameters, links, null);
```the createModelEntiy method  needs a couple of input parameters. The first one is easy, it's the id of the vCACServer. This is the IaaS server object in vRO not the vCACCAFE host. You can just configure this server as an input of the workflow. The second parameter is always "ManagementModelEntities.svc" and the third one is basically the table name of where the entity should end up. In the case of a blueprint entity it's always "VirtualMachineTemplates" because that's the table where the blueprints live. Now for the tricky part: the parameter and links values.

Parameters
----------

The parameters for the entity are basically the properties of the object. The trick is figuring out which properties to use. There are a couple of methods. You could create a simple workflow that just dumps all the properties of an existing blueprint entity in a log. I prefer using [LINQpad](http://www.linqpad.net/) because it also shows you the relations between different tables. So which properties do we need for the blueprint entity and how do we put them into the parameters variable? The script below shows how to do this.```
var parameters = {
    "MachineType": 0,
    "VirtualMachineTemplateName": blueprintName,
    "InterfaceTypeId": "vSphere",
    "InterfaceId": interfaceId,
    "Master": false,
    "IsHidden": false,
    "IsPublished": false,
    "IsReconfigureAllowed": true,
    "ExpireDays": archiveDays,
    "ReconfigureForceShutdown": 0,
    "IsComponentOnly": false,
    "EnableStoragePolicy": false,
    "BlueprintType": 1,
    "DiskSize0GB": diskSizeGB,
    "VirtualMachineTemplateDescription": description,
    "IsGlobal": false,
    "ReconfigureExecutionSelector": 0,
    "RequiresApproval": false,
    "LeaseDays": leaseDays,
    "Enabled": true,
    "TenantID": "vsphere.local",
    "CPUCount": cpuCount,
    "MachinePrefix": "",
    "MaxVMsPerUser": maxVMs,
    "MemoryMB": memoryMB
}
```Obviously you might want to change the TenantID or get it from a variable.

Links
-----

An entity object is basically an entry in a database table. This table links to other tables. So when creating a new entity you need to define to which elemetns in other tables this entity links to. For blueprint entities there are 5 links to be set:

*   InterfaceType (vSphere in this case)
*   HostReservationPolicy (The reservation policy to deploy to)
*   ProvisioningGroup (A.ka Businiss Group)
*   WorkflowInfo (This is the kind of deployment you want. So for a BP that clones a template you need the Clone Workflow WorkflowInfo entity... still with me?)
*   GlobalProfiles (A.k.a Build Profiles)

This is how you put those links into a links object:```
var links = {
    "InterfaceType": \[ interfaceType \],
    "HostReservationPolicy": \[ reservationPolicy \],
    "ProvisioningGroup": \[ provisioningGroup \],
    "WorkflowInfo": \[ cloneWorkflow \],
    "GlobalProfiles": buildProfiles,
}
```As you can see each attribute in the object is an array. The content of the array are vCAC Entities.  Most arrays have one value only the globalProfiles has multiple values if you have more then one build profile selected. In the code above I the buildProfiles variable is defined somewhere else and it is already an array so I left out the \[ \].

Finding links entities
----------------------

I guess you're now wondering how to get the entities object for the links. You need to use the vCACEntitieManager to find these entities. Here is an example of how to find the WorkflowInfo entity for the clone workflow:```
//Get Clone Workflow entity
var vCacWorkflows = vCACEntityManager.readModelEntitiesByCustomFilter(hostid, modelName, "WorkflowInfoes", { WorkflowName: "CloneWorkflow" });

if (vCacWorkflows.length != 1) {
    throw new Error ("More than one or no CloneWorkflows found... shouldn't happen");
}

cloneWorkflow = vCacWorkflows\[0\];
//
```You can find the reservation policy by name in a very similar way:```
//Get Reservation policy entity by name
var vCacReservationPolicies = vCACEntityManager.readModelEntitiesByCustomFilter(hostid, modelName, "HostReservationPolicies", { name : reservationPolicyName })

if (vCacReservationPolicies.length != 1) {
    throw new Error ("More than one or no Reservation policies found... shouldn't happen");
}

reservationPolicy = vCacReservationPolicies\[0\];
```If you have an array with build profile names as in input you can find all the corresponding entities with this piece of code:```
//Get Build profile entities
var buildProfiles = buildProfileNames.map(function(name) {
    var vCacBuildProfiles = vCACEntityManager.readModelEntitiesByCustomFilter(hostid, modelName, "GlobalProfiles", { ProfileName : name });
    if (vCacBuildProfiles.length != 1) {
        throw new Error ("More than one or no build profile with entity name " + bpr + " is found. Please use unique names for build profiles!!");
    }
    return vCacBuildProfiles\[0\];
});
```If you want more of this in a workflow that's ready to run, see the link at the end of this post.

Properties
----------

So now we are able to create an entitie with the right parameters and links. After that's successfully done there is one more thing we need to do: configuring the custom properties on the blueprint. There are a couple required properties which start with a double underscore. Here is the list I used: \_\_buildprofile\_order (order in which the buildprofiles are applied) \_\_clonefromid (id of the virtualmachineTemplate entity we created) \_\_clonefrom (name of the vmtemplate entity we created) \_\_clonespec (name of the customization spec in vCenter) \_\_displayLocationToUser (false) \_\_menusecurity\_snapshotmanagement (false) VirtualMachine.Disk_N_.IsClone (true for each disk) VirtualMachine.Disk_N_.Size (the size of each disk) You can set these properties with the "Update Property to Blueprint" workflow that comes with the vCAC plugin. I used this script to generate the buildprofile order:```
//Build the buildProfile Order property value
var buildProfileOrder = "";
buildProfiles.forEach(function(bp) {
    buildProfileOrder += bp.getProperties().get("GlobalProfileID") + ",";
});

buildProfileOrder = buildProfileOrder.replace(/,$/g,""); //Remove last comma
```

 Just give me the workflow
--------------------------

Too much information? I uploaded the example workflows to [flowgrab](http://www.flowgrab.com). Find the download [here](https://flowgrab.com/project/view.xhtml?id=00167a59-eeca-4155-9f1d-f467f498153c). A word of warning: The workflows might assume you're using the vsphere.local tenant in vCAC. Should be easily fixable. If I've got some timeleft in the near future I might fix that myself. If you're using another tenant you should be able to easily makes this work for you anyways.