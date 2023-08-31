---
title: 'vCAC Business Group Configuration'
date: Thu, 01 May 2014 10:59:06 +0000
draft: false
tags: ['Business Group', 'Configuration', 'Configuration', 'vCloud', 'VMware', 'vRealize Automation', 'vRealize Automation (vCAC)']
---

This blogpost is the third in the vCAC configuration [series](http://automate-it.today/vcac-configuration-steps/ "vCAC Configuration Steps") and will focus on the configuration of the Business Groups.

A business group associates a set of services and resources to a set of users, often corresponding to a line of business, department, or other organizational unit. Business groups are managed on the Infrastructure tab but are used throughout the service catalog. Entitlements in the catalog are based on business groups. To request catalog items, a user must belong to at least one business group. A business group can have access to catalog items specific to that group and to catalog items that are shared between business groups in the same tenant. In IaaS, each business group has one or more reservations that determine on which compute resources the machines that this group requested can be provisioned. A business group must have at least one business group manager, who monitors the resource use for the group and often is an approver for catalog requests. In IaaS, group managers also create and manage machine blueprints for the groups they manage. Business groups can also contain support users, who can request and manage machines on behalf of other group members. Business group managers can also submit requests on behalf of their users. A user can be a member of more than one business group, and can have different roles in different groups.

Within a business group there are three different roles, that should be bound to Active Directory groups.

![Business_Group_Roles](http://automate-it.today/wp-content/uploads/2014/04/Business_Group_Roles.jpg)

Below is the third workflow on the vCAC configuration about configuring the Business Groups. The action blocks are actually clickable and will show you the matching parts of the [VMware documentation](https://www.google.nl/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&cad=rja&uact=8&ved=0CDAQFjAA&url=https%3A%2F%2Fwww.vmware.com%2Fsupport%2Fpubs%2Fvcac-pubs.html&ei=tFRWU5nPLc2HyQPpiIHACQ&usg=AFQjCNEo-ZBybEXNDEJVB2OiNSzxTmmdMg&bvm=bv.65177938,d.bGQ "VMware vCAC Documentation Center") in a popup window.

\[imagemap id="223"\]

Go [back](http://automate-it.today/vcac-configuration-steps/ "vCAC Configuration Steps") to the configuration steps overview.