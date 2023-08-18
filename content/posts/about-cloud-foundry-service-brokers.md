---
title: 'About Cloud Foundry Service Brokers'
date: Tue, 18 Jul 2017 10:30:02 +0000
draft: false
tags: ['Cloud Foundry', 'Cloud Foundry', 'ITQ', 'Open Service Broker API', 'Service Brokers']
---

Cloud Foundry offers consumers of the platform all kinds of backing services. Think of services like Mysql, Redis and RabbitMQ. Those services are offered to consumers through the Cloud Foundry marketplace. ![](https://www.cloudfoundry.org/wp-content/uploads/2017/01/CFF_Logo_vertical_RGB.png) To be able to create instances of the services in the marketplace and then bind them to an application, Cloud Foundry uses Service Brokers. A Service Broker implements the [Cloud Foundry Open Service Broker API](https://docs.cloudfoundry.org/services/api.html) and takes care of provisioning of services. It also provides credentials to a service so an application can connect to the created service instance. The CF service broker API is a REST API specification. You can implement this API any way you like. Most service brokers seem to be written in either Golang or Ruby but it doesn't realy matter in which language you implement the API. It doesn't matter where and how you run it either. As long as the broker is reachable for the Cloud Controller Cloud Foundry will be able to consume it In conversations with customers I noticed some misconceptions around service brokers so in this post I want to shed some light on what a service broker is and what it's not. Let me start out by listing what a Service Broker is NOT:

*   A Service Broker is not a reverse proxy of some kind
*   A service Broker is not a connector
*   A Service Broker is not a service in and of itself

So what does a service broker do?  Let's walk through how a Cloud Foundry platform user would consume a Mysql database and map that to service brokers operations:

*   User lists content of the marketplace: cf marketplace
    *   Cloud foundry will list all the services that are offered by registered service brokers
*   User creates a Mysql service instance: cf create-service mysql 100mb mydatabase
    *   This command tells Cloud Foundry the user wants to consume to 100mb plan of the mysql service. The service will be referenced as "mydatabase" within Cloud Foundry. This won't be the actual database name.
    *   Cloud Foundry will call the "provision" API resource on the service broker that offers the mysql service
    *   The service broker will now create a new database instance for the user and respond with an http 201 status back to Cloud Foundry
    *   Cloud Foundry will save a reference to the service instance
*   Now the user wants to consume the database. He can do so by binding the created service the his application:cf bind-service myapplication mydatabase
    *   Cloud Foundry will now do a call to the bind resource of the Mysql service broker API.
    *   The broker will create a user for the mysql database and send a response to Cloud Foundry containing the connection details (URI, Username, Password) for the database server (not for the broker but the DB server itself).
    *   Cloud Foundry takes the response and populates the VCAP\_SERVICES environment variable for the application. This environment variable contains a JSON string with all the information of all the services bound to the app.
    *   The app itself is responsible for parsing the json, getting the connection details and connecting to the database. From now the broker is no longer in the loop.

In summary: The broker presents services to Cloud Foundry, CF can request service plans from the broker and request connection details for created services. After that point the broker is out of the loop. It brokered the connection, now the application is directly connected to the service. When a user no longer needs the service he can issue the cf unbind  command. This will remove the information from VCAP\_SERVICES and tells the broker to initiate the unbind task. What exactly happens then depends on the broker but in the case of MYsql it will delete the user it created during the bind operation. After the unbind you can also issue a cf delete-service  command. This tells the broker to get rid of the service. In the case of Mysql it will delete the whole database. In another post I will go into more detail on how to build you own broker.