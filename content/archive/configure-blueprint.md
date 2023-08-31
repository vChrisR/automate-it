---
title: 'vCAC Blueprint Configuration'
date: Mon, 12 May 2014 14:52:52 +0000
draft: false
tags: ['Automation', 'Blueprint', 'Configuration', 'Configuration', 'VMware', 'vRealize Automation', 'vRealize Automation (vCAC)']
---

Below is the vCAC configuration workflow about configuring a Blueprint. This blogpost is the fifth in the vCAC configuration [series](http://automate-it.today/vcac-configuration-steps/ "vCAC Configuration Steps"). The action blocks are actually clickable and will show you the matching parts of the [VMware documentation](https://www.google.nl/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&cad=rja&uact=8&ved=0CDAQFjAA&url=https%3A%2F%2Fwww.vmware.com%2Fsupport%2Fpubs%2Fvcac-pubs.html&ei=tFRWU5nPLc2HyQPpiIHACQ&usg=AFQjCNEo-ZBybEXNDEJVB2OiNSzxTmmdMg&bvm=bv.65177938,d.bGQ "VMware vCAC Documentation Center") in a popup window. \[imagemap id="240"\] Go [back](http://automate-it.today/vcac-configuration-steps/ "vCAC Configuration Steps") to the configuration steps overview.   A couple of interesting vCAC documentation links about Blueprints:

[Space-Efficient Storage for Virtual Provisioning](http://pubs.vmware.com/vCAC-60/topic/com.vmware.vcac.iaas.all.doc/GUID-48C0E6D7-0A4B-4EE0-9452-809962CD440D.html "Space-efficient storage technology eliminates the inefficiencies of traditional storage methods by using only the storage actually required for a machine's operations. Typically, this is only a fraction of the storage actually allocated to machines. vCloud Automation Center supports two methods of provisioning with space-efficient technology, thin provisioning and FlexClone provisioning.")

[Choosing a Blueprint Scenario](http://pubs.vmware.com/vCAC-60/topic/com.vmware.vcac.iaas.all.doc/GUID-5DD06442-C5EB-4630-9D89-391A630BDFC4.html "Depending on your environment and the methods of provisioning your fabric administrators have prepared, there are several procedures available to create the blueprint for your needs.")

[Create a Blueprint for the Basic Workflow](http://pubs.vmware.com/vCAC-60/topic/com.vmware.vcac.iaas.all.doc/GUID-3C1E5D17-4092-45F0-9282-42D3CE893713.html "A basic workflow blueprint provisions a virtual machine with no guest operating system. You can install the operating system after the machine is provisioned.")

[Create a Blueprint for Cloning](http://pubs.vmware.com/vCAC-60/topic/com.vmware.vcac.iaas.all.doc/GUID-70A2FE1C-8B75-4FC4-8DF3-A92BA8847BD0.html "You can use vCloud Automation Center to provision machines by cloning from a template object created from a Windows or Linux reference machine.")

[Create a Linked Clone Blueprint](http://pubs.vmware.com/vCAC-60/topic/com.vmware.vcac.iaas.all.doc/GUID-59BF437F-DDB6-4D72-94F3-44EABD336D5B.html "If you are working with a vSphere environment, you can provision a space-efficient copy of a virtual machine called a linked clone. Linked clones are based on a snapshot of a VM and use a chain of delta disks to track differences from a parent machine.")

[Create a Blueprint for Net App FlexClone Provisioning](http://pubs.vmware.com/vCAC-60/topic/com.vmware.vcac.iaas.all.doc/GUID-336AF544-9F8C-49AE-A231-B4234868497C.html "You can use vCloud Automation Center to provision clone machines using Net App FlexClone technology if you are working with a vSphere integration.")

[Create a Blueprint for WIM Provisioning](http://pubs.vmware.com/vCAC-60/topic/com.vmware.vcac.iaas.all.doc/GUID-4813235B-01BD-4A0E-87AE-3B0F99D34E9D.html "You can provision a machine by booting into a WinPE environment and then installing an operating system using a Windows Imaging File Format (WIM) image of an existing Windows reference machine.")

[Create a Blueprint for Linux Kickstart Provisioning](http://pubs.vmware.com/vCAC-60/topic/com.vmware.vcac.iaas.all.doc/GUID-EC89BBD5-2EC4-4EE3-AF6B-84000CC251DA.html "You can provision a machine by booting from an ISO image, then using a kickstart or autoYaSt configuration file and a Linux distribution image to install the operating system on the machine.")

[Create a Blueprint for SCCM Provisioning](http://pubs.vmware.com/vCAC-60/topic/com.vmware.vcac.iaas.all.doc/GUID-A5778F70-3127-469B-B592-C6F6337DC21A.html "You can provision a machine and then pass control to an SCCM task sequence to boot from an ISO image, deploy a Windows operating system, and install the vCloud Automation Center guest agent.")

[Publish a Blueprint](http://pubs.vmware.com/vCAC-60/topic/com.vmware.vcac.iaas.all.doc/GUID-66EF21C4-A709-4106-99E8-06152EBF6BD8.html "Blueprints are automatically saved in the draft state and must be manually published before they appear as catalog items.")

[Choosing a Blueprint Scenario](http://pubs.vmware.com/vCAC-60/topic/com.vmware.vcac.iaas.all.doc/GUID-5443F1E7-A3A2-41AE-9B6C-67D19F3F7664.html "Depending on your environment and the methods of provisioning your fabric administrators have prepared, there are several procedures available to create the blueprint for your needs.")

[Create a Blueprint for Linux Kickstart Provisioning](http://pubs.vmware.com/vCAC-60/topic/com.vmware.vcac.iaas.all.doc/GUID-7D3A1C50-6104-437B-A796-2A806C86EC2F.html "You can provision a Dell iDRAC or HP iLO machine by booting from an ISO image and using a kickstart or autoYaSt configuration file and a Linux distribution image to install the operating system on the machine.")

[Create a Blueprint for WIM Provisioning](http://pubs.vmware.com/vCAC-60/topic/com.vmware.vcac.iaas.all.doc/GUID-878A48AD-A6CE-4EDD-852B-5F472D46389E.html "You can provision a Dell iDRAC or HP iLO machine by booting into a WinPE environment and then installing an operating system using a Windows Imaging File Format (WIM) image of an existing Windows reference machine.")

[Create a Blueprint for SCCM Provisioning](http://pubs.vmware.com/vCAC-60/topic/com.vmware.vcac.iaas.all.doc/GUID-1495270D-3603-4BCA-8022-6FF324758092.html "You can provision a machine and then pass control to an SCCM task sequence to boot from an ISO image, deploy a Windows operating system, and install the vCloud Automation Center guest agent.")

[Create a Blueprint for PXE Provisioning](http://pubs.vmware.com/vCAC-60/topic/com.vmware.vcac.iaas.all.doc/GUID-DE412DE4-F53F-42E4-8CEA-5F07BEF68D5C.html "You can provision a machine by querying a PXE boot server for a network boot strap program (NBP). You can configure the NBP to then download and execute any boot image-based provisioning method to deploy an operating system.")