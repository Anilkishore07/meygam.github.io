---
layout: post
title:  "Installing Oracle Java 7 and Tomcat 7 on Ubuntu 13"
author: saravana
date:   2014-01-17
categories: [Ubuntu, Java, Tomcat]
---
Setting up a new Ubuntu server with Java 7 & Tomcat 7 seems to be trivial, but often I had to do it at my workplace and everytime I had to google it to do this. So I thought I will list down the steps in my blog.

### Install Oracle Java 7
Run the below commands to install Oracle Java 7 - steps followed from <a href="http://www.webupd8.org/2012/01/install-oracle-java-jdk-7-in-ubuntu-via.html" target="_blank">INSTALL ORACLE JAVA 7 IN UBUNTU VIA PPA REPOSITORY</a> 

	sudo add-apt-repository ppa:webupd8team/java
	sudo apt-get update
	sudo apt-get install oracle-java7-installer
    
Check if the right version of java is installed
 
	java -version
 
	java version "1.7.0_45"
	Java(TM) SE Runtime Environment (build 1.7.0_45-b06)
	Java HotSpot(TM) 64-Bit Server VM (build 20.45-b01, mixed mode)
    
Run the below command to set the Java environment variables

	sudo apt-get install oracle-java7-set-default

### Install Unzip
Run the below command to install unzip, we need this to unzip the tomcat binaries.

	sudo apt-get install unzip
  
### Install Tomcat 7
Download the latest tomcat from tomcat website & unzip it. And give execute access to all .sh files. Start the tomcat manually. 

    cd /opt
    sudo wget http://www.webhostingjams.com/mirror/apache/tomcat/tomcat-7/v7.0.52/bin/apache-tomcat-7.0.52.zip
    sudo unzip apache-tomcat-7.0.52.zip
    sudo chmod +x /opt/apache-tomcat-7.0.52/bin/*.sh
    sudo /opt/apache-tomcat-7.0.52/bin/startup.sh
    
Check if the tomcat is running. The below command should return the tomcat default page. 

	curl http://localhost:8080
    
 Note: I don't use tomcat7 from ubuntu repository as it some times screws up my Oracle Java installation and installs Open JDK.