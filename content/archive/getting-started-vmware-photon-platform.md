---
title: 'Getting started with VMware Photon Platform'
date: Mon, 10 Apr 2017 10:00:20 +0000
draft: false
tags: ['Cloud Native', 'Photon controller', 'Photon Platform', 'VMware Photon Platform', 'yml']
---

VMware Photon Platform is an opensource cloud platform build by VMware on top for ESXi. It is specifically build to run containerized and cloud native applications. As such it pushes a lot of features into the application layer and out of the infrastructure. For example: It doesn't support VMware HA or DRS. Or even vMotion. In this post I'll help you getting started with VMware Photon Platform. _Update 19-04-2017: This post was based on Photon platform 1.1.1. As of today the current version is Photon platform 1.2. The only supported ESXi version is now ESXi 6.5, Patch 201701001. The steps in this post may or may not work for version 1.2._ ![](data:image/png;base64,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)

The platform
------------

The Photon platform contains a few different components:

*   Photon installation appliance: Deploy this appliance first an use it to deploy other photon components
*   Lightwave: This is similar to VMware SSO
*   Photon Controller: This is basically a vCenter replacement. It has a scale-out architecture and provides the Photon API, multi tenancy and resource management
*   HA Proxy: Loadbalances requests to the Photon Controllers
*   Photon OS: A tiny Linux distribution optimized to run Docker containers
*   Photon Agent: This is running on each ESXi host managed by Photon controller

Photon supports the following VMware technologies:

*   vSAN: aggregate your local disks into a large storage pool. Since there is no vCenter server in a photon deployment you need an additional appliance to manage vSAN
*   NSX: Photon integrates with VMwares SDN platform. But again: not vCenter. So you'll only be able to use NSX-T, not the wel known NSX-v

Getting Photon Platform up and running
--------------------------------------

