---
title: 'vRO Code - Calculate CIDR notation'
date: Mon, 22 Aug 2016 10:00:27 +0000
draft: false
tags: ['CIDR', 'JavaScript', 'vRealize Orchestrator', 'vRO']
---

Recently I ran into situations where I needed to supply a network address in [CIDR notation](http://whatismyipaddress.com/cidr) to external systems (infoblox and the oracle GRID installer in my case) from a vRealize Orchestrator workflow. CIDR notation looks like this: 192.168.1.20/24. So instead of specifying the subnet mask using the 4 bytes separated by dots it just tells how many bits are used for the network number. The thing is, all you can get from vRA is the regular subnet mask (255.255.255.0 in this example). Sometimes you can get away with a solution as simple as a few ifs or a switch/case but that's not really the way to properly fix this.  I wanted to solve this once and for all so I wrote some JavaScript code for vRealize Orchestrator that calculates the number of bits in the mask and creates the CIDR network notation for you. Here it is:

*   Inputs for the script object or action:
    *   gateway (String)
    *   subnetMask (String)
*   Output:
    *   cidr (String)

```
//Split the 4 parts of the subnetmask into an array
var maskParts = subnetMask.split(".");
var numberOfBits = maskParts
    //For each part calculate the number of ones
    .map(function(maskPart) {
        var numberOfBits = parseInt(maskPart, 10).toString(2).replace("0","").length;        
        return parseInt(numberOfBits, 10);        
    })
    //count the number of ones in the whole array
    .reduce(function(previous, current) {
        return previous + current;
    });
    
//Split the 4 parts of the ip into an array
var ipParts = gateway.split(".");
networkNumber = ipParts
    //Do a bitwise AND between each ip part and the match subnetmask part to get the network number
    .map(function(ipPart, index) {
        return parseInt(ipPart, 10) & parseInt(maskParts\[index\], 10);
    })
    //join array back together in dot notation
    .reduce(function(previous, current) {
        return previous + "." + current;
    });

//Create the CIDR notation
cidr = networkNumber + "/" + numberOfBits;

//change line below to: return cidr
//if you're putting this in an action
System.log(cidr);
```This script generates the network number but it can easily be adapted to return the ip address itself in CIDR notation.