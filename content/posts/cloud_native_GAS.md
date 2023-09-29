+++
title = "Cloud-native G.A.S."
date = 2023-09-19T15:04:25+02:00
images = []
tags = []
categories = []
draft = false
+++
I'll start with a bold statement: The cloud-native world is suffering from G.A.S!

## G.A.S?
If you have never heard of G.A.S: it stands for Gear Acquisition Syndrome. Or actually originally it was Guitar Acquisition Syndrome. According to [wikipedia](https://en.wikipedia.org/wiki/Shopping_addiction#Gear_Acquisition_Syndrome_(G.A.S.)) the acronym was coined by Steely Dan guitarist Walter Becker in a 1994 satirical Guitar Player magazine column titled "The Dreaded G.A.S.". It describes the guitarists family room covered entirely with guitars. I personally know a few guitarists and they definitely all seem to suffer from G.A.S. But I think people in many other fields tend to suffer from G.A.S as well. Photographers collecting enormous amounts of cameras and lenses, Audio engineers collecting all kinds of sound processing gear and microphones, Woodworkers collecting every woodworking tool to ever be created and so on.

If you'd ask anybody that has tons of gear if the amount of gear they have made them the slightest bit more productive or creative the answer is almost always "no". Sure, for every profession or hobby there is some essential gear that you absolutely need. Then there is some gear that can make you more productive. But anything you acquire above that just takes up physical and, even worse, mental space. 

## The family room that's called "CNCF"
As I said, I think the cloud-native community is suffering from G.A.S as well. Quite badly I might say. I mean, look at the CNCF [landscape](https://landscape.cncf.io/). If that's not the IT equivalent of a room full of guitars I don't know what is. And just as having too many guitars keeps you from actually playing music or writing songs so does the overload of so called cloud-native software stifle creativity and productivity. 
Think about it: let's say you want to build an app that needs a database, you now need to think about what kind of DB first and then pick one of the many possibilities before you can continue building you app. Or lets say you want to offer a platform to your developers. If you make the decision to build it on top of kubernetes you now have more than 1200 bits and pieces to choose from. 

What if there was only one choice? You wouldn't have to burn any "mental calories" on picking the right one. It wouldn't cost you any time either. Sure, you might miss some features later on but you might just be able to work around that in your own code and still spent less time on the problem compared to the time spent to pick the "right" one.

## It's not a shop
As a counter argument you might say that the CNCF is not a family room but a shop. Of course a guitar shop has a lot of guitars in stock and on display. It's your job to pick the right one. Well, I can follow this argument up to a point. It sure is your job to pick the "guitars". But if the CNCF is a shop it is one without any sales personnel to advise you and most of the products are free. So tell me, if you walk into a shop where most products are free, wouldn't you just want them all?

So if the CNCF is anything it's a market, not a shop. Every vendor is trying to shout a bit louder than the others and picking the right one is totally up to you.

## It gets worse
But G.A.S isn't limited to the CNCF landscape as a whole. It's also true for the individual pieces of software but we usually call it "Feature creep" instead of G.A.S but it's the same thing imho. I believe the cause is that there are so many options to choose from, each option (project) tries to stand out from the others. Initially most projects provide a very limited feature set but are very good at solving a specific problem. However, over time they need to stay relevant and keep their user base happy so they'll start adding features. One operator? No we definitely need 3! CLI only? No! everything needs a GUI! Then comes multi-cluster support, RBAC and so on and so forth. 

In my previous 3 blog posts ([1](https://www.automate-it.today/posts/kiss-not-cncf/),[2](https://www.automate-it.today/posts/aaargcd-operator/),[3](https://www.automate-it.today/posts/aaargcd-deploys-itself/)) I tried to illustrate how this process seems to work. Within very little time we went from a single line of bash to an operator consisting of many hundreds of lines of Go code, hundreds of lines of yaml and a crappy bash script for a CLI. If I were to follow the usual pattern the next step would be to rewrite the CLI in Go, then add a GUI, then add my own built in RBAC, multi-namespace support and then multi-cluster support.... while all I wanted is to sync some yaml from git to kubernetes every few minutes. Not only did I add tons of complexity by writing so much unnecessary code but now this solution also consumes additional resources on my k8s cluster.

## To the point
With all this I'm trying to make a few points:
- Reiterating on the point I made in the [first](https://www.automate-it.today/posts/kiss-not-cncf/) post in this series: Please keep it simple! This is a message to both platform builders and software developers! Keeping complexity down should be a priority for everybody. Less complexity means less code, less stuff to go wrong, less updates, less administration and so on. Everybody will be so much happier.a
- Keeping complexity down sometimes simply means going for the one or two lines of bash code instead of introducing yet another tool
- Some projects are products are actually done at some point. In software land we're so used to frequent updates that we'll consider a project dead if the software hadn't had any major changes for a few months. Take the kubectx and kubens tools for example. Those used to be simple bash scripts and worked perfectly fine. "Installation" was the simplest thing for everybody with a tiny bit of linux knowledge. Now they're kubectl plugins written in Go and I need to install Krew first before I can install plugins. The bash scripts are still around but "New features will be added to the plugins". What new features? I just needed to switch contexts or namespaces. It doesn't need any new features!
- Because of the previous point we should stop maybe stop discounting "old" software that works perfectly but hasn't had major updates in 2 years time. 

I'm aware that I might starting to sound like an old man making these statements. But I honestly believe the whole "chasing the latest hotness" that seems to domninate the kubernetes scene is doing nobody any good. I hear many people say "It will mature over time". But kubernetes has been around since 2015 and I don't see many signs of this whole ecosystem maturing. When I started using Cloud foundry in 2017 it was about 5 years old by then and very mature already. So kubernetes community: it's time to mature.
