---
title: 'vRO Code - Finding VirtualMachines by Custom property'
date: Fri, 06 Jan 2017 11:11:24 +0000
draft: false
tags: ['Custom properties', 'Finding VirtualMachines by Custom property', 'Infra Automation', 'Knowledge Base', 'vRA', 'vRA API', 'vRealize Automation', 'vRealize Orchestrator', 'vRO']
---

For the current project I'm involved in, I was asked to deliver a list of vRA deployed machines that have a Production status. At first I have been writing a short piece of code that obtained all vRA managed machines and for each machine gathered the customer properties. Creating this workflow actually took less time than the execution itself as the environment has about 4200 managed objects. Next to the fact that this is time consuming to wait for, it will also generate a lot of load on the vRO service and the vRA IaaS API. The developer in me felt like improving this and move the functionality to the vRA IaaS API, the API nevertheless has the custom properties linked to the virtual machine entity object. Eventually, after some research on ODATA queries and how to query for properties within linked entities, I was able to write the following ODATA filter:```
var filter = "VirtualMachineProperties/any(p: p/PropertyName eq 'Extensibility.Properties.businessUse' and p/PropertyValue eq 'Development')";
```Putting the filter and the vCAC IaaS plugin logic together will form the following script that can be used in either a workflow or an action:```
// Define Property details
var propertyName = "Extensibility.Properties.businessUse";
var propertyValue = "Production";

// Define the filter that is used for search for the entities
var filter = "VirtualMachineProperties/any(p: p/PropertyName eq '" + propertyName + "' and p/PropertyValue eq '" + propertyValue + "')";

var machines = vCACEntityManager.readModelEntitiesBySystemQuery(
    host.id,                        //vCAC IaaS host id
    "ManagementModelEntities.svc",  //model
    "VirtualMachines",              //Entity Type in IaaS API
    filter                          //filter
    )

machines.forEach(function(machine) {
    System.log(machine.getProperty("VirtualMachineName"));
});
```To elaborate  a little bit on the code snippet above:

*   First the property and it's value are being specified
*   The second step is to setup the filter with the property and value
*   The third step is to actually perform the call to vRA IaaS to return an array of vCAC:Entity based on the filter.
*   The last step in the code is to System.log() the names of the VirtualMachines that match the query.

When necessary to have vCAC:VirtualMachine objects instead of vCAC:entity objects change the last part of the code to:```
var vCACVirtualMachines = machines.map(function(machine) {
    return machine.getInventoryObject();
});
``` 

### **Conclusion**

Gathering virtualmachines based on specific properties can be a hassle using ODATA queries as in some cases it is not completely clear on how to structure the query. Eventually when the query is ready and working it shows to be much faster than creating a script to "hammer" the API for data.  The two screenshots below show the actually difference between the initial code and the improved code. The first screenshot is the original code, it errors out after 30 minutes of API calls. The second screenshot is a capture of the improved code, it runs for only 1 second to return the list of VirtualMachines matching the filter. \[caption id="attachment\_818" align="aligncenter" width="579"\][![log get virtual machines by property and value error](http://automate-it.today/wp-content/uploads/2017/01/Log-get-virtual-machines-by-property-and-value-error.png)](http://automate-it.today/wp-content/uploads/2017/01/Log-get-virtual-machines-by-property-and-value-error.png) First attempt ended up in an error returned by the vRA IaaS API after 30 minutes of performing API calls.\[/caption\]   \[caption id="attachment\_819" align="aligncenter" width="484"\][![log get virtual machines by property and value](http://automate-it.today/wp-content/uploads/2017/01/Log-get-virtual-machines-by-property-and-value.png)](http://automate-it.today/wp-content/uploads/2017/01/Log-get-virtual-machines-by-property-and-value.png) Second attempt with improved code. The runtime of the script is now only a matter of seconds.\[/caption\]