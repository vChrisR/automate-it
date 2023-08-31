---
title: 'Meet the Automate-IT Robot'
date: Fri, 04 Jul 2014 14:11:48 +0000
draft: false
tags: ['3d print', 'Arduino Yun', 'openSCAD', 'ORchestrator', 'Passion for Technology', 'Robotic arm', 'robotics', 'Robotics and Electronics', 'thingiverse', 'vRealize Orchestrator (vCO)']
---

Time for a less serious friday afternoon post. Meet our new friend: The Automate-IT Robot a.k.a. r2vco2. It's a robotic arm with a special feature: It can be controlled by vCenter Orchestrator. [![Robotic Arm](http://automate-it.today/wp-content/uploads/2014/07/2014-07-03-16.47.25-300x168.jpg)](http://automate-it.today/wp-content/uploads/2014/07/2014-07-03-16.47.25.jpg) When I was preparing for the yearly ITQ Technical update session I was thinking of a way to show vCenter Orchestrator can do much more then just automating vSphere tasks. What better way to show this that control something physical from a tool that's usually used in the virtual world? So I decided to try and build a robotic arm.

The Bones
---------

The bones of the arm are 3d printed using an [Ultimaker Original.](https://www.ultimaker.com/pages/our-printers/ultimaker-original) Of course the type of printer isn't that interesting. But what is interesting are the 3d models I used. Thanks to [thingiverse](http://www.thingiverse.com/) it only took me a few minutes to find a 3d printable robotic arm. I also foudn some modifications to the original design which I ended up using. Here a the links to the different projects I used:

*   Original micro robotic arm: [http://www.thingiverse.com/thing:34829](http://www.thingiverse.com/thing:34829)
*   Redesign in openSCAD: [http://www.thingiverse.com/thing:65081](http://www.thingiverse.com/thing:65081)
*   Parts for MG995 servos: [http://www.thingiverse.com/thing:38875](http://www.thingiverse.com/thing:38875)

I recommend using the openSCAD version of the design. I initially used parts of the original design but the gripper didn't work for me. So I replaced the top arm and the gripper for the openSCAD parts. Also I needed stronger servos for rotation and the lower arm so I used the parts modified for MG995 servos. So the only part I used from the original thingiverse project is the middle part of the arm. ![Gripper](http://automate-it.today/wp-content/uploads/2014/07/2014-07-03-16.48.19-300x168.jpg)

The Muscles
-----------

The robot's muscles consist of  5 hobby servos. Those are the same kind found in RC cars and airplanes. For the rotation and the lower arm I used the TowerPro MG995. You can find them on e-bay for a few bucks. The other servos are the very small 9g servos. This means that they weight only 9 grams which is a good thing considering they have to be lifted by the lower servos. Oh, and you;ll find them really cheap on e-bay as well. All the servos can draw a considerable amount of power from the power. A little over 3 Amps if all motors are at full power. This cannot be supplied by a micro controller or a USB port so I build a simple power supply. It consists of just one voltage regultor  ([LM123K](http://www.google.nl/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&ved=0CDMQFjAA&url=http%3A%2F%2Fwww.alldatasheet.com%2Fdatasheet-pdf%2Fpdf%2F8618%2FNSC%2FLM123K.html&ei=Mqe2U-joKrSc0wXj1IEo&usg=AFQjCNFhSfVG7XS_hS3BIuL9p1hp88JeEQ&sig2=7znqCx1_w2si7jcVglhM3g&bvm=bv.70138588,d.d2k)) and two tiny capacitors. The LM123 converts 12 volts down to 5 volt and can deliver up two 3 Amps. It doesn't need cooling to do this and the thing turned out to be fool proof. I hooked it up the wrong way a few time but it still lives. \[caption id="attachment\_427" align="alignnone" width="300"\][![Voltage regulator on top of Arduino Yun](http://automate-it.today/wp-content/uploads/2014/07/2014-07-03-16.48.01-300x168.jpg)](http://automate-it.today/wp-content/uploads/2014/07/2014-07-03-16.48.01.jpg) Voltage regulator on top of Arduino Yun\[/caption\]

The Brain
---------

An [Arduino Yun](http://arduino.cc/en/Main/ArduinoBoardYun) is the physical part of the robot's brain. The Yun is a development board that has an AVR micro controller and an SoC running openWRT in one package. It is equiped with LAN, WiFi and an micro SD Card slot. The cool thing is that the Linux part of the board runs a webserver which can be used from the AVR part of the board. Arduino provides a bridge library to aid communication between Linux and AVR. Using this library it is relatively easy to build a web API on top of your micro controller code. And that is exactly what I did. The software for the brain is a rather simple sequence recorder/playback device. So I can program positions into steps which are put into a sequence. Everything is controllable via a REST like API. I used the API to create a very simple AJAX web interface which is served by the linux part of the Yun as well. But I didn't stop there. I created a few vCO workflows which are able to control the robot. The vCO REST Plug-in makes this very easy. I'll write more about the software and the vCO integration in a follow up blog.