---
title: 'Automated directory synchronization of the vRA Identity Manager'
date: Wed, 01 Mar 2017 10:02:49 +0000
draft: false
tags: ['Infra Automation', 'vRealize Automation', 'vRealize Orchestrator']
---

**Disclaimer: The API documentation has not yet been released, therefor I would like to notice that this is currently an unsupported method of triggering a directory sync.** During a recent project the customer requested the functionality to create a new business group with just one click. This should be a function to onboard new teams into the vRA environment, including the creation of Reservations and Active Directory groups. In vRA 6 this would not have been a problem at all, but starting at vRA 7 the Identity Manager was introduced. The Identity Manager, in short the connection from vRA to Active Directory (AD), synchronizes AD content on a specific schedule. This means that while specifying the different AD groups in the new Business Group, these will not be visible immediately but after a synchronization. As the customer stated, it should be an automated process, a click on the button. Waiting for the synchronization to take place is not an option.. We are automating this, right?! Therefor my colleague Marco van Baggum ([#vMBaggum](https://twitter.com/vMBaggum) [blog](https://www.vmbaggum.nl/)) came up with the idea to automate the synchronization of the identity manager. In a shady corner Marco found the necessary API calls and off we go! The first step is to create the a new HTTP-REST endpoint in vRO. Run the workflow “Add a REST host” located at Library / HTTP-REST / Configuration and use the following settings:

Name

vRA

URL

[https://<vRA](https://%3cvRA) FQDN>/ e.g. [https://itqlab-vra.itqlab.local/](https://itqlab-vra.itqlab.local/)

Authentication

NONE

\* The other settings are dependent on how vRA is set-up and how vRO connects to it. A new endpoint in the inventory should pop up at the HTTP-REST plugin. Now right click this endpoint and run the workflow to add the additional REST operations to it.

Name

Get Directories

Method

GET

URL template

/SAAS/t/{tenant}/jersey/manager/api/connectormanagement/directoryconfigs

 

Name

Get Directory Sync Executions

Method

GET

URL template

/SAAS/jersey/manager/api/connectormanagement/directoryconfigs/{directoryId}/syncexecutions

 

Name

Invoke Directory Sync

Method

POST

Content-type

application/json

URL Template

/SAAS/jersey/manager/api/connectormanagement/directoryconfigs/{directoryId}/syncprofile/sync

 

Name

Login

Method

POST

Content-type

application/json

URL Template

/SAAS/t/{tenant}/API/1.0/REST/auth/system/login

  The images below show the configured operations in vRO \[gallery type="slideshow" link="file" columns="4" size="large" ids="839,840,841,842,843"\] Now the endpoint and operations are created, import the workflow package attached to this post. ([nl.itq.psi.vidm Workflows)](http://automate-it.today/wp-content/uploads/2017/02/nl.itq_.psi_.vidm_.zip) When the workflow package is imported, open the Configuration Elements tab and edit the Endpoints configuration element located under the ITQ folder. Select the correct HTTP-REST endpoint and REST-Operations, insert the correct username, password and tenant to connect to vRA. As a side-note, the used API calls can only be used with a vRA local account. Domain accounts will throw an “Invalid Credentials” error. Make sure that the user has rights to execute a Directory Sync in vRA. [![](http://automate-it.today/wp-content/uploads/2017/03/Config-Element-Endpoints-300x91.png)](http://automate-it.today/wp-content/uploads/2017/03/Config-Element-Endpoints.png) Now go back to the workflow overview and expand ITQ / PSI / VIDM / Helpers. You should have the same overview as in the image below. [![vRO Workflow structure](http://automate-it.today/wp-content/uploads/2017/02/vRO-Workflow-structure-300x186.png)](http://automate-it.today/wp-content/uploads/2017/02/vRO-Workflow-structure.png) Now execute the “Synchronize active directory” workflow and the synchronization will start! \[caption id="attachment\_845" align="aligncenter" width="474"\][![vRO Workflow execution](http://automate-it.today/wp-content/uploads/2017/02/vRO-Workflow-execution-1024x383.png)](http://automate-it.today/wp-content/uploads/2017/02/vRO-Workflow-execution.png) vRO Workflow execution\[/caption\] Please note that these workflows are not production ready yet and bugs may exist! [Download nl.itq.psi.vidm Workflows!](http://automate-it.today/wp-content/uploads/2017/02/nl.itq_.psi_.vidm_.zip)