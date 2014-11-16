---
layout: post
title:  "Microstrategy 9 Installation on a new Windows 2008 server"
author: saravana
date:   2013-06-16
comments: true
categories: [Microstrategy]
---
Since my customer has decided to go with the Microstrategy(MSTR) as analytics and reporting tool, I got curious about this tool and wanted to know more on its capabilities. I got an opportunity to go for a free training from MSTR, and I got the evaluation CD & a nice notebook J. I thought I will install it and explore it for 30 days… Lets see how far I go!

<!-- more -->


The installation of actual MSTR seems to be simple, but setting up the windows server for the installtion took more time. It would have been simpler if MSTR gave the VM with MSTR services installed in it. Anyways lets assume we have the Windows Server 2008 ready and let see how to install the MSTR. The prerequites are mentioned in the MSTR website http://www.microstrategy.com/evaluation-edition

###System Requirements
* x86 or x64 compatible CPU
* 4 GB RAM
* Minimum storage space required: Three times the amount of RAM available to Intelligence Server. For example, an Intelligence Server that is provided 4 GB of RAM requires 12 GB of hard drive space.
* Windows 2003 Standard Edition SP2 (on x86 or x64) ,Windows 2003 Enterprise Edition SP2 (on x86 or x64) ,Windows 2003 Standard Edition R2 SP2 (on x86 or x64) ,Windows 2003 Enterprise Edition R2 SP2 (on x86 or x64) ,Windows Vista Business Edition SP2 (on x86 or x64), Windows Vista Enterprise Edition SP2 (on x86 or x64) ,Windows 7 Professional Edition SP1(on x86 or x64), Windows 7 Enterprise Edition SP1 (on x86 or x64) ,Windows 8 all editions (on x86 or x64) ,Windows 2008 Standard Edition SP2 on x64 ,Windows 2008 Enterprise Edition SP2 on x64 ,Windows 2008 Standard Edition R2 SP1 on x64 ,Windows 2008 Enterprise Edition R2 SP1 on x64 ,Windows Server 2012 Standard (on x64)
* Microsoft Internet Information Services (IIS) * version 6.x, 7.0.x, 7.5.x, or 8.x.
* Microsoft .NET Framework 4.0 or 4.5. This must be installed prior to installing MicroStrategy.
* Microsoft Internet Explorer version 7.x, 8.x, 9.x, or 10.x.
* Adobe® Reader® version 9.x, 10.x or 11.x.
* Adobe® Flash® Player version 10.1.x, 10.3.x, or 11.x.
* 16-bit color display or higher
* Windows fonts display set to small fonts (96 dpi)

Insert the DVD and double click on the DVD, it will open the below window, click on "Begin Microstrategy Platform Installation".

![](/assets/images/2014/Mar/061713_0449_microstrate1.png)

Select the language and click "Ok"

![](/assets/images/2014/Mar/061713_0449_microstrate2.png)

Click "Next"

![](/assets/images/2014/Mar/061713_0449_microstrate3.png)

Accept the license agreements and click "Next"

![](/assets/images/2014/Mar/061713_0449_microstrate4.png)

The license kay can be obtained form http://www.microstrategy.com/LicenseKey. Promo code is required to get the license key. I got one with the evaluation DVD pack. License ley will be sent to your registered email address. Yes, you will have to register to get the license key.

![](/assets/images/2014/Mar/061713_0449_microstrate5.png)

Change the installation folders if needed. I prefer it to have the default installation path.

![](/assets/images/2014/Mar/061713_0449_microstrate6.png)

Select the features you want to install. Since I am evaluating I installed all the features except the "Microstrategy office" as it required MS Office to be installed in the server, and I don't have one on my server.

![](/assets/images/2014/Mar/061713_0449_microstrate7.png)

