---
title: 'webEye - The webhook receiver'
date: Thu, 19 Mar 2015 11:30:17 +0000
draft: false
tags: ['AMQP', 'docker', 'RabbitMQ', 'receiver', 'vRealize Orchestrator (vCO)', 'vRO', 'webEye', 'webhook', 'weEye']
---

When building out the demo environment which I was going to use for our NLVMUG UserCon presentation I came accross a problem: I wanted to notify my private cloud whenever a new Docker image was build on Docker Hub. This proofed impossible with the existing VMware software so I created my own solution. And here it is: webEye - The webhook receiver. It will simply forward all received webhooks to an AMQP bus after checking that it's a valid webhook message. You can pick up the message with your favourite orchestration tool and act on them.

[![docker](http://www.automate-it.today/wp-content/uploads/2015/03/docker.png)](http://www.automate-it.today/wp-content/uploads/2015/03/docker.png)
---------------------------------------------------------------------------------------------------------------------------------------------------

webEye
------

Every hook needs an eye to hook into. That's why my little app is called webEye :) [![nodejs](http://www.automate-it.today/wp-content/uploads/2015/03/nodejs-300x81.png)](https://github.com/vChrisR/webEye) [webEye](https://github.com/vChrisR/webEye) is written in JavaScript and runs on [node.js](http://nodejs.org/). It is designed to run in a docker container. However, it already evolved fom something that was originally intended to just receive [docker hub](https://hub.docker.com/) webhooks. Currently it also has support for my "Magic Button" and even for vRealize Operations. Other web hook senders might follow.

Getting started
---------------

As I said, [webEye](https://registry.hub.docker.com/u/vchrisr/webeye/) is developed to run in a docker container so this "getting started" will only cover how to start the app in a docker environment.

*   All received hook are forwarded to an AMQP bus. So let's start an AMQP Server: docker run --name rabbit -p 5672:5672 -p 15672:15672 dockerfile/rabbitmq
*   Now start webEye: docker run -p 80:80 -p 443:443 -e "DHKEY=12345" -e "MBKEY=12345" --name webEye --link rabbitmq:rabbit -t vchrisr/webeye
*   The DHKEY in the line above sets the API key that you need to send with the request. This adds a bit of security. Make sure to put in a random string instead of "12345".  tip: [random.org](https://www.random.org/strings/)
*   Now make sure port 80 on your webEye server is mapped to a public ip address
*   Open the now running webEye page in a browser to to get it running. This first visit actually triggers phusion passenger in the container to start the node.js app. This in turn creates a persistent exchange on the rabbitMQ server.
*   Create a webhook on your docker hub repository to http://{your public ip}:{public port}/dockerhub?apikey=12345
*   Connect your consumer to the rabbitMQ server
*   Create a new Q to receive your messages
*   Create a binding which routes messages with routing key webeye.docker.hub to your Q
*   Create a suscription for the Q you created
*   If you're using vRO you can now create a policy which triggers a workflow when a message appears in the subscription.
*   Create a workflow that does whatever you want when a docker hub hook is received.

Testing webEye
--------------

If you were able to make webEye available on the public internet and you've configured a webhook on your docker repo you can

*   now simply click "test" on the webhook configuration page.
*   To test offline I usually use the Firefox RESTClient plugin.Select "POST" as the method.
*   Enter this url: http://<ip of webEye machine>:<port>/dockerhub?apikey=<apikey>
*   Add this header: Content-Type: application/json
*   For the body you need some actual content. webEye will check the presence of some specific fields in the json to make sure it's a Docker Hub webhook. I usually use the json from the Docker Hub Documentation:
*   ```
    {
        "callback\_url": "https://registry.hub.docker.com/u/svendowideit/busybox/hook/2141bc0cdec4hebec411i4c1g40242eg110020/",
        "push\_data": {
            "images": \[
                "27d47432a69bca5f2700e4dff7de0388ed65f9d3fb1ec645e2bc24c223dc1cc3",
                "51a9c7c1f8bb2fa19bcd09789a34e63f35abb80044bc10196e304f6634cc582c"
            \],
            "pushed\_at": 1417566822,
            "pusher": "svendowideit"
        },
        "repository": {
            "comment\_count": 0,
            "date\_created": 1417566665,
            "description": "",
            "full\_description": "webhook triggered from a 'docker push'",
            "is\_official": false,
            "is\_private": false,
            "is\_trusted": false,
            "name": "busybox",
            "namespace": "svendowideit",
            "owner": "svendowideit",
            "repo\_name": "svendowideit/busybox",
            "repo\_url": "https://registry.hub.docker.com/u/svendowideit/busybox/",
            "star\_count": 0,
            "status": "Active"
        }
    }
    ```

[![resttest-webeye](http://www.automate-it.today/wp-content/uploads/2015/02/resttest-webeye-300x106.png)](http://www.automate-it.today/wp-content/uploads/2015/02/resttest-webeye.png)