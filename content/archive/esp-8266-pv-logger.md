---
title: 'ESP8266 PV Logger'
date: Fri, 10 Apr 2015 08:00:33 +0000
draft: false
tags: ['Arduino', 'ESP8266', 'mastervolt', 'pv', 'Robotics and Electronics', 'soladin', 'solar panels']
---

I have written about the ESP8266 before in my post about [the magic button](http://automate-it.today/magic-button/). For the button I am using the [nodeMCU](https://github.com/nodemcu/nodemcu-firmware) firmware which let's you run lua script on the ESP. But recently the people over at [esp8266.com](http://www.esp8266.com/viewtopic.php?f=31&t=2150) created an [Arduino IDE](https://github.com/esp8266/arduino) version which is compatible with the ESP8266. I was already using an ESP connected to an Arduino to log the output of my solar panels so I decided to try and run this code natively on the ESP. And it worked, so although this is not automation or virtualization related, here are some more details about my ESP8266 PV Logger.

Library
-------

The logger connects to my [Mastervolt Soladin 600](http://www.mastervoltsolar.com/solar/products/soladin-600/soladin-600-160-700-wp-model-for-use-in-the-netherlands/). This inverter has an RS485 port but connecting it to a TTL serial port is easy. All you need is two resistors. For the schematic see the [readme on github](https://github.com/vChrisR/SolaDin). I have been monitoring my PV panels for years using [this library](https://github.com/teding/SolaDin). That lib only works with software serial ports which are not supported by the ESP so I changed that to use the Stream class instead. Both Software Serial and Serial are inherited from Stream so now it should work with both on a normal arduino but I haven't tested it with the software serial. I also changed the serial delay and timeout settings to get reliable communication between the ESP and the Soladin. You can find the updated lib [here](https://github.com/vChrisR/SolaDin).

Log Destination
---------------

There are a couple places you could send your logging data to. Until today I was using [data.sparkfun.com](http://www.data.sparkfun.com) and that worked fine. But it's lacking nice grapical presentation of you data. And I'm too lazy to build my own so I looked for something else. Now I am using [thingspeak](http://www.thingspeak.com) and that seems to work just a s well with the added bonus of nice graphs and google gauge visualisation. You can find my stats [here](https://thingspeak.com/channels/27609).

Logger Code
-----------

The logger code is pretty easy. It tries to connect to the Soladin, if that succeeds it sends data to thingspeak. This is repeated every 15 seconds. If it can't make a connection to the Soladin the loop slows down. You can find the whole code [here on github](https://github.com/vChrisR/esp8266-pv-logger). Don't forget to compile it with [arduino 1.6.1-esp8266-1](https://github.com/esp8266/Arduino/releases) instead of the regular arduino IDE.

Hardware
--------

All you need to run this is a 3.3volt power supply, two resistors, a cable with an RJ11 connect and a ESP-01. Mince looks like this. [![ESP8266 PV logger](http://www.automate-it.today/wp-content/uploads/2015/04/2015-04-06-16.07.53-300x169.jpg)](http://www.automate-it.today/wp-content/uploads/2015/04/2015-04-06-16.07.53.jpg) The best part? The whole thing costs about 5 bucks! That's a lot cheaper than Mastervolt's PC-Link cable.