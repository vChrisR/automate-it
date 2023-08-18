---
title: 'Selecting Network Through vCAC Property'
date: Tue, 20 May 2014 14:18:35 +0000
draft: false
tags: ['Attributes', 'Automation', 'Infra Automation', 'Network Selection', 'Property', 'VMware', 'vRealize Automation', 'vRealize Automation (vCAC)']
---

vCAC is a really powerful automation tool, primarily giving users the opportunity to request their own applications without interaction of the IT Department. In this particular case I was configuring a blueprint to be used by developers.

Using vCAC to clone vCenter templates is really simple; users request a machine to use in the development or in the production environment. While I was testing the deployment of this blueprint the department responsible for Active Directory complained because my test machines, without the correct naming convention, appeared in the production AD.

The reservation used for this blueprint contained two networks, a development and a production network. Normally after deploying a vCenter template you edit the Virtual Machine hardware and select the correct network, before powering on the VM. In this case the requesting user does not have permission to connect to vCenter and to change this setting, and vCAC will just power on the VM. So we need another solution.

The solution is surprisingly not that difficult, you can use a custom property within vCAC. I will describe this in a few steps.

The first step is to find out which networks are available, this can be done by editing the reservation for this particular blueprint.

[![networkproperty-1](http://automate-it.today/wp-content/uploads/2014/04/networkproperty-1.jpg)](http://automate-it.today/wp-content/uploads/2014/04/networkproperty-1.jpg)

The next step is creating a new property definition within the property dictionary:

[![networkproperty-2](http://automate-it.today/wp-content/uploads/2014/04/networkproperty-2.jpg)](http://automate-it.today/wp-content/uploads/2014/04/networkproperty-2.jpg)

Name: VirtualMachine.Network0.Name Display Name: Select Network Control Type: DropDownList Required: Yes

After you created the definition you can edit the property attributes

[![networkproperty-3](http://automate-it.today/wp-content/uploads/2014/04/networkproperty-3.jpg)](http://automate-it.today/wp-content/uploads/2014/04/networkproperty-3.jpg)

Type: ValueList Name: Network Value: Add all choices comma separated.

Now when the user will request a blueprint, there is a required choice with the name Network and the choices you added, in this case Development-1 and Production-1.

[![networkproperty-4](http://automate-it.today/wp-content/uploads/2014/04/networkproperty-4.jpg)](http://automate-it.today/wp-content/uploads/2014/04/networkproperty-4.jpg)