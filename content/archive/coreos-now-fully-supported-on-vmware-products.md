---
title: 'CoreOs now fully supported on VMware products'
date: Tue, 17 Mar 2015 10:08:22 +0000
draft: false
tags: ['Containerization', 'CoreOS', 'docker', 'ESXi', 'VMware', 'vSphere6']
---

Last week CoreOS [released](https://coreos.com/blog/) an OS image which included the open-vm-tools. Of course it was possible to run CoreOS on VMware before, but something was missing. With the addition of the open-vm-tools CoreOs is now fully [supported](https://blogs.vmware.com/vsphere/2015/03/coreos-now-supported-vmware.html) on all VMware products. This includes vSphere 6 and vCloud Air. [![coreos](http://www.automate-it.today/wp-content/uploads/2015/03/coreos.png)](http://www.automate-it.today/wp-content/uploads/2015/03/coreos.png) I happened to be working on my demo for the Dutch VMUG UserCon which involves CoreOS as well. So I decided to give it a go as soon as the image was released. And it turns out it works perfectly. I no longer have to build sleeps in my workflows, I can just wait until the VMware Tools are online and then continue the workflow. This makes deploying CoreOS much more efficient and reliable. Aslo, the gracefull shutdown finally works which prevents the OS from getting corrupted when I have to force a reboto from a workflow. I 'll write in more detail about my automated CoreOS deployment in the coming weeks. If you happen to live in the Netherlands then come and see our demo and presentation this thursday (19-march-2015) at the [Dutch VMUG UserCon](http://www.nlvmug.com/nlvmug-event-2015). Want to get started with CoreOs yourself? check out [this blog post](https://coreos.com/blog/vmware-vcloud-air-and-vsphere/) for instructions on how to do this on VMware Fusion. Want to run on vSphere? Here is what I do to download the image on a linux machine and get it to vSphere.```
wget http://alpha.release.core-os.net/amd64-usr/current/coreos\_production\_vmware.vmx
wget http://alpha.release.core-os.net/amd64-usr/current/coreos\_production\_vmware\_image.vmdk.bz2

mkdir coreos
ovftool coreos\_production\_vmware.vmx coreos/coreos.insecure.ovf
```then import the ovf to vSphere using the vSphere (web) client. To start using the image you'll also need a configdrive ISO file. How to create this is aslo explained in [this](https://coreos.com/blog/vmware-vcloud-air-and-vsphere/) article.