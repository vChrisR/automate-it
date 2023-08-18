---
title: 'Orchestrator and your configuration data part 2 - SQL database'
date: 
draft: true
tags: ['Uncategorized']
---

**SQL database**
================

A SQL database can be used in different implementation sizes, depending on the use-case and the availability this can be either a Microsoft SQL database or an open-source variant like PostgreSQL. When you make the choice for a SQL database, make sure to create a maintainable and dynamic database model. This means that there should be small design that specifies which tables are created, which keys are defined, what mappings are being created and which views should be made available. Having a vision of what the future will bring your software defined datacenter will help you to create a model that will not only fit now, but also will in the future. Microsoft SQL comes with the management studio, this tool has a very nice feature called Database Diagrams. Database diagrams allow you to create a database model including keys, relations and best of all: It will create everything for you! In terms of the network data that we have briefly touched, what would a relational database model look like? The picture below is one of the possible implementations. [![Configuration-data-SQL](http://automate-it.today/wp-content/uploads/2016/07/Configuration-data-SQL-1024x823.jpg)](http://automate-it.today/wp-content/uploads/2016/07/Configuration-data-SQL-1024x823.jpg) Why choose for a relational SQL database? There are a couple advantages of using a SQL database:

*   Speed (in the back- and front-end)
*   everything available in transact-SQL can be used to obtain, manipulate or filter your data
*   As it is fast, it can be used in the vRA ASD backing pre-defined dynamic lists

Orchestrator implementation
---------------------------

There are two options that allows you to query a SQL database in vRO. Using the SQL plugin (which utilizes JDBC) allows you to easily create Create, Read, Update and Delete (CRUD) workflows for tables. Using custom workflows based on JDBC implementation, you have to write your own queries and the backing code. Both ways have their own advantages and disadvantages: **SQL Plugin** _Pro's_

*   Easy to use when unfamiliar with writing SQL queries
*   Quick setup of database connections
*   Out of the box functionality to create CRUD workflows for tables
*   Visual insight in the database tables and columns

_Con's_

*   No out of the box ability to query database views, only available using the executeCustomQuery method
*   When new tables are added to the database the plugin does not automatically update it's inventory
*   When multiple database connections are configured and one is broken, all other connections will not work

**JDBC** _Pro's_

*   Ability to execute queries on views
*   Only two workflows necessary for all INSERT and SELECT statements
*   And therefor the ability to write a generic (database vendor independent) framework for querying.

_Con's_

*   SQL query experience necessary
*   No visual insight into the database tables and columns
*   No out-of-the-box functionality to generate CRUD workflows

Concluding this post I would like to make a small remark on the above two choices. As SQL is my favorite way of storing configuration data due to it's easy to handle relational data and the strong query language that is available; I would in most cases in a larger environment advice to use a SQL database. Of course this depends on whether it in any form fits the environment you are working in. For the two connection types (JDBC and SQL plugin): There is no bad choice in this, just make sure to keep in mind that your choice should not block you in any way to quickly develop and change your automation logic. If querying databases is not in your comfort zone, go for the plugin; otherwise wizkid around and use the JDBC implementation!