After this I got an error saying "Adobe Acrobat Reader 7 or greater" is needed. I downloaded and installed the acrobat reader. Then I got the below errors
    
    MicroStrategy Common Files requirements:MISSING: Microsoft .Net Framework v4.0
    MicroStrategy Desktop Products requirements:MISSING: Microsoft .Net Framework v4.0
    MicroStrategy Object Manager requirements:
    OK
    MicroStrategy Command Manager requirements:
    OK
    MicroStrategy Enterprise Manager requirements:
    OK
    MicroStrategy Intelligence Server requirements:
    OK
    MicroStrategy Web Universal requirements:
    MISSING: Microsoft Internet Information Server Version 5.0 or greater.
    MISSING: Microsoft .Net Framework v4.0
    MicroStrategy Web Services requirements:
    MISSING: Microsoft Internet Information Server Version 5.0 or greater.
    MISSING: Microsoft .Net Framework v4.0
    MicroStrategy Mobile Server (ASP .NET) requirements:
    MISSING: Microsoft Internet Information Server Version 5.0 or greater.
    MISSING: Microsoft .Net Framework v4.0
    MicroStrategy SDK requirements:
    OK
    MicroStrategy Integrity Manager requirements:
    OK
    MicroStrategy Office requirements:
    MISSING: Microsoft .Net Framework v4.0
    MISSING: Microsoft Office XP or higher.
    MISSING: Windows Installer 4.5 or higher.
    (Windows Installer 4.5 redistributables can be found in
    ..\Utilities\3rdParty\WindowsInstaller45 folder
    on the MicroStrategy product installation media.)
    SequeLink ODBC Socket Server requirements:
    OK
    MicroStrategy Narrowcast Administrator requirements:
    OK
    MicroStrategy Delivery Engine requirements:
    OK
    MicroStrategy Subscription Portal requirements:
    MISSING: Microsoft Internet Information Server Version 5.0 or greater.
    MicroStrategy Tutorial - Reporting requirements:
    OK
    MicroStrategy Analytics Modules requirements:
    OK
    MicroStrategy MDX Cube Provider requirements:
    MISSING: Microsoft Internet Information Server Version 5.0 or greater.
    MISSING: Microsoft .Net Framework v4.0
    
I got below message after installing Microsoft .Net 4.0 and IIS.

    MicroStrategy Mobile Server (ASP .NET) requirements:
    MISSING Web Server feature: Security\Windows Authentication
    MISSING Web Server feature: Application Development\ASP
    
I had to enable the ASP & Windows Authentication services for the IIS as shown below.

![](/assets/images/2014/Mar/061713_0449_microstrate8.png)

Since it's the new server, I didn't get a chance to enable the ASP and ASP.Net extensions in the IIS. Good that MSTR installation recognizes that and showed me the below pop up.

![](/assets/images/2014/Mar/061713_0449_microstrate9.png)

Leave the default and click "Next"

![](/assets/images/2014/Mar/061713_0449_microstrate10.png)

 This is to just an information that you will get the activation key in the mail.
 
![](/assets/images/2014/Mar/061713_0449_microstrate11.png)

Enter the instance name, location, use and click "Next"

![](/assets/images/2014/Mar/061713_0449_microstrate12.png)

Enter your information and click "Next".. In case if you select "I am not an employee…" option then it will prompt you an one more window where it will ask you to entert one of the Employee's information associated with this project.

![](/assets/images/2014/Mar/061713_0449_microstrate13.png)

The installation will start now. It took me hours to finish the installation as my VM's processor and memory are under the required specifications.  Select to get the activation code in email. You need to activate the software within 7 days from the installation.

![](/assets/images/2014/Mar/061713_0449_microstrate14.png)

