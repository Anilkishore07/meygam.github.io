---
layout: post
title:  "Overview of Microstrategy features"
author: saravana
date:   2014-06-20
categories: [Microstrategy]
---
In my previous blog [Microstrategy 9 Installation on a new Windows 2008 server](/microstrategy-9-installation-on-a-new-windows-2008-server/) we looked at installing the MSTR. Here let's look at the features we installed. Below is the list of all the features from MSTR.

<!-- more -->

![](/assets/images/2014/Mar/062313_1702_overviewofm1-1.png)

The below picture will give us the overview of how all these features are related.

![](/assets/images/2014/Mar/062313_1702_overviewofm2-1.png)

Let's look at each one of them in detail

####Microstrategy Intelligence Server

Microstrategy Intelligence Server is the architectural foundation for the Microstrategy platform. This communicates to the various data sources, and runs the reports and distributes to MSTR Desktop and Web users. Intelligence server ensures the scalability and fault tolerance required for analysis of terabyte databases and millions of users.

Below are the sub-features installed as part of MSTR Intelligence server. We will cover these features in detail when we explore the Intelligence server in detail.

![](/assets/images/2014/Mar/062313_1702_overviewofm3-1.png)

####Microstrategy Web universal
MSTR Web enables the users to create and/or run the reports in web.

![](/assets/images/2014/Mar/062313_1702_overviewofm4-1.png)

Below are the sub-features installed as part of MSTR Web Universal.

![](/assets/images/2014/Mar/062313_1702_overviewofm5-1.png)

####Microstrategy Office
MSTR Office is a plugin for Microsoft Office and lets the user to run reports from Office tools. I could not show the screen shot for this product, I didn't install this feature as I don't have the MS Office in my VM.

Below are the sub-features installed as part of MSTR Office.

![](/assets/images/2014/Mar/062313_1702_overviewofm6.png)

####Microstrategy Mobile

MSTR Mobile enables the users to view the reports from mobile devices.

![](/assets/images/2014/Mar/062313_1702_overviewofm7.png)

Below are the sub-features installed as part of MSTR Mobile.

![](/assets/images/2014/Mar/062313_1702_overviewofm8.png)

####Microstrategy Desktop Products
MSTR Desktop allows the user to create and/or run the reports in the Desktop.

![](/assets/images/2014/Mar/062313_1702_overviewofm9.png)

MSTR Architect helps to map the data model to the business model.

![](/assets/images/2014/Mar/062313_1702_overviewofm10.png)

Below are the sub-features installed as part of MSTR Desktop.

![](/assets/images/2014/Mar/062313_1702_overviewofm11.png)

####Microstrategy Object Manager

MSTR Object Manager is used to migrate MSTR objects from one environment to another.

![](/assets/images/2014/Mar/062313_1702_overviewofm12.png)

####Microstrategy Command Manager

Command Manager is used to create scripts to automate common administrative tasks.

![](/assets/images/2014/Mar/062313_1702_overviewofm13.png)

####Microstrategy Enterprise Manager
MSTR Enterprise Manager provides the usage details of the MSTR Intelligence Server. The core of Enterprise Manager is nothing but a MSTR project with set of predefined reports, and the metrics and attributes to create your own reports. You can run reports that help you to:

* Allocate system resources based on data warehouse usage trends
* Research efficient aggregation, partitioning, and indexing strategies
* Profile users based on their system resource usage
* Determine the optimal time to run scheduled jobs, load data, or perform routine system and database maintenance
* Determine the most popular reports so you can schedule and cache them, thus increasing their response time and reducing the load on the system
* Identify unused objects from your metadata repository so they can be deleted later
* Identify peak usage times and patterns and, if necessary, change your Intelligence Server configuration to respond appropriately
* Determine whether you need to add more threads to the database connection threads if queue times are long
* Find which tables are the most used, and create indexes to improve performance

####Microstrategy Integrity Manager
MSTR Integrity Manager is used to automate the report testing.

![](/assets/images/2014/Mar/062313_1702_overviewofm14.jpg)

![](/assets/images/2014/Mar/mstr_integrity_manager.png)

####Microstrategy System Manager
Microstrategy System Manager helps to automate the configuration of MSTR products in larger environments.

![](/assets/images/2014/Mar/062313_1702_overviewofm15.png)

####Microstrategy SDK
MSTR SDK enables the developers to extend and integrate MSTR platform through a set of APIs that exposes all platform functionality. It helps to embed intelligence in any custom application.

####Microstrategy Narrowcast Sever
MSTR Narrowcast server delivers the report in mail/phone based on the schedules.

![](/assets/images/2014/Mar/062313_1702_overviewofm16.png)

Below are the sub-features installed as part of MSTR Narrowcast Server.

![](/assets/images/2014/Mar/062313_1702_overviewofm17.png)

####Other Components
This installs the drivers to access Cubes from Analysis Servers and Sequelink ODBC Socket server to talk to multiple data sources.

![](/assets/images/2014/Mar/062313_1702_overviewofm18-1.png)