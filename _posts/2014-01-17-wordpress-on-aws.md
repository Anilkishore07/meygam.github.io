---
layout: post
title:  "WordPress on AWS"
author: saravana
date:   2014-01-17
categories: aws wordpress
---
In this blog I am going to cover the step by step instructions to host your own WordPress blog in AWS. Hosting your own blog has some advantages, you can install any plugins/themes you want and publish any content you like. Having said that it’s not for everyone. If you are a basic user better you create account in wordpress.com. I wanted to run my own blog engine, primarily because I am a techie.

### Architecture for WordPress on AWS

At a minimum WordPress needs a web server, to serve your blog and a database instance, to store your blog contents. You can install both web server and the database instance in just one server. But for high availability and better backup mechanism I choose to keep the web server and database instance separate. Below is the architecture that we are going to follow. I will talk about each components in following sections.

<iframe src="http://www.slideshare.net/slideshow/embed_code/30109797" width="427" height="356" frameborder="0" marginwidth="0" marginheight="0" scrolling="no" style="border:1px solid #CCC; border-width:1px 1px 0; margin-bottom:5px; max-width: 100%;" allowfullscreen> </iframe>


<table>
<tr>
	<th>Service</th>
    <th>Unit Rate</th>
    <th>Quantity (Per Year)</th>
    <th width="80px">Cost per Year</th>
</tr>
<tr>
  <td>EC2 – Linux (t1.micro)</td>
  <td>$0.02 per hour</td>
  <td>365 days * 24 hours = 8760 hours</td>
  <td>$175.20</td>
</tr>
<tr>
  <td>EC2 – Data Transfer OUT From Amazon EC2 To Amazon Elastic Load Balancing</td>
  <td>$0.01 per GB</td>
  <td>1 GB per month * 12 month = 12 GB</td>
  <td>$0.12</td>
</tr>
<tr>
  <td>EC2 – Data Transfer IN To Amazon EC2 From Amazon RDS  Using a private IP address	</td>
  <td>$0.00 per GB</td> 
  <td>1 GB per month * 12 month = 12 GB</td>
  <td>$0.00</td>
</tr>
<tr>
  <td>EBS - Standard volumes</td>
  <td>$0.10 per GB-month</td>
  <td>8 GB * 12 month
= 96 GB-month</td>
  <td>$9.60</td>
</tr>
<tr>
  <td>EBS - Standard volumes</td>
  <td>$0.10 per 1 million I/O requests</td>
  <td>1 million request per month * 12 month</td>
  <td>$1.20</td>
</tr>
<tr>
  <td>RDS – MySQL -
Standard Deployment (db.t1.micro)</td>
  <td>$0.025 per hour</td>
  <td>365 days * 24 hours
= 8760 hours</td>
  <td>$219.00</td>
</tr>
<tr>
  <td>RDS – MySQL - Standard Deployment Database Storage</td>
  <td>$0.125 per GB-month</td>
  <td>20 GB * 12 month
= 240 GB-month</td>
  <td>$30.00</td>
</tr>
<tr>
  <td>ELB</td>
  <td>$0.025 per hour</td>
  <td>365 days * 24 hours
= 8760 hours</td>
  <td>$219.00</td>
</tr>
<tr>
  <td>ELB – Data Transfer</td>
  <td>$0.008 per GB of data processed by ELB</td>
  <td>1 GB per month * 12 month = 12 GB</td>
  <td>$0.10
</td>
</tr>
<tr>
<td>Route 53 - Hosted Zones</td>
<td>$0.50 per hosted zone / month for the first 25 hosted zones
$0.10 per hosted zone / month for additional hosted zones</td>
<td>1 hosted zone * 12 month = 12 hosted zone month</td>
<td>$6.00</td>
</tr>
<tr>
<td>Route 53 -
Standard Queries</td>
<td>$0.500 per million queries – first 1 Billion queries / month
$0.250 per million queries – over 1 Billion queries / month</td>
<td>under 1 million queries per month
* 12 month = 12 million quries</td>
<td>$6.00</td>
</tr>
<tr>
<td></td>
<td></td>
<td><strong>Total Cost Per Year (approx.)</strong></td>
<td align="right"><strong>$666.22</strong></td>
</tr>
<tr>
<td><strong> </strong></td>
<td><strong> </strong></td>
<td><strong>Total Cost Per Month <strong>(approx.)</strong></strong></td>
<td><strong>$55.52</strong></td>
</tr>
</table>

My blog do not get lot of visits for now, so I went with less powerful machines and DB, to save cost. I can scale my blog when required, that’s the advantage of AWS. The current architecture allows scaling. I will cover more about scaling in next part.

