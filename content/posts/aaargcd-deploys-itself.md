+++
title = "AaargCD Deploys Itself"
date = 2023-09-18T15:39:21+02:00
images = []
tags = []
categories = []
draft = false
+++
In this post I continue to build out my own CD tool to make it a viable CNCF member and a valuable DevOps tool. 

As you know, every good DevOps tool needs to be able to install itself. So that's what I worked on lately. For now this only involved changing the "cli tool". This means that the operator won't be able to install itself. This is because the pod that is started by the cronjob that is created by the operator when you create an aapp resource only gets admin permissions within it's own namespace. Hence it will be unable to assign cluster wide permissions to anything it deploys. Which is needed when deploying the operator.

I didn't want the "cli" to now always create jobs with cluster wide admin permissions so I made some changes there. First of all I changed from "unnamed" parameters to using flags. Here is how you'd use the cli now: 

```
AaargCD.sh [-s <namespace>] -n <name> -r <repo url> -f <repo folder>
Use -k to use kustomize instead of plain kubectl apply
Use -a to give the job clusterwide admin permissions
```

I hope you didn't use it in any scripts or pipelines because you'd have to change everything. But then again, if you're in the kubernetes space you're probably used to that anyways ;)

To use it to deploy the operator just run this: AaargCD.sh -s default -n aaargcd-operator -r https://github.com/vChrisR/AaargCD-operator.git -f config/default -k -a

Now, for everyone who thinks all this AaargCD stuff is rather silly. Sit tight, I'll get back to you shortly :)
