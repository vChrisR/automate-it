---
title: 'VMware Photon Platform 1.2 released'
date: Wed, 19 Apr 2017 09:00:32 +0000
draft: false
tags: ['Cloud Native', 'Kubernetes', 'Photon', 'Photon Platform', 'VMware', 'VMware Photon Platform']
---

Yesterday VMware silently released a new version of its opensource cloud native platform. VMware Photon Platform 1.2 is available for download at [github](https://github.com/vmware/photon-controller/releases) now. You can find the details of the new release in the [release notes](https://github.com/vmware/photon-controller/wiki/Release-Notes). Below are the highlights of the new release. ![](https://raw.githubusercontent.com/docker-library/docs/de9a58372c9e1e58ccb08186ab6ebed278b86521/photon/logo.png)

What's new?
-----------

*   Photon Controller now supports ESXi 6.5 Patch 201701001. Support for ESXi 6.0 is dropped.
*   Photon platform now comes with Lightwave 1.2 which supports authenticating using windows sessions credentials. Given you're using the CLI from a windows box.
*   The platform now supports Kubernetes 1.6 and also supports persistent volumes for Kubernetes
*   NSX-T support is improved
*   Resource tickets have been replaced with quotas which can be increased and decreased. This is a big improvement in my opinion. The previous release wouldn't let you change resource allocation which was a definite blocker for production use.
*   The API is now versioned. Which means the API url now starts with /v1/

What's broken?
--------------

*   Lightwave 1.2 is incompatible with earlier versions
*   ESXi 6.0 is no longer supported
*   The API is incompatible with previous API versions. But the good new is that it's now versioned so this was the last time they broke the API (hopefully).

_update 20-04-2017: Some updates taken from the [github issues](https://github.com/vmware/photon-controller/issues): _

*   _HA Lightwave setup is no longer supported. Will be back in 1.2.1_
*   _version 1.1.1 didn't create any flavours at installation but 1.2 seems to create duplicate flavours._