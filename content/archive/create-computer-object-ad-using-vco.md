---
title: 'Create Computer object in AD using vCO'
date: Wed, 02 Jul 2014 10:00:59 +0000
draft: false
tags: ['vRealize Orchestrator']
---

When using vCAC to deploy Windows machines you really want your windows machines to automatically join the domain. Of course just joining the domain will put the machine in the default computer OU which often is not the right place. The easiest way to get the machine to join the domain in the right OU is to pre-stage the computer account. In other words: Create the computer account before the machine is deployed. Since vCAC cannot do this for us we need a vCO workflow to do the job. Since vCO comes with a Active Directory plug-in that shouldn't be a problem. Right? Well, it is a little more complicated than just running the out-of-othe-box workflow. So here is how to create computer object in AD using vCO.

Find the OU Object
------------------

The plug-in comes with a workflow called "Create a computer in an organizational unit". One of the inputs for this workflow is an "organization unit" object. This is fine if you're running the workflow from the vCO client or from the vSphere web client. But if you trigger it from vCAC you'll only get a bunch of properties which are all of the "string" type. So what we really need is a workflow that can use a distinguished name to find the right OU object and create the computer object there. Since we already have the workflow that takes the OU object the only thing we need to do is convert a distinguished name into an OU object. The script below does exactly that.```
var searchOU = targetOU.split("=")\[1\].split(",")\[0\];
System.log("Search OU: " +searchOU);

var ouArray = ActiveDirectory.search("OrganizationalUnit", searchOU);

var ouIndex = ouArray.map(function(e) { return e.distinguishedName.toLowerCase(); }).indexOf(targetOU.toLowerCase());
if (ouIndex > -1) {
    ou = ouArray\[ouIndex\];
    System.log("Found OU: " +ou.distinguishedName);
} else {
    throw("OU not found");
}
```The script has one input variable called "targetOU" which is a string that contains the DN of the OU. There is one output variable called "ou" which is of the type AD:OrganizationalUnit.

Step by step
------------

Let's go through the script step by step:```
var searchOU = targetOU.split("=")\[1\].split(",")\[0\];
System.log("Search OU: " +searchOU);
```This retrieves the name of the OU name from the DN which is then used as input for the next step.```
var ouArray = ActiveDirectory.search("OrganizationalUnit", searchOU);
```The ActiveDirectory.search method is used to find all OUs in AD matching the name of our OU. This returns an array of OU objects. If there is only on OU with that name we could stop here but a lot of ADs contain OUs with the some name. For example: you could have an OU for each department and then a sub OU for each OS (2008, 2012). This means there are a whole bunch of 2012 OUs but you need a particular one.```
var ouIndex = ouArray.map(function(e) { return e.distinguishedName.toLowerCase(); }).indexOf(targetOU.toLowerCase());
```So we map the distinguishedName of every object in the array into a new array and look for the index number of the OU we are looking for. So now we now at which index number the OU is stored. I convert everything to lower case to make it case insensitive.```
if (ouIndex > -1) {
    ou = ouArray\[ouIndex\];
    System.log("Found OU: " +ou.distinguishedName);
} else {
    throw("OU not found");
}
```If we actually found the OU we copy the object from the array into the output variable. If not, just throw an error. Now that we have the right OU object we can feed this into the out-of-the-box workflow "Create Computer account in organization unit" together with the computer name. Below is a picture. Up to you to create your own one :) [![Create Computer in OU](http://automate-it.today/wp-content/uploads/2014/07/Screenshot-from-2014-07-01-163932-300x60.png)](http://automate-it.today/wp-content/uploads/2014/07/Screenshot-from-2014-07-01-163932.png)