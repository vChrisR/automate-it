---
title: 'Stack Elevator - Level 5: Buildpacks and Apps'
date: Tue, 24 Apr 2018 10:00:51 +0000
draft: false
tags: ['Boston', 'CF summit', 'CF Summit Boston 2018', 'cfmiauw', 'Cloud Foundry', 'Cloud Foundry', 'PiFoundry', 'Raspberry Pi']
---

This is the 7th post in a series about porting Cloud foundry to Raspberry PI. We took the elevator and went down to the lowest level: the phyiscal world. After an interesting elevator ride we now arrive at level 5 at which we'll find the acutal things running on top of the platoform: buildpacks and applications.

buildpacks
----------

am sure there is a lot to explain about buildpacks. But in relation to our efforts to port CF to ARM there isn't a whole lot to talk about. The rest of the porting effort took so much of our free time that we didn't want to invest any more at this point. So we just use the binary buildpack for our demos. If we ever continue our efforts to maybe run CF on a more mature ARM platform we will definitely get back to this.

Apps
----

This is what this porting effort was all about: Running Apps on CF running on a Raspberry PI. It took over 3 months to get here but we did it! At CF Summit 2018 in Boston we did live demos. Watch them in the video below. https://www.youtube.com/watch?v=LOuXzLfvuEA