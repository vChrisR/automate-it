---
title: 'Stack Elevator - Level 4: Stack&Co'
date: Thu, 19 Apr 2018 10:00:47 +0000
draft: false
tags: ['BOSH', 'CF Summit Boston 2018', 'Cloud Foundry', 'Cloud Foundry', 'docker', 'ITQ', 'PiFoundry', 'Raspberry Pi', 'stack']
---

This is the 6th post in a series about porting Cloud Foundry to Raspberry Pi. Today our ride in the stack elevator brings us to level 4: Stack&Co. Wait? what? Isn't the stack elevator supposed to transport us through the stack? not bing us to the stack? Well.. keep reading to find out what's on this floor.

Cloud Foundry Stack
-------------------

A stack in Cloud Foundry terms is basically a root filesystem for the containers that CF will run for you. The stack is created by exporting a docker image to a tarball and is deployed to a diego cell using a bosh release. When you push an app to Cloud Foundry you can specify the stack to be used. The default usually is cflinuxfs2 but when you happen to have Windows cells as well you could tell your app to use a windows stack. The rep process which is running on each diego cell knows which stacks are available. So when the auctioneer (part of the CF scheduler) offers a task or LRP up for auction the rep knows wether to respond or not. For example: a windows diego cell would only have the windows stack available so it won't participate in an auction for a cflinuxfs stack. This way you won't end up with a linux app on a windows stemcell. Since the raspberry is an ARM platform we needed a similar way of running the right app on the right cell. And of course we neede an ARM compatible root FS for our containers. So we created our own stack. Which wasn't very difficult is our case. We used the docker file from the cflinuxfs2 stack, swapped the base docker image with an ubuntu ARM image, forked the cflinuxfs2 bosh release, put our own tgz in there an done.

app\_lifecycle
--------------

I honestly thought that the stack would be the second to last hurdle in running CF on RPi. The next step being a buildpack and then we would be able to run an app. Right? Nope, there is one other layer in this case, or pie actually. This layer consists of three executables: _builder_, _launcher_ and _shell_ and is called app\_lifecycle. There are actually two flavors of this layer: buildpack\_app\_lifecycle and docker\_app\_lifecycle. But I didn't bother with the second one. The tag line of the buildpack\_app\_lifecycle github [repo](https://github.com/cloudfoundry/buildpackapplifecycle) is very discriptive: "_The bit that takes some bits, mashes them together with other bits, and produces a new bit"_ As the names of the mentioned executables  (_builder,launcher, shell_) suggest, these execs are used to initialize builds, processes and shells. It is basically the entry point of the container. Just like you would specify an entry point for a docker container but this is al the ways the same. Which is to be expected for an opinionated platform. The app\_lifecycle executables are installed on the cloud controller as part of the "file\_server" job in the Diego release. The files are then downloaded by the cells if and when needed. this posed a challenge for me. Not a technical, an psychological one ;). I had been working on a fork of the Diego bosh release and modified it to be deployed on RPi. But I am not running the other components. like the cloud controller, on the RPi but on vSphere machines. This means that I now had two maintain two versions of the Diego release: One targeted for deploying on the RPi and one targeted for deploying to vSphere but with added packages that are compatible with the PI. This also mean these packages have to be cross compiled because this release contains the x86 go compiler and all packages are compile on an x86 machine.

Other bits an pieces
--------------------

In the same diego release that contains the app\_lifecycle there are also _healthcheck_ and _diego-sshd_ packages. These are installed and use in the same way as the app\_lifecycle packages mentioned above. So for these packages I also created additional packages in the bosh release that are cross compiled. That's all! We're finally ready to run an app!  are we?