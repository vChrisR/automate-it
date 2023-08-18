---
title: '10 Videos 5 Cool BOSH features'
date: Wed, 27 Jun 2018 10:00:43 +0000
draft: false
tags: ['BOSH', 'BOSH', 'Cloud Foundry', 'ITQ', 'youtube', 'Youtube']
---

Yesterday I released the [10th video](https://youtu.be/gmsaBOuG65A) in the [BOSH tutorial](https://www.youtube.com/playlist?list=PLu8oHHyQbBrTGvoGdF7NuZqKqU_31cKkc) series. I know [BOSH](http://bosh.io) can have a steep learning curve but if you have been following along you should now at least know what BOSH is and how to use it to deploy software. You should also be aware of most of the features that make BOSH so cool and help make your life easier. Just to be sure I'll list some of the cool things about BOSH here:

*   _Deploy software without knowledge about the software_: BOSH releases are basically bundles of software and knowledge. Using a BOSH release means using the knowledge of the author of the release. Because of this you won't have to learn all the details of the software you want to run. BOSH will do magic and the you can do yours.
*   _BOSH takes care of the software after initial deployment:_ This means that when something goes haywire during the night you won't be woken up. BOSH has got you covered.
*   _OS updates are a breeze:_ Whenever a new stemcell is deployed all the old system disks will simply be removed and replaced with a new disk. This makes OS upgrades very easy for BOSH users.
*   _It also makes your machines more secure_ because any malware that may have ended up on a system disk is now gone. Also, stemcells are released very regularly so there is no excuse not to have the latest patches on your machines.
*   _Everything is code_. All configuration is text based, deployment manifests are text, release sources are text, everything is text. More specifically: everything is yaml. This makes putting everything in source control very easy. Which in turn makes automating and pipelining everything very easy.

Which is what we'll dive into soon. Keep an eye on the [BOSH Rocks! channel](http://bosh.rocks) to learn more!