### Setup infrastructure in AWS
* Create a VPC with the CIDR 10.0.0.0/16.
![](http://farm4.staticflickr.com/3831/11989981795_b8780b74a8_b_d.jpg)
* Create a private subnets under the new VPC. We need two subnets in different availability zones to setup RDS in a VPC.
![](http://farm6.staticflickr.com/5547/12023870814_0df096e40a_b_d.jpg)
* Create an internet gateway and attach it to the VPC.
![](http://farm4.staticflickr.com/3732/12023835684_beebf39e90_b_d.jpg)
* Add internet gateway to the route table and attach it to the subnet.
![](http://farm8.staticflickr.com/7452/12023771363_854f9efc68_b_d.jpg)
* Create a DB Subnet group with minimum two subnets each in different availability zones.
![](http://farm6.staticflickr.com/5472/12023771423_9b337e66b5_b_d.jpg)
* Create an MySQL RDS instance inside the VPC, so that only the instances in the same network can connect to it.
![](http://farm6.staticflickr.com/5535/12023836124_690f1f14d6_b_d.jpg)
* Create an Ubuntu EC2 instance inside the VPC without any public IP, and attach it to the elastic IP, so that I can ssh into the machine.
![](http://farm4.staticflickr.com/3720/12024318096_9fdcc9f651_b_d.jpg)

### Install php5
Now that EC2 is up and running, ssh into the machine using the private key using the command
	
    ssh -i <<private-key.pem>> ubuntu@<<elastic ip>>

once you are in the machine, run

	sudo apt-get update
    
to update the package list. Then install the php5, php5 fpm and php5 mysql packages using the below command

	sudo apt-get install php5
	sudo apt-get insatll php-fpm
	sudo apt-get install php5-mysql
    
### Install WordPress
Download and extract the WordPress using the below commands.

    mkdir -p /var/www/blog.meygam.com/htdocs/ /var/www/blog.meygam.com/logs/
    cd /var/www/blog.meygam.com/htdocs/
    wget http://wordpress.org/latest.tar.gz
    tar –strip-components=1 -xvf latest.tar.gz
    rm latest.tar.gz
    chown -R www-data:www-data /var/www/blog.meygam.com/
    
### Install & configure nginx
By default Ubuntu comes with Apache2. I like to use nginx, as its more modern than Apache2. So first we need to uninstall the Apache2 & all of its dependencies using the below command.

	sudo apt-get remove apache2*
    
Install nginx using the below command.

	sudo apt-get install nginx
    
Create the website configuration

	vi /etc/nginx/sites-available/blog.meygam.com
    
and add the below configuration in the file. Replace blog.meygam.com to your site name.
    
    server {
    
    server_name blog.meygam.com;
    
    access_log /var/log/nginx/blog.meygam.com.access.log;
    error_log /var/log/nginx/blog.meygam.com.error.log;
    
    root /var/www/blog.meygam.com/htdocs;
    index index.php;
    
    location / {
    
    try_files $uri $uri/ /index.php?$args;
    
    }
    
    location ~ \.php$ {
    
    try_files $uri =404;
    include fastcgi_params;
    fastcgi_pass unix:/var/run/php5-fpm.sock;
    
    }
    
    }
    
Restart the nginx to load the configuration

	sudo service nginx restart
    
### Configure Load Balancer
Configure Elastic load balancer (ELB) attach the EC2 instance that we created to it. Using ELB will let you scale the application later point.

![](http://farm4.staticflickr.com/3685/12024782696_02458889fc_b_d.jpg)

![](http://farm8.staticflickr.com/7349/12024303824_703f47f09a_b_d.jpg)

![](http://farm8.staticflickr.com/7438/12024782816_f152481a0c_b_d.jpg)

![](http://farm4.staticflickr.com/3686/12024236493_d63f2c8e01_b_d.jpg)

### Configure Route 53
Route 53 helps to map your domain name to the elastic load balancer. Create a hosted zone for your domain, and add the Route 53 name servers to your domain register.

![](http://farm4.staticflickr.com/3806/12024318566_f502004ed4_b_d.jpg)

Add a CNAME record to point to your ELB.
![](http://farm3.staticflickr.com/2823/12023772813_a2a59d99fa_b_d.jpg)

### Configure WordPress

Now access your website in the browser, and you should see the WordPress configuration page. Follow the instructions and point it to the database that we created.

![](http://farm6.staticflickr.com/5511/12024553674_1d1230de93_b_d.jpg)

![](http://farm8.staticflickr.com/7415/12024196015_b966e2ee75_b_d.jpg)

![](http://farm4.staticflickr.com/3769/12024553914_8e8b023647_b_d.jpg)

![](http://farm6.staticflickr.com/5540/12025035976_f9738840b3_b_d.jpg)

![](http://farm6.staticflickr.com/5534/12024554344_e2b70a17af_b_d.jpg)

And that’s it, your blog is fully up & running on AWS now! Happy Blogging!