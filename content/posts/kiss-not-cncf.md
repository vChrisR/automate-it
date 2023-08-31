+++
title = "Kiss! instead of the next fancy CNCF project"
date = 2023-08-31T19:34:18+02:00
images = []
tags = []
categories = []
draft = false
+++
If you're using kubernetes on a daily basis or even if you're following the CNCF stuff from a distance you're probably aware of the vast amount of projects that is part of the CNCF. There seems to be a project for anything you can think of.
On one hand that's a good thing. It means there is choice and competition. 
On the other hand, the choice might not be so great since a lot of the projects are developed by companies that had a very specific need and it is not applicable to most other companies. Also the fact that there is competition means that inevitebly a lot of project are going to disapear rather sooner than later. What if you just decided that project was a good fit for you? And you did a lot of engineering around that project...?

Another aspect of competition is that projects try to stay relevant. How do they usually do that? By adding features so more "customers" will start using their product. If you started using the product early on because it did that one thing you neded very good you're now forced to either manage a product that is growing in complexity or look for something else. Both of which aren't very good options since they both have the tendency to sink a lot of your time. And since this already solved a problem you're now spending time on something that was already solved instead of spending it on new developments or wathcing netflix.

But if you wantend to accomplish something simple in the first place wouldn't you have been better off using the tools that kubernetes natively provides and one or a few lines of bash? Let's take Continues Deployment as an example. You could not think about it and just throw ArgoCD on your cluster. It works, looks kinda cool, will likely be around for a while and there is many people using it. So don't get me wrong, I have nothing against ArgoCD. But if all you want is just apply some yaml in a few namespaces and keep it in synch with a git repo you might be better off using a cronjob and one line of bash. 

I spent a few minutes today to try that out and here is the result:

```
--
apiVersion: batch/v1
kind: CronJob
metadata:
  name: aaarg
spec:
  schedule: "*/5 * * * *"
  successfulJobsHistoryLimit: 4
  failedJobsHistoryLimit: 4
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: cd
            image: bitnami/kubectl
            imagePullPolicy: IfNotPresent
            command:
            - /bin/sh
            - -c
            - cd /tmp;git clone $repo repo; cd repo/${folder}; kubectl apply -f . 
            env:
            - name: repo
              value: https://github.com/argoproj/argocd-example-apps.git
            - name: folder
              value: guestbook
          restartPolicy: Never
---
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: aaargcd
subjects:
- kind: ServiceAccount
  name: default
roleRef:
  kind: ClusterRole
  name: admin
  apiGroup: rbac.authorization.k8s.io
```

I called this "AaargCD" (because everything kubernetes makes you "Aaarg!") and you can find it [here](https://github.com/vChrisR/AaargCD).

Of course it comes with it's own cli as well because all CNCF project come with their own cli. It is in the git repo and is called "AaargCD.sh". Here is how it works:

```
./AaargCD.sh <app name> <git repo url> <folder inside git repo>
```

As with any brand new cloud-native project this is only half baked. The only thing the CLI can do is create new stuff for you. Deletion is yet to be implemented.

But all joking aside, this took me mere minutes to write (copy/paste I mean) and I wrote most of it on my phone. And depending on what you want this might get you 60% of the way there. Spent a bit more time on it and it might get you to 80% and you can usually just drop that final 20% so then you're done ;). And all that without having to rely on somebody elses project which in itself needs to be kept up to date, comes with yet another cli and another learning curve and another operator with its corresponding CRDs. In other words: Keep it stupid simple wherever you can. Don't just grab the next fancy CNCF project to fix a tiny problem.

Speaking about operatorsi and cNCF: each CNCF project ssems to come with their own operator. Maybe AaargCD needs one too....  but that's a topic for another post :)

 
