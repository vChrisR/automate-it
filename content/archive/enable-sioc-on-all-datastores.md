---
title: 'Enable SIOC on all datastores'
date: Tue, 07 Apr 2015 09:30:32 +0000
draft: false
tags: ['Datastore', 'Infra Automation', 'iorm', 'SIOC', 'storage IO Control', 'vRealize Orchestrator']
---

Recently I had to [enable Storage I/O Control](http://www.vmware.com/products/vsphere/features/storage-io-control) on a lot of datastores. I did find [this](http://www.vnugglets.com/2011/01/enable-sioc-on-all-datastores.html) powershell script to do it. But being a vRO junky I obviously wanted to use vRO to do this so I quickly created a workflow  to enable SIOC on all datastores. Actually not on all datastores, the workflow asks for a filter string. All datastores which name contain that string will be skipped. Useful if you don't want SIOC enabled on, for example, vplex luns like I did. I released the workflow on [Flowgrab](http://www.flowgrab.com). You can check it out [here](https://flowgrab.com/project/view.xhtml?id=11f20119-521a-4e9e-b6ea-b69186dd7987). I tested this with vRO 5.5.2.0 and vSphere 5.5. If you don't want to install the package. Below is the actual code from the action I created.```
if (! datastore) {
    throw new Error ("Datastore cannot be null");
}

//Find storageResourceManager for this datastore
var sdkConnection = datastore.sdkConnection;
var storageRM = sdkConnection.storageResourceManager;


//Create IORM spec
var iormSpec = new VcStorageIORMConfigSpec();
iormSpec.enabled = true;

//Start the reconfig task
return storageRM.configureDatastoreIORM\_Task(datastore, iormSpec);
```I also use a filter in my workflow which looks like this:```
filteredDatastores = allDatastores
    .filter(function(ds) {
        return ds.name.indexOf(filter) == -1;
    });
    
filteredDatastores.forEach(function(ds) { System.log(ds.name) });
```Now it's up to you to put it together. Or [download](https://flowgrab.com/project/view.xhtml?id=11f20119-521a-4e9e-b6ea-b69186dd7987) my package :)