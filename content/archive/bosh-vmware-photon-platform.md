---
title: 'BOSH on VMware Photon Platform'
date: Tue, 18 Apr 2017 09:00:27 +0000
draft: false
tags: ['BOSH', 'Cloud Foundry', 'CPI', 'manifest', 'Photon', 'Photon Platform', 'Photon Platform', 'Redis']
---

I explained both [BOSH](http://www.automate-it.today/what-is-bosh/) and the [Photon platform](http://www.automate-it.today/getting-started-vmware-photon-platform/) in previous posts. I never did a post on how to deploy BOSH on vSphere but [this document](https://bosh.io/docs/init-vsphere.html) does a very good job describing the process. The only thing I want to add to that is: Don't use "@" in your passwords! Cost me a day or so to figure out what was going wrong. In this post I will detail how to run BOSH on VMware Photon platform. _Update 19-04-2017: This post was based on Photon platform 1.1.1. As of today the current version is Photon platform 1.2. The steps in this post may or may not work for version 1.2._ ![](https://upload.wikimedia.org/wikipedia/en/4/41/Bosh-logo.png) ![](http://zdnet4.cbsistatic.com/hub/i/r/2015/04/21/e90ed476-41b5-402e-b6b7-ab551436d09e/thumbnail/770x578/c3533a7a82c0fd8c25e7f005473d2b67/vmw-logo-photon.jpg)

Prepare Photon Platform
-----------------------

1.  Install Photon platform. [This blog post](http://www.automate-it.today/getting-started-vmware-photon-platform/) details how you to do that.
2.  Make sure you have the photon cli installed. Instructions [here](https://github.com/vmware/photon-controller/wiki/Quick-Start-Guide#installing-the-photon-controller-cli-on-a-linux-workstation).
3.  I'm going to assume that you don't have anything configured on the photon platform yet. If you have you'll probably already know what to do. I'll also ussume this is a lab where you have full access.
4.  Connect the photon cli to you photon platform.```
    photon target set https://192.168.192.76:443 --nocertcheck
    photon target login --username administrator@photon.lab
    ```
5.   Create a photon tenant and tell the cli to use it (press enter on any questions to use the default)```
    photon tenant create bosh
    photon tenant set bosh
    ```
6.  Create a network. I'm going to assume you use the default portgroup named "VM Network". If not please substitute your network name in the command below.```
    photon network create --name vmnet --portgroups 'VM Network' --description vmnet
    ```
7.  Create a resource ticket for the bosh environment. I didn't find a way to deploy to other projects than the one you deployed the bosh director to. So make sure you create a big enough ticket to also fit the workloads you'll be deploying with BOSH.```
    photon resource-ticket create --name boshTicket --tenant bosh --limits 'vm.count 100 COUNT, vm.memory 32 GB, vm.cpu 400 COUNT'
    ```
8.  Create a project that consumes the resources.```
    photon project create --name boshProject --tenant bosh --resource-ticket boshTicket --percent 100
    ```
9.  Add some flavor.  Flavors are types of resources on offer on the Photon platform. It's like AWS instance types.```
    photon flavor create --name vm-basic --kind vm --cost 'vm.count 1 COUNT, vm.cpu 2 COUNT, vm.memory 2 GB'
    photon flavor create --name disk-eph --kind ephemeral-disk --cost 'ephemeral-disk.count 1 COUNT'
    photon flavor create --name disk-persist --kind persistent-disk --cost 'persistent-disk.count 1 COUNT'
    ```

Deploy BOSH
-----------

### Install BOSH cli tools

To be able to install BOSH you'll need the bosh-init tool. This tool is like a mini BOSH which is able to deploy BOSH. So it's kinda like BOSH deploys itself. I won't explain how to install bosh-init, the cloud foundry docs on this are pretty good. Please follow instructions [here](https://bosh.io/docs/install-bosh-init.html). To be able to interact with a BOSH director once it's deployed you'll need the BOSH cli itself. In this case you'll even need it before the BOSH director is running because it's used to build the Photon CPI release. Again, find the cloud foundry docs on how to install the bosh cli [here](https://bosh.io/docs/bosh-cli.html).

### Prepare the Photon CPI

BOSH is able to work with a lot of different cloud (IaaS) providers and platforms. I already mentioned vSphere. But BOSH is also able to use vCloud, AWS, Google and Openstack. The magic that makes this multi-cloud solution possible is the Cloud Provider Interface or CPI. VMware has published a CPI for Photon. It's not published on the BOSH website yet but you can find it [on github](https://github.com/vmware/bosh-photon-cpi).  To be able to use the CPI you'll want to install it into a BOSH director. How? Using a BOSH release of course. The Photon CPI BOSH release is [here](https://github.com/cloudfoundry-incubator/bosh-photon-cpi-release). Since there is no ready build  Photon CPI release we'll have to build our own. Don't be scared, it's not that hard (disclaimer: I'm using Ubuntu. commands on a Mac should be  similar, not sure about window though). Here we go:

1.  Make sure you have the git client installed on your OS
2.  Create a folder to contain the CPI release and your deployment yml. I used ~/my-bosh/photon.
3.  cd into the folder you created
4.  Clone the Photon CPI release git repo, cd into the created folder and create the release:```
    git clone https://github.com/cloudfoundry-incubator/bosh-photon-cpi-release.git
    cd ./bosh-photon-cpi-release/
    bosh create release --force --with-tarball
    
    ```
5.  There'll be a dev\_releases folder in the bosh-photon-cpi-release folder now. Copy the cpi tgz file to ~/my-bosh/photon```
    cd ~/my-bosh/photon
    cp ./bosh-photon-cpi-release/dev\_releases/bosh-photon-cpi/bosh-photon-cpi-1.1.1+dev.1.tgz ./
    ```

### Create BOSH manifest

deployments in BOSH are described in so called manifests. These are files in YAML format containing a bunch of settings. Each type of deployment has it's own manifest and so does the bosh deployment itself. You can find an example manifest for bosh with the photon CPI in the [photon CPI release git repo](https://github.com/cloudfoundry-incubator/bosh-photon-cpi-release/blob/master/manifests/bosh-micro.yml).  I'll share my own manifest below so you 'll have a feel of what it should look like with all the values populated. If you used the yml from [my blog post](http://www.automate-it.today/getting-started-vmware-photon-platform/) to deploy photon  then you can use the my bosh manifest with just two changes:

1.  change the network id on line 39. The command to get the id is```
    photon network list
    ```
2.  Change the photon project id on line 114. The command to get the id is```
    photon project list
    ```

save the manifest yml to ~/my-bosh/photon/bosh-photon.yml

```
\---
name: bosh

releases:
- name: bosh
  url: https://bosh.io/d/github.com/cloudfoundry/bosh?v=261.4
  sha1: 4da9cedbcc8fbf11378ef439fb89de08300ad091
- name: bosh-photon-cpi
  url: file://bosh-photon-cpi-1.1.1+dev.1.tgz

resource\_pools:
- name: vms
  network: private
  stemcell:
    url: https://bosh.io/d/stemcells/bosh-vsphere-esxi-ubuntu-trusty-go\_agent?v=3363.12
    sha1: 8899d9b76edde5722d98088983d416fa32c597e9
  cloud\_properties:
    vm\_flavor: vm-basic
    disk\_flavor: disk-eph
  env:
    bosh:
      # c1oudc0w is a default password for vcap user
      password: "$6$4gDD3aV0rdqlrKC$2axHCxGKIObs6tAmMTqYCspcdvQXh3JJcvWOY2WGb4SrdXtnCyNaWlrf3WEqvYR2MYizEGp3kMmbpwBC6jsHt0"

disk\_pools:
- name: disks
  disk\_size: 20\_000
  cloud\_properties:
    disk\_flavor: disk-persist

networks:
- name: private
  type: manual
  subnets:
  - range: 192.168.192.0/24
    gateway: 192.168.192.1
    dns: \[192.168.192.21\]
    cloud\_properties:
      network\_id: 53bf297f-bfd7-45f3-9701-5e86448baefd

jobs:
- name: bosh
  instances: 1

  templates:
  - {name: nats, release: bosh}
  - {name: postgres, release: bosh}
  - {name: blobstore, release: bosh}
  - {name: director, release: bosh}
  - {name: health\_monitor, release: bosh}
  - {name: cpi, release: bosh-photon-cpi}

  resource\_pool: vms
  persistent\_disk\_pool: disks

  networks:
  - {name: private, static\_ips: \[192.168.192.40\]}

  properties:
    nats:
      address: 127.0.0.1
      user: nats
      password: password

    postgres: &db
      listen\_address: 127.0.0.1
      host: 127.0.0.1
      user: postgres
      password: password
      database: bosh
      adapter: postgres

    blobstore:
      address: 192.168.192.40
      port: 25250
      provider: dav
      director:
        user: director
        password: password
      agent:
        user: agent
        password: password
      options:
        endpoint: http://192.168.192.40:25250
        user: agent
        password: password


    director:
      address: 127.0.0.1
      name: my-bosh
      db: \*db
      cpi\_job: cpi
      user\_management:
        provider: local
        local:
          users:
          - {name: admin, password: password}
          - {name: hm, password: password}

    hm:
      director\_account:
        user: hm
        password: password
      resurrector\_enabled: true
      intervals:
        agent\_timeout: 180

    photon: &photon
      target: https://192.168.192.76:443
      user: administrator@photon.lab
      password: Passw0rd123!
      ignore\_cert: true
      project: 9c6103d7-b7e1-4850-bd17-bad87570be3f

    agent:
      mbus: nats://nats:password@192.168.192.40:4222

    ntp: &ntp \[nl.pool.ntp.org\]

cloud\_provider:
  template: {name: cpi, release: bosh-photon-cpi}
  mbus: https://mbus:password@192.168.192.40:6868

  properties:
    photon: \*photon
    agent: {mbus: 'https://mbus:password@0.0.0.0:6868'}
    blobstore:
      provider: local
      options:
        blobstore\_path: /var/vcap/micro\_bosh/data/cache
    ntp: \*ntp
```

### Run bosh-init deploy

Now you can finally start the deployment. It's very simple :)```
cd ~/my-bosh/photon
bosh-init deploy ./bosh-photon.yml
```And now we wait :)

Use BOSH
--------

Now that we deployed BOSH we might want to try to use BOSH for something useful. One of the simplest examples of something useful is deploying a redis server. Here are the steps involved:

1.  On the Photon platform create another resource ticket and a new project consuming the ticket.
2.  Target the bosh cli to the fresh BOSH director and login (if you're using my yml the password is 'password'```
    bosh target 192.168.192.40
    bosh login admin
    ```
3.  run bosh status to confirm you're connected and BOSH is up and running. [![Screenshot from 2017-04-04 16-44-17](http://www.automate-it.today/wp-content/uploads/2017/04/Screenshot-from-2017-04-04-16-44-17-300x195.png)](http://www.automate-it.today/bosh-vmware-photon-platform/screenshot-from-2017-04-04-16-44-17/)
4.  Upload the ubuntu trusty stemcell```
    bosh upload stemcell https://s3.amazonaws.com/bosh-core-stemcells/vsphere/bosh-stemcell-3363.14-vsphere-esxi-ubuntu-trusty-go\_agent.tgz
    ```
5.  Upload the redis release```
    bosh upload release http://bosh.io/d/github.com/cloudfoundry-community/redis-boshrelease?v=12
    ```
6.  Create a cloud-config YAML for BOSH. Below is my yml.
    1.  Replace the project id on line 17
    2.  Configure you ip range in lines 37..41
    3.  Replace the network id in line 42```
        azs:
        - name: homelab
        vm\_types:
        - name: default
          cloud\_properties:
            vm\_flavor: vm-basic
            disk\_flavor: disk-eph
          bosh:
            password: $6$Y2VgK5cR75B1G$CecLGT4euQoKHs9hI1GBk9GXONr1ntlPHFdlWrtkXgEC4bh/1WX.HBT.ygR0WVWq.aV8Vg/cuTi1JbRNXmNJy1
        
        disk\_types:
        - name: default
          disk\_size: 3\_000
          cloud\_properties:
            disk\_flavor: disk-persist
        - name: medium
          disk\_size: 20\_000
          cloud\_properties:
            disk\_flavor: disk-persist
        - name: large
          disk\_size: 50\_000
          cloud\_properties:
            disk\_flavor: disk-persist
        
        networks:
        - name: default
          type: manual
          subnets:
          - range: 192.168.192.0/24
            gateway: 192.168.192.1
            az: homelab
            dns: \[192.168.192.21\]
            reserved: \[ 192.168.192.2-192.168.192.150 \]
            cloud\_properties: {network\_id: 53bf297f-bfd7-45f3-9701-5e86448baefd}
        
        compilation:
          workers: 4
          reuse\_compilation\_vms: true
          az: homelab
          vm\_type: default
          network: default
          env:
            bosh:
              password: $6$hVE/3gPUys$BIgRmQXrAy6lh3YVwjTYqWueqYUhhw7UQkOUFzf3p44bMmZnp5XDvpDebkGjR1om3ot.1jCNYNiXLggLI8Dw1.
        
        ```
7.  Load the cloud config into bosh```
    bosh update cloud-config ~/my-bosh/photon/photon-cloud-config.yml
    
    ```
8.  Create the redis deployment yaml. Again, below is my version of it.
    1.  Replace the director\_uuid. Retrieve the uuid by running bosh status
    2.  Store the manifest in ~/my-bosh/photon/redis.yml```
        name: redisOnPhoton
        director\_uuid: c1fe3423-9b10-42e0-95f9-e06053ec38ee
        releases:
        - name: redis
          version: latest
        instance\_groups:
        - name: redis-master
          instances: 1
          azs:  \[ homelab \]
          jobs:
          - name: redis
            release: redis
            properties:
              redis:
                password: password
          vm\_type: default
          stemcell: default
          persistent\_disk\_type: medium
          networks:
          - name: default
        update:
          canaries: 1
          canary\_watch\_time: 1000-100000
          max\_in\_flight: 1
          update\_watch\_time: 1000-100000
        stemcells:
        - alias: default
          os: ubuntu-trusty
          version: latest
        ```
9.  Tell the bosh cli to use this manifest```
    bosh deployment ~/my-bosh/photon/redis.yml
    ```
10.  Now deploy redis

```
bosh deploy
```After the deployment is finished you can list the deployments and the VMs it deployed but running these commands```
bosh deployments
bosh vms
```The output should be similar to this: [![Screenshot from 2017-04-04 19-19-46](http://www.automate-it.today/wp-content/uploads/2017/04/Screenshot-from-2017-04-04-19-19-46-300x134.png)](http://www.automate-it.today/bosh-vmware-photon-platform/screenshot-from-2017-04-04-19-19-46/) Pfew..... if you made it this far: Congrats! you're on your way to being a cloud native :).