The installed components and it details are listed once the installation is complete.
    
    Component Install:
    - MicroStrategy Desktop Analyst
    - MicroStrategy Desktop Designer
    - MicroStrategy Architect
    - MicroStrategy Server Administrator
    - MicroStrategy Object Manager
    - MicroStrategy Command Manager
    - MicroStrategy Enterprise Manager
    - MicroStrategy System Manager
    - MicroStrategy Intelligence Server
    - MicroStrategy Report Services
    - MicroStrategy OLAP Services
    - MicroStrategy Distribution Services
    - MicroStrategy Transaction Services
    - MicroStrategy Web Universal - MicroStrategy Web Server (ASP .NET)
    - MicroStrategy Web Universal - MicroStrategy Web Server (JSP)
    - MicroStrategy Web Universal Analyst
    - MicroStrategy Web Universal Professional
    - MicroStrategy Web Universal Reporter
    - MicroStrategy Mobile - BlackBerry® Device Client Application
    - MicroStrategy Mobile - MicroStrategy Mobile Server (ASP .NET)
    - MicroStrategy Mobile - MicroStrategy Mobile Server (JSP)
    - MicroStrategy Portlets
    - MicroStrategy GIS Connectors
    - MicroStrategy SDK
    - MicroStrategy Integrity Manager
    - MicroStrategy Tutorial - Reporting
    - MicroStrategy Analytics Modules
    - MicroStrategy Narrowcast Administrator
    - MicroStrategy Delivery Engine
    - MicroStrategy Subscription Portal
    - MicroStrategy Tutorial - Delivery
    - SequeLink ODBC Socket Server
    - MicroStrategy MDX Cube Provider
    Target Directories:
    - MicroStrategy Desktop Products: C:\Program Files\MicroStrategy\Desktop
    - MicroStrategy Object Manager: C:\Program Files\MicroStrategy\Object Manager
    - MicroStrategy Command Manager: C:\Program Files\MicroStrategy\Command Manager
    - MicroStrategy Enterprise Manager: C:\Program Files\MicroStrategy\Enterprise Manager
    - MicroStrategy System Manager: C:\Program Files\MicroStrategy\System Manager
    - MicroStrategy Intelligence Server: C:\Program Files\MicroStrategy\Intelligence Server
    - MicroStrategy Web Universal - MicroStrategy Web Server (ASP .NET): C:\Program Files\MicroStrategy\Web ASPx
    - MicroStrategy Web Universal - MicroStrategy Web Server (JSP): C:\Program Files\MicroStrategy\Web JSP
    - MicroStrategy Mobile - BlackBerry® Device Client Application: C:\Program Files\MicroStrategy\Mobile Clients
    - MicroStrategy Mobile Server - MicroStrategy Mobile Server (ASP .NET): C:\Program Files\MicroStrategy\Mobile Server ASPx
    - MicroStrategy Mobile Server - MicroStrategy Mobile Server (JSP): C:\Program Files\MicroStrategy\Mobile Server JSP
    - MicroStrategy SDK: C:\Program Files\MicroStrategy\SDK
    - MicroStrategy Portlets: C:\Program Files\MicroStrategy\Portlets
    - MicroStrategy GIS Connectors: C:\Program Files\MicroStrategy\GISConnectors
    - MicroStrategy Integrity Manager: C:\Program Files\MicroStrategy\Integrity Manager
    - MicroStrategy Tutorial - Reporting: C:\Program Files\MicroStrategy\Tutorial Reporting
    - MicroStrategy Analytics Modules: C:\Program Files\MicroStrategy\Analytics Modules
    - MicroStrategy Narrowcast Administrator: C:\Program Files\MicroStrategy\Narrowcast Server\Delivery Engine
    - MicroStrategy Tutorial - Delivery: C:\Program Files\MicroStrategy\Narrowcast Server\Tutorial
    - MicroStrategy Subscription Portal: C:\Program Files\MicroStrategy\Narrowcast Server\Subscription Portal
    - MicroStrategy Health Center: C:\Program Files\Common Files\MicroStrategy\HealthCenter
    - MicroStrategy MDX Cube Provider: C:\Program Files\MicroStrategy\MDX Cube Provider
    - MicroStrategy Common Files: C:\Program Files\Common Files\MicroStrategy
    Program Folder: MicroStrategy
    Log file:
    C:\Program Files\Common Files\MicroStrategy\install.log
    MicroStrategy Web Server (ASP .NET) Virtual Directory: MicroStrategy
    MicroStrategy Mobile Server (ASP .NET) Virtual Directory: MicroStrategyMobile
    MicroStrategy Subscription Portal Virtual Directory: NarrowcastServer
    MicroStrategy MDX Cube Provider Virtual Directory: MicroStrategyMDX
    License Details:
    Contract ID: 76053
    License Key Group: 1
    License Key Issue Date: 7/18/2012
    GA installation
    MicroStrategy Intelligence Server Module
    Evaluation Version
    Evaluation period: 1
    Evaluation duration: 30 day(s)
    License based on Named Users
    Maximum number of Named Users allowed: 500
    MicroStrategy Intelligence Server Universal Option
    License based on Named Users
    Maximum number of Named Users allowed: 500
    MicroStrategy Narrowcast Server Option
    License based on Named Users
    Maximum number of Named Users allowed: 500
    MicroStrategy Report Services Option
    License based on Named Users
    Maximum number of Named Users allowed: 500
    MicroStrategy OLAP Services Option
    License based on Named Users
    Maximum number of Named Users allowed: 500
    MicroStrategy Distribution Services Option
    License based on Named Users
    Maximum number of Named Users allowed: 500
    MicroStrategy Transaction Services Option
    License based on Named Users
    Maximum number of Named Users allowed: 500
    MicroStrategy Intelligence Server Clustering Option
    License based on Named Users
    Maximum number of Named Users allowed: 500
    MicroStrategy Intelligence Server MultiSource Option
    License based on Named Users
    Maximum number of Named Users allowed: 500
    Maximum number of Concurrent Users allowed: 50
    MicroStrategy Web Reporter Module
    Evaluation Version
    Evaluation period: 1
    Evaluation duration: 30 day(s)
    License based on Named Users
    Maximum number of Named Users allowed: 500
    MicroStrategy Web Universal Option
    License based on Named Users
    Maximum number of Named Users allowed: 500
    MicroStrategy Web Analyst Option
    License based on Named Users
    Maximum number of Named Users allowed: 500
    MicroStrategy Web Professional Option
    License based on Named Users
    Maximum number of Named Users allowed: 500
    MicroStrategy Desktop Products
    Evaluation Version
    Evaluation period: 1
    Evaluation duration: 30 day(s)
    MicroStrategy Desktop Analyst Module
    Maximum number of Named Users allowed: 500
    MicroStrategy Desktop Designer Option
    Maximum number of Named Users allowed: 500
    MicroStrategy Architect
    Maximum number of Named Users allowed: 500
    MicroStrategy Server Administrator
    MicroStrategy Command Manager
    Evaluation Version
    Evaluation period: 1
    Evaluation duration: 30 day(s)
    License based on Named Users
    Maximum number of Named Users allowed: 500
    Maximum number of Managed Named Users allowed: 500
    MicroStrategy Enterprise Manager
    Evaluation Version
    Evaluation period: 1
    Evaluation duration: 30 day(s)
    License based on Named Users
    Maximum number of Named Users allowed: 500
    Maximum number of Managed Named Users allowed: 500
    MicroStrategy Object Manager
    Evaluation Version
    Evaluation period: 1
    Evaluation duration: 30 day(s)
    License based on Named Users
    Maximum number of Named Users allowed: 500
    Maximum number of Managed Named Users allowed: 500
    MicroStrategy SDK
    Production Version
    MicroStrategy Analytics Modules
    Evaluation Version
    MicroStrategy Integrity Manager
    Evaluation Version
    Evaluation period: 1
    Evaluation duration: 30 day(s)
    Maximum number of Named Users allowed: 1
    Maximum number of Managed Named Users allowed: 500
    MicroStrategy Mobile
    Evaluation Version
    Evaluation period: 1
    Evaluation duration: 30 day(s)
    License based on Named Users
    Maximum number of Named Users allowed: 500
    MicroStrategy System Manager
    Evaluation Version
    Evaluation period: 1
    Evaluation duration: 30 day(s)
    License based on Named Users
    Maximum number of Named Users allowed: 500
    Maximum number of Managed Named Users allowed: 500
    
Restart the server to complete the installation

![](/assets/images/2014/Mar/061713_0449_microstrate15.png)

Activate the server using Start à Microstrategy à License Manager

![](/assets/images/2014/Mar/061713_0449_microstrate16.png)

Enter the activation key that you got in the mail.

![](/assets/images/2014/Mar/061713_0449_microstrate17.png)

This completes the Microstrategy Server installation.

![](/assets/images/2014/Mar/061713_0449_microstrate18.png)

The installation should be easy if you have a good server to install it in. I had to struggle to get the VM ready and install MSTR on top of that. That too my VM was about micro in size. Took 3 hours to complete my installation. Better to use a larger VM for quicker installation.
