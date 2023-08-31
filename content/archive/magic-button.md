---
title: 'The Magic Button'
date: Mon, 23 Mar 2015 11:30:36 +0000
draft: false
tags: ['AMQP', 'ESP8266', 'IoT', 'RabbitMQ', 'Robotics and Electronics', 'vCO webEye', 'vRO', 'What does this button do', 'Wifi']
---

On March 19th we used The Magic Button ( a.k.a "The What Does This Button Do Button") in our demo's at the Dutch VMUG UserCon. It magically  made a CoreOS cluster appear out of nowhere, Launched our demo app and then it scaled it out so all people in the room could open the page. Of course you want to build your own now. Here is how. [![IMG-20150210-WA0001](http://www.automate-it.today/wp-content/uploads/2015/03/IMG-20150210-WA0001-300x168.jpeg)](http://www.automate-it.today/wp-content/uploads/2015/03/IMG-20150210-WA0001.jpeg)

Hardware
--------

The button itself is just a regular emergency stop button I got off e-bay (6$). Inside there is enough space for a battery holder with 2x AA batteries. These batteries power an ESP8266-01 board. The ESP8266 is a Wifi SoC, has a 80Mhz processor, wifi connection, 96KBytes of RAM, a couple of GPIOs, comes with SPI flash on the board, costs around 5$ and looks like this: [![ESP8266_Wi-Fi_Module](http://www.automate-it.today/wp-content/uploads/2015/02/ESP8266_Wi-Fi_Module-300x295.jpg)](http://www.automate-it.today/wp-content/uploads/2015/02/ESP8266_Wi-Fi_Module.jpg) The chip has a UART and was originally intended to function as a serial to wifi module. Out of the box it comes with an awkward AT firmware (hello 1990!). But thanks to a very active [community](http://www.esp8266.com/) we can now build ourfirmware for this neat little chip. I don't have the time or the knowledge to write my own firmware in C++ but luckily someone created a [firmware](https://github.com/nodemcu/nodemcu-firmware) for this chip that let's you run [Lua](http://www.lua.org/) code on it! I didn't know any Lua before but it turns out to be rather easy. Since it's an event driven interpreted language it has some commonalities with Javascript, which I am very familiar with. Here is how I connected the board:

*   Connect the button between GPIO0 and Ground
*   Connect the LED between GPIO2 and Ground. I used a 100Ohm resistor to limit the current through the LED
*   Put a 1K pullup resistor between VCC and CH\_PD
*   Batteries are directly connected to VCC and GND. No Caps or regulators.

[![The magic button internals](http://www.automate-it.today/wp-content/uploads/2015/02/2015-02-26-21.51.37-300x168.jpg)](http://www.automate-it.today/wp-content/uploads/2015/02/2015-02-26-21.51.37.jpg) When everything is connected you can squeeze it all into the case. It actually doesn't really fit. When I close the case the battery gets damaged a bit. But whatever, it works.... [![The MAgic Button Squeezed](http://www.automate-it.today/wp-content/uploads/2015/03/2015-02-26-21.50.45-300x168.jpg)](http://www.automate-it.today/wp-content/uploads/2015/03/2015-02-26-21.50.45.jpg)

Software
--------

So How did I turn this wifi board into a magic button? The button simply does an HTTP POST to my [webEye](https://github.com/vChrisR/webEye) application. This application forwards the posted body to an AMQP bus where it get's picked up by vRealize Orchestrator. vRO in turn runs a workflows which actually performs the magic. To enable your board to do the same, follow these steps:

*   Setup [webEye](https://github.com/vChrisR/webEye) or another webhook receiver to test the button
*   Flash this firmware on the chip: [nodeMCU](https://github.com/nodemcu/nodemcu-firmware)
*   Use [ESPlorer](http://esp8266.ru/esplorer/) or another tool to load these two Lua files: [https://github.com/vChrisR/TheMagicButton](https://github.com/vChrisR/TheMagicButton).
*   Please edit the variable at the top of the files before copying to your ESP
*   Emergency stop buttons are normally closed. So make sure the button is pressed (open) when you power up the ESP. If you don't do that it will keep GPIO0 low which makes it boot into bootloader (flash) mode.

Now build a cool workflow which you can trigger with this button. Share your creations in the comments or find me on twitter.