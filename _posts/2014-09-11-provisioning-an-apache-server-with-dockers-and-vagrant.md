---
layout: post
title:  "Provisioning an Apache Server With Dockers And Vagrant"
author: saravana
date:   2014-09-11
comments: true
categories: [Dockers, Vagrant]
---
At the end of this blog we will provision a server with vagrant and run apache in a docker container.

<!-- more -->

To understand dockers, visit https://docs.docker.com/

You can download the docker file & vagrant file used in this blog from https://github.com/meygam/docker-apache-ubuntu.git

### Docker File
To run a docker container, we need an docker image. You could either pull a publicly available images from https://registry.hub.docker.com/ or build your own. In our case we will build our own docker image from a ubuntu base image.

Docker file to have apache installed in it looks like below. Will take a look what each line does.

{% highlight ruby linenos %}
	FROM    ubuntu:14.04
	MAINTAINER Saravana Kumar Periyasamy <saravanakumar.periyasamy@gmail.com>

	RUN     apt-get update
	RUN     apt-get install -y apache2
	CMD     service apache2 start && tail -f 	/var/log/apache2/error.log
{% endhighlight %}

Line#1: takes the base image ubuntu, tag 14.04.
Line#2: is the maintainers meta data for this new docker image.
Line#3/4: Runs apt-get update and installs apache2.
Line#5: Starts Apache and tails the apache logs. This will be the default command that will run when a new container is created from this docker image. Tailing the log helps to have a foreground process, and it is required if this container has to be run in background.

###Vagrant File
To build docker image from this file we need a computer with docker installed. To make life easier, we will use Vagrant to build and run this container. If you are new to vagrant, go to this link get started - http://docs.vagrantup.com/v2/getting-started/index.html

Here is the vagrant file to build an image from docker file, run the docker and forward the port 80 from vm to host on port 18080.

{% highlight ruby linenos %}
	VAGRANTFILE_API_VERSION = "2"

	Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  	config.vm.box = "hashicorp/precise64"

  	config.vm.network "forwarded_port", guest: 80, host: 18080

  	config.vm.provision "docker" do |d|
      d.build_image "/vagrant", args: "-t speriyasamy/apache2"
      d.run "apache2", image: "speriyasamy/apache2", args: "-p 80:80"
  	end
	end
{% endhighlight %}

Use `vagarnt up` to bring the vm up. It should install docker, build the docker image and use the same image to run the container.

You can verify this by ssh into the vagrant box, `vagarnt ssh`, and list the docker container using `docker ps` and you should see a apache container running.

![Docker Screenshot](/assets/images/2014/Sep/docker.png)

Now you can access the apache from your host machine using http://localhost:18080

![Apache Screenshot](/assets/images/2014/Sep/apache.png)


###Manual Steps
If you prefer not to use Vagrant, install the docker on your host machine and run the below command manually.

	docker build <<Dockerfile_Folder>> -t "speriyasamy/apache2"

And to run a contianer manually from this image, use the below command

	docker run -d speriyasamy/apache2
    
###Commonly used docker commands

	#list images available on your host machine 
	docker images
    
    #list all the docker containers including inactive ones
    docker ps -a
    
    #pull a docker image from docker hub
    docker pull speriyasamy/docker-openfire-centos
    
    #stop container
    docker stop <<cotainer id>>
    
    #start container
    docker start <<cotainer id>>
    
    #remove container
    docker rm <<cotainer id>>
    
    #remove docker images
    docker rmi <<cotainer id>>
    
