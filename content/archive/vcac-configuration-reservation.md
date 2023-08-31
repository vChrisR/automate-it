---
title: 'vCAC Configuration - Reservation'
date: Mon, 05 May 2014 11:00:46 +0000
draft: false
tags: ['Automation', 'Configuration', 'Configuration', 'Reservation', 'VMware', 'vRealize Automation', 'vRealize Automation (vCAC)']
---

This blogpost is the fourth in the vCAC configuration [series](http://automate-it.today/vcac-configuration-steps/ "vCAC Configuration Steps").

Before a user can request a machine there need to be available resources, this resources are created with the [Fabric](http://automate-it.today/fabric-configuration) groups. Within this fabric you can create a reservation.

A fabric administrator creates a reservation to allocate provisioning resources in the fabric group to a specific business group.

A virtual reservation allocates a share of the memory, CPU and storage resources on a particular compute resource for a business group to use.

A physical reservation is a set of physical machines reserved for a business group to use. Unprovisioned physical machines must be added to a physical reservation before being provisioned or imported, and cannot be removed until they are decommissioned and become unprovisioned.

A cloud reservation provides access to the provisioning services of a cloud service account, for Amazon AWS, or to a virtual datacenter, for vCloud Director, for a business group to use.

A business group can have multiple reservations on the same compute resource or different compute resources, or any number of physical reservations containing any number of physical machines.

A compute resource can also have multiple reservations for multiple business groups. In the case of virtual reservations, you can reserve more resources across several reservations than are physically present on the compute resource. For example, if a storage path has 100GB of storage available, a fabric administrator can create one reservation for 50GB of storage and another reservation using the same path for 60GB of storage. You can provision machines by using either reservation as long as sufficient resources are available on the storage host.

You can reserve physical machines only for a single business group. Because physical machines do not belong to fabric groups, all fabric administrators can manage all physical machines and reserve them for a particular business group.

![Reservation](http://automate-it.today/wp-content/uploads/2014/04/Reservation.jpg)

*   A reservation can only contain one policy.
*   A policy can be used on multiple reservations
*   Only one policy can be added to a blueprint

Go [back](http://automate-it.today/vcac-configuration-steps/ "vCAC Configuration Steps") to the configuration steps overview.