---
title: 'Stack Elevator - Ground Floor: Piaas Level'
date: Thu, 05 Apr 2018 11:57:28 +0000
draft: false
tags: ['Bakery', 'BOSH', 'BOSH', 'Cloud Foundry', 'ITQ', 'PiFoundry', 'Raspberry Pi']
---

This is part 2 in a series of blogs about how I ported cloud foundry diego cells to Raspberry PI. In this series I'll take you with me in the stack elevator. [Last time](https://wp.me/p4Cjyf-fU) I talked about the basement level which contains the physical side of things. Today we'll take a look at the IaaS layer. Or actually the Piaas layer (Pi As A Service).

bakery
------

Since this piece of software is going to bake Pi(e)s I named it "bakery". [Bakery](https://github.com/PiFoundry/bakery) is basically a very simple _Infrastructure As A Service_ or actually a _Pi As A Service_. Bakery is written in Golang and provides a REST like API for Pi consumers. It relies on DNSMASQ for serving proxy DHCP, HookTFTP for bootstrapping over the network and NFS for serving disks over the network. These services are running on the same machine as bakery itself

Boot process
------------

How to network boot a Pi is explained in [this](https://www.raspberrypi.org/documentation/hardware/raspberrypi/bootmodes/net_tutorial.md) tutorial. However, to be able to serve each Pi its own image I needed to modify and automate the process a "little bit". I think the easiest way to explain how bakery works is just taking you through the boot process of a Pi. So here goes:

1.  Upload a bakeform to bakery. A bakeform is a Pi compatible image. These are the same as SD card images. To be able to boot from the image the cmdline.txt in the boot partition needs to be replaces with a special cmdline.txt which you can find in the [bakery github repo](https://github.com/PiFoundry/bakery).
2.  After the upload bakery mounts the images and uses rsynch to copy the contents of the boot partition into a boot folder which will be served over TFTP.
3.  Someone or something requests en new Pi through the bakery API. The source image is given as a parameter
4.  Bakery mounts the requested image using loopback devices (uses kpartx). Then it will create a new directory under the NFS root directory and use rsynch to copy the contents of the image to this directory.
5.  Bakery will generate a new /etc/exports and run exportfs -a
6.  The Pi is now ready to be powered on. Bakery uses an external executable to control the power manager of the Pis. It is called the [PPI](https://github.com/PiFoundry/bakery-simpleweb-ppi) and works similar to how BOSH uses CPIs. A JSON is fed into the stdin of the PPI and the PPI will call the Pi Power manager ([see previous blog](https://wp.me/p4Cjyf-fU)) to power on the Pi.
7.  The Pi will now do a DHCP request. The actual request is answered by whatever DHCP server is active on the network. But then the DHCP reply is augmented by DNSMASQ proxy dhcp. This reply tells the Pi which TFTP server to use
8.  The Pi will start downloading files from the TFTP server. When it does this it always requests files from a directory which has the Pi serial number as its name. So for example when the Pi downloads start.elf it first requests /<pi serial number>/start.elf. This request is then served by [HookTFTP](https://github.com/tftp-go-team/hooktftp)
9.  HookTFTP is configured to forward all requests over HTTP to bakery instead of serving them itself.
10.  Since the Pi tries to access a folder which is named after its serial number we can use this information in bakery to determine which files need to be served to the Pi. We also use it to render the right cmdline.txt on the fly so the Pi mounts the correct root volume over NFS.
11.  HookTFTP receives the replies from Bakery and then serves the files over TFTP back to the Pi
12.  With the files served over TFTP, the rendered cmdline.txt and the root volume offered over NFS the Pi can now fully boot.

Additional Disks
----------------

Because I was building Bakery specifically to be used by BOSH I also needed a way to offer additional disks to the Pis next to the NFS mounted root volume we already needed to boot. I could have use iSCSI for that but I didn't want to setup an iSCSI box as that would complicate things further. Instead I let bakery create a new folder which is exported over NFS. In that folder Bakery puts a sparse image file called disk.img. The Pi mounts the NFS export and then uses a loopback device to mount the disk. In the BOSH stemcell, which I will discuss in a future article, I run a scheduled script to look for new disks and mount them as needed. It's a bit ugly but it works.

Deployment
----------

I did create a BOSH release for bakery. Yes... a BOSH release to deploy the IaaS that BOSH is going to talk to. Seems like a chicken and egg problem but actually bakery was never intended to be the only IaaS in use. I run my BOSH director on vSphere and use the multi-CPI feature to connect BOSH to both vSphere and Bakery. Unfortunately Bakery won't work properly on Ubunt 14.04 because of a bug in rsynch. So you can't use the Ubuntu Trusty BOSH stemcell to run Bakery. I haven't tested CentOS yet so you might give that a go. I myself am waiting for the Ubuntu 16.04 stemcells.

API Docs
--------

Well.... There are none. If people are interested I might do that at a later stage. I didn't bother so far because bakery was a means to an end, not a standalone project. The end being: running cloud foundry on Rapsberry Pis. However, I did create a Golang [client package](https://github.com/PiFoundry/bakery-client) for Bakery. This same package is also used by the [Bakery CPI](https://github.com/PiFoundry/bakery-cpi).

Security
--------

well.... there is none. There is no authentication on the API, no multi-tenancy, no nothing. Same on the other side: NFS exports are exported to everyone. so each Pi can technically access each other Pis disks. **So use with caution! Definitely not production ready in any way shape or form. Use at your own risk!**