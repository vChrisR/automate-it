---
title: 'Stack Elevator - Level 2: Release Department'
date: Thu, 12 Apr 2018 10:00:34 +0000
draft: false
tags: ['BOSH', 'BOSH Releases', 'CF summit', 'CF Summit Boston 2018', 'Cloud Foundry', 'Cloud Foundry', 'Diego', 'ITQ', 'PiFoundry', 'Runc']
---

This is the 4th blog in a series about porting Cloud foundry to Raspberry Pi. In this installment we'll take a look at the BOSH releases involved in the Cloud Foundry deployment and what we had to do to make them deployable to Raspberry Pi.

BOSH Releases involved
----------------------

The following releases are involved:

*   diego
*   garden-Runc
*   cf-networking
*   loggregator
*   bosh-dns

I decided to run CF without Consul so we wouldn't need to touch the consul release. In exchange we needed to touch the bosh-dns release but I expected that to be relatively easy and I turned out to be right for once :).  Also please keep in mind we were not planning on running all of CF on Raspberry, just the Diego Cells.

Changes Made
------------

By the time we got to this stage in the process I thought we were almost done. Just exchange the Go compiler packaged with the releases for the ARM Go compiler and BOSH will compile and run everything just fine. Right? There was that 32bit/64bit compatibility issues thingy looming though but that's a subject for the next blog post. To kinda test the waters first I decided to just start out with the garden-runc release. since garden-runc can also be run standalone and controlled from a CLI, just running garden-runc without the rest of CF made for a much tighter feedback loop. It was also the release I probably spent the most time on in the end. As I said the first change I made was exchanging the Golang compiler package for the ARM Golang compile. I could also have opted for cross compilation but that would have required changes all the packaging scripts as well. Now we just had to replace the golang package. On top of that a lot of releases also include C(++) source code and the C compiler is not packaged with the releases. The C compiler on the stemcell is used. Cross compiling C is a nightmare so I just choose to run the compile jobs on the PI itself. (Actually... there is one release in which we had to cross compile something. I'll get back to that in a blog post or two.) Unfortunately there are also a few binaries packaged with some releases. The diego release has jq and envoy in binary form and the garden-runc release has a docker image which is x86 compatible of course. So we had to swap these out as well. Keep an eye on our [github org](https://github.com/orgs/PiFoundry), I'll be publishing our forks there. With that done we gave the garden-runc release a go.... and what I thought was the final part in the project turned out to be the start of the second half of the project. Meanwhile we had submitted a CFP for a talk about CF running on RPi at CF Summit in Boston. So we kinda had to get it to work :) you'll read how in the next few blog posts. Stay tuned!