There is a [quickstart guide](https://github.com/vmware/photon-controller/wiki/Quick-Start-Guide) which gives you most information you need t deploy Photon Platform. Use the steps below to save some time and fill in some blanks.

### Prepare your lab

1.  Download the installer OVA [here](https://github.com/vmware/photon-controller/releases).
2.  Download ESXi 6.0.0 [here](https://my.vmware.com/group/vmware/details?downloadGroup=ESXI600&productId=490&download=true&fileId=478e2c6f7a875dd3dacaaeb2b0b38228&secureParam=75e1fdba57cbdd98c5df0c85e6e53af3&uuId=bf9f7bf9-f325-42bb-ab9b-b01c86dd988c&downloadType=) (note: 6.5 is not supported at the moment of writing)
3.  Download patch with build number4600944 [here](https://my.vmware.com/group/vmware/patch) (yes, photon only supports this specific build nr sadly...)
4.  Install two ESXi 6.0.0 hosts. I run them as virtual machines on my home lab. DO NOT CONNECT THEM TO A VCENTER!
5.  Both ESxi hosts need a local or shared datastore If you're following my instruction you'll have to name them "local02". I used 150GB datastores which is sufficient to deploy the Photon components on one host. I have 23.4GB left on host running the platform.
6.  SCP the patch to the fresh hosts and use [this KB article](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2144595) for instructions on how to deploy the patch
7.  Make sure you have at least 1 static IP available in the network where you'll be deploying Photon. Obviously that IP should be able to reach the ESXi hosts

### Deploy Photon

1.  Deploy the photon-installer ova file to one of the ESXi hosts. Just use the good ol' vSphere C# client :). The quickstart guide mentions the web client but there is no webclient on ESXi 6.0.... Of course you can use the web client fling but that would add another step to this process.
2.  Prepare a YAML file. The quickstart guide describes the file you need.
    1.  One thing the guide doesn't mention is the fact that you need a complex password of at least 8 characters for the lightwave administrator. If you don't the installer won't throw an error, the installation of lightwave will just fail with a very generic error.
    2.  something that is in de quickstart guide but I missed at first is the fact that all components need to use the lightwave server as their DNS server. Only the lightwave server itself uses your own DNS server.
    3.  Below is the YAML I used. You'll probably have to replace the IP addresses and it assumes that the root password for your ESXi hosts is "password".  It also assumes that your ESXi hosts have a datastore called "local02". another thing you might notice: I'm not joining the host where the photon appliances are deployed to the photon controller. Somehow I can't get that to work.
    4.  ```
        compute:
          hypervisors:
            vesxi60:
              hostname: "vesxi60"
              ipaddress: "192.168.192.6"
              dns: "192.168.192.78"
              credential:
                username: "root"
                password: "password"
            vesxi60c01:
              hostname: "vesxi60c01"
              ipaddress: "192.168.192.23"
              dns: "192.168.192.78"
              credential:
                username: "root"
                password: "password"
        
        lightwave:
          domain: "photon.lab"
          credential:
            username: "administrator"
            password: "Passw0rd123!"
          controllers:
            lightwave:
              site: "homelab"
              appliance:
                hostref: "vesxi60"
                datastore: "local02"
                memoryMb: 2048
                cpus: 2
                credential:
                  username: "root"
                  password: "password"
                network-config:
                  network: "NAT=VM Network"
                  type: "static"
                  hostname: "lightwave.photon.lab"
                  ipaddress: "192.168.192.78"
                  dns: "192.168.192.21"
                  ntp: "nl.pool.ntp.org"
                  netmask: "255.255.255.0"
                  gateway: "192.168.192.1"
        photon:
          imagestore:
            img-store-1:
              datastore: "local02"
              enableimagestoreforvms: "true"
          cloud:
            hostref1: "vesxi60c01"    
          controllers:
              pc:
                appliance:
                  hostref: "vesxi60"
                  datastore: "local02"
                  memoryMb: 2048
                  cpus: 2
                  credential:
                    username: "root"
                    password: "password"
                  network-config:
                    network: "NAT=VM Network"
                    type: "static"
                    hostname: "pc.photon.lab"
                    ipaddress: "192.168.192.77"
                    netmask: "255.255.255.0"
                    dns: "192.168.192.78"
                    ntp: "95.211.160.148"
                    gateway: "192.168.192.1"
        loadBalancer:
          plb:
            appliance:
              hostref: "vesxi60"
              datastore: "local02"
              credential:
                username: "root"
                password: "password"
              network-config:
                network: "NAT=VM Network"
                type: "static"
                hostname: "plb.photon.lab"
                ipaddress: "192.168.192.76"
                netmask: "255.255.255.0"
                dns: "192.168.192.78"
                ntp: "nl.pool.ntp.org"
                gateway: "192.168.192.1"
        
        ```
3.  Save the yml above to a file and copy it to the photon installer appliance. The root password for the appliance is "changeme". I stored the file in /root/photon.yml
4.  Log into the photon installer appliance over SSH (root/changeme)
5.  run: cd /opt/vmware/photon/controller/bin
6.  run: ./photon-setup platform install -config /root/photon.yml
7.  watch the magic happen :)
8.  when the magic is finished connect a browser to the loadbalancer ip. If you used my yml go to: https://192.168.192.76:4343[![Screenshot from 2017-04-04 13-15-07](http://www.automate-it.today/wp-content/uploads/2017/04/Screenshot-from-2017-04-04-13-15-07-300x137.png)](http://www.automate-it.today/getting-started-vmware-photon-platform/screenshot-from-2017-04-04-13-15-07/)
9.  Log in using the lightwave administrator credentials. If you used my yml that would be: administrator@photon.lab / Passw0rd123!
10.  Tadaa:   [![Screenshot from 2017-04-04 13-17-56](http://www.automate-it.today/wp-content/uploads/2017/04/Screenshot-from-2017-04-04-13-17-56-300x150.png)](http://www.automate-it.today/getting-started-vmware-photon-platform/screenshot-from-2017-04-04-13-17-56/)
11.  The GUI is nice but a lot of features are still missing. If you want to use photon you'll need the CLI. you can find it on the [Github releases](https://github.com/vmware/photon-controller/releases) page and [here](https://github.com/vmware/photon-controller/wiki/Quick-Start-Guide#installing-the-photon-controller-cli-on-a-linux-workstation) are instructions on how to install it.

### Using Photon

This post is lengthy enough as it is so I won't go into details here. One of the features of Photon is that it can [deploy a Kubernetes cluster for you](https://github.com/vmware/photon-controller/wiki/Creating-a-Kubernetes-Cluster).  I'm also working on a post explaining how to use BOSH with photon.