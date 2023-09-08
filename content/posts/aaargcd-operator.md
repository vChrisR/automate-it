+++
title = "Aaargcd Operator"
date = 2023-09-07T13:41:10+02:00
images = []
tags = ['aaargcd', 'kubernetes']
categories = ['kubernetes']
draft = false
+++
*Disclaimer: Don't run this operator in a production cluster! Ever!*


In my previous blog post I introduced you to [AaargCD](https://github.com/vChrisR/AaargCD). Now that we have a single line of bash ru.. I mean now that we have a kubernetes app that's going to change the world we need to make it "kubernetes native". I'm not sure why exactly but it's what the cool kids do so let's do this :).

So I wrote an [operator](git@github.com:vChrisR/AaargCD-operator.git). Because that's what it means to be kubernetes native apparently. This was my first time writing an actual operator. I do have some experience writing Go code for kubernetes. I did a [mutating webhook](https://github.com/orangeglasses/k8s-mutate-registry) a while back and also build k8s support into our [smoketest](https://github.com/orangeglasses/cf-smoketests). So yeah... "trust me bro ;)"

After you have applied the CRDs and installed the AargCD-operator you don't have to write all that pesky yaml anymore. Of course there is some yaml you'll need to write but it is much simpler now:
```
apiVersion: aaargcd.io/v1alpha1
kind: Aapp
metadata:
  name: aaargdemo
spec:
  repo: https://github.com/argoproj/argocd-example-apps.git
  folder: guestbook
```

See? We went from 39 lines of yaml to just 7 lines of yaml to create a resource of kind "Aapp" (short for AaargCD App). Much better! Not that we ever had to write the original yaml to begin with because we have a  CLI but still, this is much better right?

## Installing the operator
This is a first version so for now here is how you deploy it:
- Clone the git [repo](git@github.com:vChrisR/AaargCD-operator.git)
- cd into the repo
- run: `kubectl apply -k ./config/default`

As per the usual this first version won't run in a air gapped environment. You'll have to wait a few versions before I give you that ability.

## Using the operator
To try out the operator just run `kubectl apply -f example.yml`. It should automatically create a CronJob, an SA and a RoleBinding. And within 5 minutes you should have a running guestbook deplyoment.
