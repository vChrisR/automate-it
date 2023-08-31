---
title: 'Orchestrator JavaScript speed test: .map()'
date: Wed, 25 Feb 2015 12:15:33 +0000
draft: false
tags: ['JavaScript', 'map()', 'Performance', 'vRealize Orchestrator', 'vRealize Orchestrator (vCO)', 'vRO']
---

A while ago I wrote a [blog post](http://www.automate-it.today/orchestrator-javascript-speed-test-indexof/) in which I showed the performance difference between the array prototype function .indexOf() and a for each loop. Now it's time for the second part of the series: Orchestrator Javascript speed test: .map()

Test setup
----------

The test setup is identical to the setup I described in the [previous post](http://www.automate-it.today/orchestrator-javascript-speed-test-indexof/). Same machine, same vCO appliance. I did change the script that generates the test array slightly. Instead of a string I now store an object in each array elemen. Here is the script:```
System.log("======Generating Test Array===========");

var testArray = new Array();

var startTime = System.getCurrentTime();
for (var i = 0; i < arraySize;i++) {
    var element = {
        number: i.toString(),
        value: "value=" +i.toString()
    }    
    testArray.push(element);
}
var stopTime = System.getCurrentTime();

var timeElapsed = stopTime - startTime;
```Both tests are done in the same workflow so they are as close together as possible to get the same circumstances of the tests. [![map vs for each workflow](http://automate-it.today/wp-content/uploads/2015/02/Screenshot-from-2015-02-25-131444-300x31.png)](http://automate-it.today/wp-content/uploads/2015/02/Screenshot-from-2015-02-25-131444.png)

 Mapping an Array
-----------------

Mapping an array into an other array means running some action on every element of the array and returning the value of that action into a new array. What I will do in this test is taking one attribute of the object that is stored in each array element and create a new array that only consists of that one attribute. This makes it easier to  search for the right index number later on using .indexOf()

*   So here the content of one array element: { number: "1", value: value=1 }
*   And what we want as an end result is an array where each element just contains "value=1" for example.

There are basically two ways to do this. You can either use the prototype function .map() or create your own loop. Let's try the prototype function first.  map() takes takes a function as an argument. Whatever the function returns is stored in the active element of the target array.```
var startTime = System.getCurrentTime();

var mappedTestArray = testArray
    .map(function(e) { 
        return e.value 
    });

var stopTime = System.getCurrentTime();
var timeElapsed = stopTime - startTime;

System.log("Start Time: " +startTime);
System.log("Time finished: " +stopTime);
System.log("mS elapsed: " +timeElapsed);
```Below is the result of this test:```
Start Time: 1424793480446
Time finished: 1424793480540
mS elapsed: 94
```So the map action took 94 milliseconds. But over a couple of test runs I did get different results. Ranging from 119 to 86mS. Now let's try a for each loop to see how long that takes:```
var startTime = System.getCurrentTime();

var mappedArray = new Array();

for each (var e in testArray) {
    mappedArray.push(e.value);
}

var stopTime = System.getCurrentTime();
var timeElapsed = stopTime - startTime;

System.log("Start Time: " +startTime);
System.log("Time finished: " +stopTime);
System.log("mS elapsed: " +timeElapsed);
```And here are the results:```
Start Time: 1424793484701
Time finished: 1424793484840
mS elapsed: 106
```So this particular run took 106 milliseconds. But again as with the .map I don't get consistent results. I've seen values everywhere between 82 and 139 mS. I run both test sequentially in the same workflow. And even the difference between both methods is not the same. Sometimes the map() is faster, sometimes the loop is faster.

Conclusion
----------

I cannot definitively say which method is faster. The only thing I can say for sure that they are about the same speed. But if you ask me which method I prefer the answer is: .map()! Why?  Because if I read somebody elses code and I see the .map being used I know something is being mapped. But if I see a pof each loop I have to go through the whole loop to understand what's going on. In the example above the loop might be simple but in real life it can get complicated pretty quick.