---
layout: post
title:  "Connecting to Open VPN server from iOS device"
author: saravana
date:   2014-03-13
categories: [OpenVPN, iOS]
---
To connect iOS device to Open VPN server we need

* Open VPN app
* Open VPN setting file (client.ovpn)
* Client Certificate
* CA Certificate (if its self signed)

<!-- more -->


###Install Open VPN app

Install Open VPN client from app store (https://itunes.apple.com/app/openvpn-connect/id590379981)

![](/assets/images/2014/Mar/20140313_174551000_iOS.jpg)


###Import Open VPN settings
Get the Open VPN settings file, clinet.ovpn, from your VPN admin. Send that file to your iOS device through email, and open it in Open VPN app on the iOS device.

![](/assets/images/2014/Mar/20140313_175334000_iOS.jpg)

Click on the green plus sign to import the profile to iOS device.

{<3>}![](/assets/images/2014/Mar/20140313_180407000_iOS.jpg)

Once imported it should like below

![](/assets/images/2014/Mar/20140313_180416000_iOS.jpg)

###Install Client Certificate
Get the client certificate in .p12 format from your Open VPN admin. Send that also to your iOS device through email, and install it.

![](/assets/images/2014/Mar/20140313_180433000_iOS-1.jpg)

	Note: You need your Client certificate with password protected. iOS doesn't allow to import certificate with blank password.
    
![](/assets/images/2014/Mar/20140313_180452000_iOS.jpg)

You should see a successfully installed message

![](/assets/images/2014/Mar/20140313_180504000_iOS.jpg)

If it say "Not Trusted" as shown in my certificate above, then you will have to install the CA certificate. Else, when you try to connect Open VPN you will get the below error

	2014-03-13 10:59:29 EVENT: CORE_ERROR PolarSSL: ca certificate is undefined [ERR]

###Install CA Certificate
Get the CA certificate from VPN admin. it should be in the form of .pem file. Send that to your iOS device through email, and install it.

![](/assets/images/2014/Mar/20140313_180531000_iOS.jpg)

Install the root CA certificate. 

![](/assets/images/2014/Mar/20140313_180534000_iOS.jpg)

Once installed you should see that Certificate Authority is "Trusted"

![](/assets/images/2014/Mar/20140313_180547000_iOS.jpg)

###Connect to Open VPN server

Open "Open VPN" app in your iOS device, select the `None Selected` list and select your certificate.

![](/assets/images/2014/Mar/20140313_180601000_iOS.jpg)

Now turn on the VPN button and you should get connected to Open VPN server successfully.

![](/assets/images/2014/Mar/20140313_180641000_iOS.jpg)
