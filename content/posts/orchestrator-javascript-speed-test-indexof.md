---
title: 'Orchestrator Javascript speed test: IndexOf()'
date: Thu, 08 Jan 2015 13:12:35 +0000
draft: false
tags: ['JavaScript', 'JS', 'Performance', 'Uncategorized', 'vRealize Orchestrator (vCO)', 'vRO']
---

As you might know Javscript is the scripting language used in vRealize Orchestrator. So while I'm not a web developer  I  use a lot of Javascript. When handling arrays in my scripts I tend to use a lot of prototype functions like .map, .forEach, indexOf and a couple others. But when I go through the library workflows I see a lot of of for each loop with some ifs and a break instead of the prototype functions being used. I have some opinions on this which I will share later. For now I was just wondering which method is faster, using the prototype functions or using your own loops. To answer this question I decided to do some speeds tests. This is the first post about these tests: the Orchestrator Javascript speed test: indexOf()

Setting up the test
-------------------

To be able to measure a difference in performance I needed a significantly large array. I settled on an array with 100.000 elements as this seemed to take enough time to loop through to see some actual performance difference between different methods. I executed the tests on a vCO 5.5.2 Virtual Appliance running on my laptop. So if you run the appliance on a faster machine you might need a bigger array. I used this script to create the array:```
var testArray = new Array();

for (var i = 0; i < 100000;i++) {    
    testArray.push(i.toString());
}
```The actual speed tests are in the same workflow as the array generation script. This way both tests are ran as close together as possible to ensure the same circumstances for both tests. [![indexof-flow](http://www.automate-it.today/wp-content/uploads/2015/01/indexof-flow-300x43.png)](http://www.automate-it.today/wp-content/uploads/2015/01/indexof-flow.png)

Finding the index of a value
----------------------------

Imagine you have an array and you want to figure out in which element a certain value is stored. There are two ways to do this. The easiest is using the .indexOf() prototype method. Alternatively you could use a for each (..) loop. To find out which method is the fasted I generated an array with 100k elements. the value in each element is the string representation of the index number. On the array I executed the code below:```
System.log("=====Starting Index Test=======");

var startTime = System.getCurrentTime();

var index = testArray.indexOf("99999");

var stopTime = System.getCurrentTime();
var timeElapsed = stopTime - startTime;

System.log("Index found: " +index);
System.log("Start Time: " +startTime);
System.log("Time finished: " +stopTime);
System.log("mS elapsed: " +timeElapsed);
System.log("=======Index test Finished=======");
```This piece of code searches for the value "99999" in the array elements. We already know that is the very last element of the array so this measures how long the function takes to loop through the whole array while still validating that the actually works correct. Below is the result of this script.```
Start Time: 1420720777704
Time finished: 1420720777756
mS elapsed: 52
```So the total time elapsed for the indexOf() method is **52 milliseconds**. Let's compare this to a for each loop.```
System.log("=====Starting for each Test=======");

var startTime = System.getCurrentTime();

var index = 0;
for each (var e in testArray) {
    if (e == "99999") {
        break;
    }
    index++;
}

var stopTime = System.getCurrentTime();
var timeElapsed = stopTime - startTime;

System.log("Index found: " +index);
System.log("Start Time: " +startTime);
System.log("Time finished: " +stopTime);
System.log("mS elapsed: " +timeElapsed);

System.log("=======for each test Finished=======");
```And here are the results:```
Start Time: 1420720779772
Time finished: 1420720779938
mS elapsed: 166
```So this run took **166mS**. Which is more than **3 times** slower than the .indexOf() prototype method.

Conclusion
----------

Not only did I have to write more code to achieve the same result, the execution of the code also takes more than 3 times longer to execute the code. Obviously if you hit the target earlier in the array or use a smaller array the difference would be smaller. Still, It doesn't make sense to write more code that is slower, harder to understand and not maintained by the software vendor. So please: use the .indexOf array prototype method when searching the index for a specific value.