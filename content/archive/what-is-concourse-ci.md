---
title: 'What is Concourse CI?'
date: Thu, 20 Apr 2017 09:00:42 +0000
draft: false
tags: ['CI', 'Cloud Foundry', 'Cloud Foundry', 'Concourse', 'Concourse CI', 'ITQ', 'Pivotal']
---

This is my third blog in my "What is" series about different products that are part of the Cloud Foundry ecosystem. I discussed [Cloud Foundry](http://www.automate-it.today/what-is-cloud-foundry/) and [BOSH](http://www.automate-it.today/what-is-bosh/) earlier and now it's time to for he next: What is Concourse CI? ![](http://seeklogo.com/images/C/concourse-logo-42A9F81B42-seeklogo.com.png)

So what is it?
--------------

The github tagline for the concourse project is "Continuous thing doer". Which is quite accurate. Some would call it a Continuous Integration tool. It serves the same purpose as the well known tool Jenkins. It works quite different though. you can find a comparison between Concourse and and other CI tools [here](http://concourse.ci/concourse-vs.html) so I won't go into details right now. What is interesting to know though is that Concourse was born at Pivotal and is considered the standard CI tool for Cloud Foundry and related projects for a while now. The product was born out of necessity. Other tools just couldn't deliver what the CF development teams needed. And what may even be more important: Other tools don't follow the design principles all Pivotal and CF software is following. One of the most important ones being: "No snowflakes".

Snowflake?
----------

As you may know, each snowflake is different from other snowflakes. It's unique. And that's fine when we're talking about real snowflakes. I's not so fine when it concerns servers, especially if you're running hundreds of them. If every server is special you have to run a backup for each one of them regularly, you need instructions on how to configure the server when it needs to be rebuild or DR'd. Troubleshooting becomes difficult because you don't know how it needs to be configured. After all it's different from all other servers so you have no reference. In order to avoid snowflakes CF, BOSH and Concourse use text files to store configuration for Apps, Servers and Pipelines. If a server or app fails you can just blow it away and reload from the config file. Done. If you are using Jenkins for your CI you probably did a lot of specific configuration on the Jenkins server. If you lost the server you would need to spent a lot of time re-configuring it or restore it from a backup. It's different for Concourse Though. In concourse everything is stored in yml files. Concourse server is gone? Build a new one from scratch and reload your pipelines from yml files. You already know that works fine. After all that's how the config got there in the first place.

Concourse concepts
------------------

Pipelines are first class citizen in Concourse CI. A CI pipeline is basically all the steps that need to be taken to get application code from the code repository all the way to production servers or at least a production release. Steps could be: download the code, build the code, run unit tests, run integration tests, deploy to Cloud Foundry. concourse pipelines consist of resources and tasks. Jobs are used to compose resources and tasks. The pipline is describes in a yml file. Tasks can be describe in the same yml but are often described in external files. Since all this is stored in the same repo as the application code versioning tasks and pipelines becomes really easy.  For an example take a look at the ci folder in my demo app [here](https://github.com/vChrisR/vMiauw/tree/master/ci). Below is a screenshot of what that pipeline looks like in the Concourse GUI [![Screenshot from 2017-04-13 15-23-29](http://www.automate-it.today/wp-content/uploads/2017/04/Screenshot-from-2017-04-13-15-23-29-300x154.png)](http://www.automate-it.today/what-is-concourse-ci/screenshot-from-2017-04-13-15-23-29/) The online documentation for Concourse CI is excellent so I'll be lazy and give you the link to the description of the concepts [here](http://concourse.ci/concepts.html) in case you want to know more :).

Try it yourself
---------------

Before you run off and try it yourself let me tell you how to interact with Concourse. I already showed you the GUI. But know that the GUI is only intended to give a visual presentation of your pipelines. It is great so show on a big monitor in you dev teams office. Creating the pipelines and some other configuration things are done through the fly cli. Which is nice, I hate taking my hands off the keyboard :). If you want to try Concourse out for yourself then running the dockerized version is probably the fastest way to [get going](http://concourse.ci/docker-repository.html). If you read my [blog post](http://www.automate-it.today/what-is-bosh/) about BOSH and gave that a go yourself you might want to try to deploy Concourse [using BOSH](http://concourse.ci/clusters-with-bosh.html). To help you get started I shared my BOSH manifest below. I couldn't get the HTTPS part working so I left that out for know.```
\---
name: concourse
director\_uuid: #put you BOSH idrector UUID here
stemcells:
- alias: trusty
  os: ubuntu-trusty
  version: latest

releases:
- name: concourse
  version: latest
- name: garden-runc
  version: latest

instance\_groups:
- name: web
  instances: 1
  vm\_type: default
  stemcell: trusty
  azs: \[homelab\]
  networks: \[{name: default}\]
  jobs:
  - name: atc
    release: concourse
    properties:
      external\_url: #put your external url here
      postgresql\_database: &atc\_db atc

      basic\_auth\_username: admin
      basic\_auth\_password: admin
      bind\_port: 80  
  - name: tsa
    release: concourse
    properties: {}

- name: db
  instances: 1
  # replace with a VM type from your BOSH Director's cloud config
  vm\_type: default
  stemcell: trusty
  # replace with a disk type from your BOSH Director's cloud config
  persistent\_disk\_type: default
  azs: \[ homelab\]
  networks: \[{name: default}\]
  jobs:
  - name: postgresql
    release: concourse
    properties:
      databases:
      - name: \*atc\_db
        # make up a role and password
        role: atc
        password: password

- name: worker
  instances: 1
  # replace with a VM type from your BOSH Director's cloud config
  vm\_type: large
  stemcell: trusty
  azs: \[homelab\]
  networks: \[{name: default}\]
  jobs:
  - name: groundcrew
    release: concourse
    properties: {}
  - name: baggageclaim
    release: concourse
    properties: {}
  - name: garden
    release: garden-runc
    properties:
      garden:
        listen\_network: tcp
        listen\_address: 0.0.0.0:7777

update:
  canaries: 1
  max\_in\_flight: 1
  serial: false
  canary\_watch\_time: 1000-60000
  update\_watch\_time: 1000-60000

```