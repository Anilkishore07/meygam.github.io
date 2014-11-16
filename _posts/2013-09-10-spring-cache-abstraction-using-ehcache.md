---
layout: post
title:  "Spring Cache abstraction using Ehcache"
author: saravana
date:   2013-09-10
categories: [Spring, Java, EhCache]
---
In this article, we will see how to use EhCahce with Spring Cache abstraction. Refer http://ehcache.org/documentation/recipes/spring-annotations for using EhCache’s own annotations. The advantage of using it with Spring Cache abstraction is that it's easy to change the underlying storage. Say in future if we want to change it to Memcached, then it is just a configuration change and not a code change. 

<!-- more -->

Let’s look at the Spring configuration steps and common issues that one encounters while using Spring Cache.
	
* Add the Ehcache dependency to the project

{% gist 6613368 %}

Note: Without adding the dependency you might see the error

    org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerMapping#0': Invocation of init method failed; nested exception is java.lang.NoClassDefFoundError: Lnet/sf/ehcache/CacheManager;
   
* Include the cache namespace and schema location in the Spring Configuration file.

{% gist 6613426 %}

* Configure the application to use annotation driven

{% gist 6613497 %}

* Define the cacheManager bean

{% gist 6613514 %}

* Configure the ehcache.xml
Defined the caches in the ehcache configuration XML. Below is the sample ehcache xml file to define the caches.

{% gist 6613543 %}

Note: Without defaultCache defined, below error will be thrown

    Cannot resolve reference to bean 'cacheManager' while setting bean property 'cacheManager'; nested exception is org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'cacheManager' defined in ServletContext resource [/WEB-INF/servlet-config.xml]: Cannot resolve reference to bean 'ehcache' while setting bean property 'cacheManager'; nested exception is org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'ehcache' defined in ServletContext resource [/WEB-INF/servlet-config.xml]: Invocation of init method failed; nested exception is net.sf.ehcache.CacheException: Illegal configuration. No default cache is configured.
    
* Add the @Cacheable annotation to the code

{% gist 6613568 %}


#### Common issues one might face in using the Spring Cache abstraction

* Cache key collision:
The default key generator is a simple one and it is easy to get key collisions using this. Refer the DefaultKeyGenerator.java code to see how it works. Also when you have an array as a parameter, the default key generator produces different key for each execution.

It’s better to use your own key generator. Below is the sample one I used

{% gist 6613617 %}

And you can set this as your default key generator in the Spring configuration file like below.

{% gist 6613634 %}

* Application context is loaded more than once:

Sometimes due to misconfiguration the application context gets loaded twice and due to that you might get the error. Make sure you name the cacheManager in the ehcache.xml and you are not loading the application context twice.

    Another unnamed CacheManager already exists in the same VM. 
    Please provide unique names for each CacheManager in the config or do one of following:
    1. Use one of the CacheManager.create() static factory methods to reuse same CacheManager with same name or create one if necessary
    2. Shutdown the earlier cacheManager before creating new one with same name.
    The source of the existing CacheManager is: InputStreamConfigurationSource [stream=sun.net.www.protocol.jar.JarURLConnection$JarURLInputStream@47f3ed6]
