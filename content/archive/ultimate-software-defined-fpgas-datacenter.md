---
title: 'The ultimate software defined – FPGAs in the datacenter'
date: Tue, 24 Jan 2017 10:30:51 +0000
draft: false
tags: ['accelerators', 'big data', 'datacenter', 'FPGA', 'Robotics and Electronics', 'VHDL']
---

Recently I have been playing around with this thingy: [![](http://cdn1.bigcommerce.com/server3200/wgbxee/products/154/images/677/102010023_1_52450.1426027875.1280.1280__48264.1456764957.1280.1280.jpg?c=2)](http://store.gadgetfactory.net/papilio-duo-2mb-sram-arduino-compatible-fpga-dev-board/)what is it and why are you blogging about it you might wonder. Well… it’s an FPGA development board.

A what?
-------

Yes. An FPGA development board. I’m aware that a lot of people in IT infrastructure probably never heard about FPGA’s or maybe they heard about it but don’t really know what they are. Let me explain what it is before I dive into why I’m experimenting with it. FPGA stands for Field Programmable Gate Array. It is basically a chip which contains a collection of digital gates. So AND gates, OR gates, NOT gates and every thinkable combination thereof. The gates are not connected to each other by default but the chip can be configured to have the gates connected in whichever way you like. Thereby creating any kind of functionality you like. In short: An FPGA is a programmable (or reconfigurable) chip. So it is possible to have an FPGA behave like a processor, or a display driver, or an S/PDIF receiver, or a bitcoin miner or… well you get the point. It’s a chip that’s not really a chip until you program it to be any chip you like. And I use you the word “program” loosely here because I’m still not sure this can be actually called programming. But for lack of a better word I’ll call it programming for now. FPGAs are programmed using a hardware description language. The two main ones are Verilog and VHDL. Since VHDL seems to be the dominant language in Europe I started out to learn VHDL. This has been very interesting for multiple reason. Maybe I’ll go into some of those in another blog post but for now I want to highlight the fact that with these languages you are actually describing hardware, not software. The code you write determines what the chip will look like inside. And if you’ve tested this on an FPGA you can even send the code to a chip fab and have your own silicon created. How cool is that? :) So in a way FPGA can be called software defined hardware.

SDH?
----

Can hardware be software defined? Yes I think so. Software defined means generic hardware performs a specific function just by loading the right software and it’s behavior can be altered by software. All this is true for FPGAs. In my opinion FPGA are the ultimate software defined devices. FPGAs have been used in products for years now. you’ll find them in FusionIO (or whatever they’re called now) cards, digital sound consoles, LED displays, Audio DACs and lots of other devices which need real time, parallel processing.

FPGAs in the datacenter
-----------------------

But recently the FPGA has left it’s world of “devices” and can also be found in the datacenter. And that is exactly why I’m so interested in these amazing pieces of technology. In the last couple years a lot of things have been happening in this field:

*   In 2015 Intel acquired Altera, one of the 2 (xilinx being the other one) major FPGA manufacturers
*   Also in 2015 Microsoft started using FPGAs on a large scale for Bing
*   Last year microsoft equipped all Azure physical machines with FPGAs
*   Last year Amazon started offering the “F1 instance” An amazon machine with access to an FPGA

to me this shows that FPGAs are here to stay nd I think they will play an important role in the datacenter of the not so distant future.

**What are FPGAs used for?**
----------------------------

The main purpose of an FPGA in a server is to offer a reconfigurable accelerator. A lot of algorithms that can be parallelized can benefit from hardware acceleration. In the past it was often not feasible to make a purpose build accelerator for one algorithm because this would make the server it was placed in a single purpose server. Also tweaking the algorithm after the hardware is produced is impossible. Since FPGAs are reconfigurable you can keep all your hardware identical while providing dedicated acceleration hardware. Acceleration in hardware is becoming more and more important since the end of Mores Law is in sight but the amount of data that needs to be processed is increasing faster than ever. FPGAs are one of the ways to keep up with the processing demand without adding more CPUs. On top of that, FPGA are a lot a lot more energy efficient than traditional CPUs. Currently FPGAs are mostly used in big data analytics and real time processing. Another area where FPGAs could become popular in the near future is in network function virtualization. Imagine if VMware NSX had access to FPGAs in your servers. It would be able to offload a lot of networking features from the CPUs. Just like current network vendors are using dedicated networking chips or even FPGAs in their networking equipment, NSX would have dedicated networking hardware at its disposal and it would still be 100% softwre defined. All while decreasing latency, increasing throughput and lowering the CPU overhead.