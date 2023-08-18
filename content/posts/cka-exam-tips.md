---
title: 'Certified Kubernetes Admin (CKA) exam tips'
date: Fri, 31 Jan 2020 08:25:33 +0000
draft: false
tags: ['Certified Kubernetes Administrator', 'CKA', 'Exam', 'ITQ', 'Kubernetes']
---

I recently passed the certified Kubernetes Admin exam. Head [here](https://www.cncf.io/certification/cka/) for more info on the program. The CKA exam is a live lab exam and I heard from quite a few people that they struggled to complete all tasks within the 3 hours you're allotted to finish the exam. Since I finished the exam early (29 minutes left on the clock) I thought I'd give some hints that will save you some time during the exam. So here goes.

Know your stuff
---------------

Obviously :). You are allowed to use the docs on kubernetes.io during the exam but you won't have time to learn new stuff. It's ok to lookup specific details on how to configure certain objects but anything beyond that will probably cost you too much time.

### Linux Foundation study materials :(

To study I started out with the materials provided by the Linux Foundation. I bought the exam + learning materials in a discounted bundle during the holiday period. But unfortunately I have to say that the Linux foundation stuff isn't that good. It will teach you some stuff but I don't think it's sufficient to pass the exam if you start from scratch. Also: It's mainly slides you have to read yourself, not videos. I have a hard time reading dense information on slides for more than a couple hours a day. I just get distracted and can't absorb the information after a few hours, let alone a few days. Another thing that could be better are the lab exercises included in the linux foundation materials. It mostly gives you all the yaml and command lines that you need see you can get away with just copy/pasting that. Not a good way to learn imho.

### Mumshad's course on udemy :)

I can absolutely  recommend Mumshad Mannambeth's "[Certified Kubernetes Administrator With Pratice Tests](https://www.udemy.com/course/certified-kubernetes-administrator-with-practice-tests/)"  course on udemy. Pro tip: Set the playback speed of the videos to 1.5x. That kept me hyper focussed and helps going through the course rather quickly. The course comes with labs on KodeKloud so no need to run up a cloud bill or to have your own home lab to do all the practice exercises. The lab exercises aren't "pre-answered" so you'll have to figure out a lot yourself which helps the learning process. The coruse also contains a few mock exams. I did those on the morning of the real exam and the really helped me picking up the speed I needed for the real deal.  They'll also help you highlight some points where you might need a little more study or practice.

Use an alias
------------

For some this tip might be obvious but if you don't have an extensive linux background you might overlook this one. Linux, or the shell rather, allows you to create aliases. One I always create when working with kubectl is this one:```
alias k=kubectl
```This means you can now just type "k" instead of "kubectl". Might sound trivial but actually saves you a bunch of time. You can create more aliases if you want to. I just use the one above but you could, for example, create an alias for listing deployments:```
alias kgp=kubectl get deploy
```and so on... Just make sure you don't spent more time on creating the aliases in the exam environment than you'll ever save.

Get comfortable with bash and vim
---------------------------------

Chances are you're already familiar with Linux and have used bash or zsh and tools like vim extensively. In that case please skip ahead to the next tip. If you're not yet familiar with working on a linux command line make sure you get a load of airtime with it before attempting the exam. You'll absolutely need to use a text editor in the terminal, no graphical shell available during the exam! So get at least a bit comfortable with vim.

Use kubeCTL create/run
----------------------

As I mentioned before, you're allowed to access the docs on the kubernetes.io site. So when asked to create a certain object in k8s you'll probably want to go to the docs first, find the example yaml, copy/paste it into your editor and change it to your needs. While that's a good way for some objects there is a quicker way especially for often used objects:```
kubectl create <object> <name> <options> --dry-run -o yaml > <filename.yml>
```Let's say you need to create a deployment which uses the nginx image. Just run the command below to get the basic yaml for that deployment.```
kubectl create deployment nginx-deployment --image=nginx --dry-run -o yaml > nginx-deploy.yml
```Now you can open the nginx-deploy.yml and edit it to your liking and then load it into the API by running kubectl apply -f nginx-deploy.yml  For a lot of the tasks the basic yaml might actually be enough. For some objects you can feed in more options into the create command. just run kubectl create --help  or kubectl create <object> --help  for more information. If your task is just to create a pod you can't use the create command. You'll have to use the run command:```
kubectl run --generator=run-pod/v1 <pod name> --image=<image name> --dry-run -o yaml > <filename.yml>
```There are a lot of options you can pass into the run command. I think for most of the tasks that require you to run a pod you can generate the yaml with the run command and a bunch of option flag. However, I usually ended up generating the basic pod yaml and then editting it in vim. Just because searching the right options in the help (kubectl run --help) took more time than just pasting or typing a few lines of yaml.

Use kubectl explain
-------------------

After going through all the training material you'll probably remember a bunch of yaml but chances are you don't know exactly what all the options are, which option is a list or a map and so on. To quickly get some explanation you can run kubectl explain <opject>.<attribute>.<and so on> For example```
kubectl explain pod.spec.containers
```or```
kubectl explain pod.spec.containers.volumeMounts
```For some info if you don't exactly remember certain parts of the yaml this is way quicker than searching the docs.

Search the docs
---------------

More importantly: keep searching the docs till the end. If you come across a task that requires more than just a quick search for an example yaml just skip the task and go back to it later if you have time left. If you can't find the answer in the docs within a few minutes then don't waste more time, just move on to the next task. If you have some time left after finishing the other tasks, by all means, spent all the time you have left on the tasks that you couldn't figure out quickly. The answer is usually in the docs.