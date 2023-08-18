---
title: 'vCO LockingSystem'
date: Wed, 06 Aug 2014 10:00:14 +0000
draft: false
tags: ['external resource', 'hostname', 'ip', 'Locking', 'Locking Mechanism', 'LockingSystem', 'vRealize Automation (vCAC)', 'vRealize Orchestrator', 'vRealize Orchestrator (vCO)']
---

Whenever you are using external resources in vCO you might run into a race condition. This can happen when a workflow using an external resource is running multiple times simultaneously. To avoid data consistency problems you can use the vCO LockingSystem.

Why lock?
---------

Imagine you have a vCO workflow which reads a counter from a web API, then changes the data, lets say add 1 to the counter, and writes it back to the API. What happens if you start the workflow multiple times simultaneously? You could run into a situation where one workflow reads the data and modifies it but has not yet written it to the API, then the second workflow reads the not yet updated data and modifies it. Then the javascript engine decides to execute the API call in the first workflow and then the API call in the second workflow. Now both workflows are using the same counter value while they were supposed to get a unique value from the API. A real life example for this is retrieving a hostname sequence number from a vCAC hostname prefix. This process involves reading the current number from the vCAC API, increasing the number and then sending the new number back to the API. Btw: why the api itself does not handle the increase of the number remains a mystery to me. To prevent above scenario from happening you want to lock the resource before reading and updating it so any other action using the resource has to wait until you are done.

Using the LockingSystem
-----------------------

Locking in vCO is done using the LockingSystem scripting object. To acquire a lock you use:```
LockingSystem.lock(lockid, owner)
```This method tries to acquire the lock once and then returns a boolean which tells you if the lock was acquired or not. If you want the workflow to wait until the lock is acquired you can use```
LockingSystem.lockAndWait(lockid, owner);
```The parameters _lockid_ and _owner_ are strings. The lockid identifies the object you are trying to lock. So just use a name that makes sense. The owner can be anything, I usually use the name of the workflow which is acquiring the lock. Below is an example of a scriptable task which acquires a lock. Note that this script will wait until the lock is acquired. [![LockingSystem.lockAndWait example](http://automate-it.today/wp-content/uploads/2014/08/Screenshot-from-2014-08-05-214400-300x171.png)](http://automate-it.today/wp-content/uploads/2014/08/Screenshot-from-2014-08-05-214400.png) After the lock is acquired you can get, modify and write data to the external resource. After that you release the lock and start using the data. Below is a screenshot of a workflow that uses locking. [![Locking example workflow](http://automate-it.today/wp-content/uploads/2014/08/Screenshot-from-2014-08-05-214639-300x118.png)](http://automate-it.today/wp-content/uploads/2014/08/Screenshot-from-2014-08-05-214639.png) Remember to release the lock in case something goes wrong. If you don't handle exceptions for the tasks between acquiring the lock and releasing it, you can end-up with a lock that never get's released. If you do end up in that situation you can run a workflow which contains a single script with this line:```
LockingSystem.unlockAll();
```