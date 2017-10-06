Installation Overview
The general sequence for setting up a Tomcat server is as follows:

Define your server requirements and install your server.
Install Tomcat on your server.
Configure your server and network devices so that laptops or Android devices connecting to the internet from an external access point (e.g., Starbucks) can access your server. You may wish to restrict access to your server to devices directly connected to your local network. In this case, ODK Collect would not be able to access your server (to download forms or upload finalized forms) until the ODK Collect device returns to your site and establishes a direct connection to your local network.
Obtain and Install an SSL certificate if you need secure (https:) access.
Select and Install your database server (MySQL or PostgreSQL or Microsoft SQL Server or Azure SQL Server).
Download and install ODK Aggregate.
Server Requirements
The key questions are:

How available do you need your server to be (e.g., 24/7, 8/5)?
Must it stay up when the power goes out? For how long?
How rapidly do you need it operational after a failure (e.g., disk failure)?
How much data loss can you tolerate?
How big is your dataset?
How secure and protected do you need your data to be?
Availability: Most users provision ODK Collect with blank forms and rarely update those forms over the course of a study; surveyors upload finalized forms to ODK Aggregate infrequently and opportunistically. If that is your situation, you likely do not need a high-availability server. If you do need a high-availability server, you need to talk to your Internet Service Provider (ISP) as to their availability guarantees and consider obtaining a UPS (uninterruptable power supply) and generator to operate the server and the networking infrastructure (cable modem and/or routers) during power failures.  High-reliability servers are generally also highly available and have features that speed up their repair time. If you cannot tolerate any downtime, you can deploy two or more servers to reduce the likelihood of being without a running ODK Aggregate instance.

Data Loss: Your tolerance to data loss will impact your backup schedule and your server hardware. If you cannot tolerate any data loss, or less than 24 hours of data loss, you should invest in a RAID storage array with battery-backed controller cards (this is different from having an UPS); you should also have a small UPS with a power-failure sensing circuit that can shut down your server when the power goes out (to avoid disk crashes).  If you can tolerate a day or longer interval of data loss, be sure you have a periodic tape or other means of backup for your system that matches or is shorter than the data loss interval, or have a RAID array and less frequent backups. Backup tapes should be stored off-site in a secured location.

Seek technical assistance for these requirements; the more highly-available and data-loss-less your server requirements, the more important it is to consult a network and computer systems engineer. Also consider web hosting services (provided your site has internet access). Many provide data backup services and rapid redeployments should there be a hardware failure. They also have the investments in power generators and likely have a more reliable connection to the internet than you do.

Dataset Size: The quantity of data you intend to collect will affect the size of the machine required to host the ODK Aggregate instance and of your database server. If you will use the built-in "Export to CSV file", "Export to KML file" or "Export to JSON file" functionality, ODK Aggregate can only generate these documents if they can fit within the size of the Java virtual machine (JVM) in which ODK Aggregate runs. This is a configuration setting within Apache Tomcat. For most applications, the default size should be fine. If you are collecting more than 6000 submissions, you may need to increase the JVM size. Note that the maximum size of the JVM is limited by the size of the physical memory on your machine; if you only have 1GB of free memory when your system is idling, ODK Aggregate will be limited to a maximum JVM size of less than 1GB. And, for very large datasets (above 100,000 records), database servers operate more efficiently if they have a large amount of physical memory.

Secure and Protected Data: If you need to prevent eavesdroppers from seeing your data as it is transmitted to your ODK Aggregate instance, you should either (1) only connect to ODK Aggregate from within your organization's network (when the ODK Collect devices are on your premises), (2) obtain an SSL certificate and install it on your Tomcat server (a certificate is required to secure transmissions over https:), or (3) use Encrypted Forms; note that encrypted forms prevent ODK Aggregate from publishing data to Google Spreadsheets or Fusion Tables and from performing any data visualizations.

If you are not using encrypted forms and are handling sensitive data, a computer security specialist should review your system and your security procedures.

When operating without an SSL certificate, your data will be transmitted over http: and potentially be visible to anyone with a network sniffer along the transmission path. In this case, to minimize potential security breaches, changing passwords should be restricted to either a browser on the server itself or from browsers on computers connected directly to your local area network (i.e., when operating without an SSL certificate, do not access ODK Aggregate from a remote location when changing passwords).

Google App Engine services provides all of these features.

Install Tomcat
The overall steps are:

Install Java 7 or Higher (if using Azure SQL Server, you must use Java 8)
Configure PATH variable
Download and Install Tomcat 8
The particulars follow.

Install Java 7 or Higher
Make sure Java 7 or higher (if using Azure SQL Server, you must use Java 8) is installed on the computer you plan to use. If it is not, download and install it.

Note that you generally need to launch installers with Run as administrator privileges (available under the right-click menu).

Accept all the defaults.

Configure PATH variable
Add the installed Java bin directory to the PATH variable.This is vaguely described here: https://www.java.com/en/download/help/path.xml. On Windows:

Go to Start menu. Right-click on Computer, select Properties.

Select Advanced system settings (on the left in Windows 7).

This should open a System Properties dialog, with the Advanced tab selected. You can also get here via the System app on the Control Panelpage.

Choose the Environment Variables... button. This opens up an environment variables dialog.

Now open a Windows file explorer, and browse to the 'bin' directory under the 'jdk...' directory of the Java software. This defaults to something like C:\Program Files\Java\jdk1.7.0_25\bin Click into the text box at the top of the Windows file explorer, and copy this directory path into the cut-and-paste buffer.

Return to the environment variables dialog. Scroll down the System variables list and select the 'Path' variable, double-click. This opens up the Edit System Variable dialog. Click on the 'Variable value:' text field. Hit your 'End' key, then type a semicolon and paste in the 'bin' directory path. Don't forget the semicolon before the paste! Hit OK.

Exit out of all the dialogs.

Download and Install Tomcat 8
Download Tomcat 8 from here: https://tomcat.apache.org/download-80.cgi

NOTE: If using ODK Aggregate v1.4.12 or earlier, you should download and install Tomcat 8 but apply the manual fix for Tomcat 8 outlined in the Unsupported Webserver Configurations section below.

If using the Windows installer, change to use port 80 for the HTTP/1.1 port. If you are going to set up an SSL certificate, change the HTTPS/1.1 port to 443. Use all other defaults.

Verify that Tomcat 8 is running by opening a browser on this server to http://localhost/ You should see the Apache Tomcat administration page. If you didn't request port 80 during the install, you will need to specify the port you chose (http://localhost:port/). If you didn't configure a port, the default port is 8080 (and 8443 for HTTPS).

Before continuing, apply or change the administrator password for Tomcat; the administration functions should be secured before advancing to Configure for Network Access.

IMPORTANT: If your system is running in a locale that uses commas for decimal points, there is a known issue that prevents ODK Aggregate from using the Map Visualization. The work-around is to configure your Tomcat server to run in a locale that uses periods for decimal points (e.g., en_US, en_GB). This can be done with Tomcat configuration settings independently of the locale of your computer.

Note: We only test and support Tomcat 8.0 and, for ODK Aggregate v1.4.12 and earlier, we only tested and supported Tomcat 6. If using ODK Aggregate v1.4.12 or earlier, you should download and install Tomcat 8 but apply the manual fix for Tomcat 8 outlined in the Unsupported Webserver Configurations section below.

Linux Installs
This tip was contributed by a user.

To ensure that the proper java settings are found by the web server, you may need to specify the '-E' flag when restarting the webserver. e.g.,

sudo apt-get install tasksel
sudo tasksell install tomcat
sudo apt-get install java7-jdk
open /.bashrc with your editor and add: export JAVA_HOME=/usr/lib/jvm/java-7-openjdk-amd64 at the bottom of that file. Change this to whatever path is appropriate for your java installation.
sudo -E /etc/init.d/tomcat8 restart
The 'E' flag on the last command is critical. It forces Ubuntu to reload the environment settings for the service, causing it to pick up the new JAVA_HOME setting.

Unsupported Webserver Configurations
ODK Aggregate v1.4.13 and higher are supported on Tomcat 8.0; these newer releases should also work, without modification on other webservers. Please let us know if your webserver requires special configuration steps.

Prior to ODK Aggregate v1.4.13, we only supported Tomcat 6.
These instructions are for ODK Aggregate v1.4.12 and earlier.

Tomcat 7, Glassfish and Jetty require additional configuration steps to run ODK Aggregate v1.4.12 and earlier; we don't support either Tomcat 7 or Glassfish or Jetty. All of these webservers require configuration settings to enable cookies under HTTPS. Otherwise, ODK Aggregate uses no special Tomcat features and it should operate correctly within any compliant Servlet 2.5 web container. From user's efforts on these other webservers:

 

Tomcat 7 (unsupported)
These instructions are for ODK Aggregate v1.4.12 and earlier.

Edit context.xml (under Tomcat 7's conf directory) to have the attribute 'useHttpOnly' set to false. I.e.,

<Context useHttpOnly="false">
...
 

Tomcat 8 (unsupported>
These instructions are for ODK Aggregate v1.4.12 and earlier.

From a user:

My ODK Aggregate file is installed as /var/lib/tomcat8/webapps/ODKAggregate.war
The following content needed to be placed in the file webapps/ODKAggregate/META-INF/context.xml (this is within the expanded content of the war file, once the Tomcat 8 server has exploded it).

<Context path="" useHttpOnly="false" />
 

Glassfish 4 (unsupported)
These instructions are for ODK Aggregate v1.4.12 and earlier.

Add glassfish-web.xml under ODK Aggregate's WEB-INF directory with the content:

<?xml version="1.0" encoding="UTF-8"?>
<glassfish-web-app>
    <session-config>
        <cookie-properties>
            <property name="cookieHttpOnly" value="false" />
        </cookie-properties>
    </session-config>
</glassfish-web-app>
 

Jetty (unsupported)
These instructions are for ODK Aggregate v1.4.12 and earlier.

Add jetty-web.xml under ODK Aggregate's WEB-INF directory with the content:

<?xml version="1.0"  encoding="ISO-8859-1"?>
<!DOCTYPE Configure PUBLIC "-//Jetty//Configure//EN" "http://www.eclipse.org/jetty/configure.dtd">

<Configure class="org.eclipse.jetty.webapp.WebAppContext">
    <Get name="sessionHandler">
        <Get name="sessionManager">
            <Set name="secureCookies" type="boolean">true</Set>
        </Get>
    </Get>
</Configure>
 

Configure for Network Access
The high-level network configuration steps are:

configure your server firewall
make your server visible on the internet (optional)
establish a DNS name for the server
If your organization has a network or systems administrator, contact them for assistance.

Configure your Server Firewall
To allow phones and other computers to access ODK Aggregate, you need to configure the server firewall to allow access.

On Windows, open the command window (Sart, search for 'cmd'. Right-click, choose 'Run as administrator'). Assuming you configured Tomcat to use port 80, within this command window, type:

netsh firewall add portopening TCP 80 "ODK Aggregate"
If you will have an SSL certificate (required for https:), repeat this command after changing 80 to 443. Or, if you used other ports, perform the command using the specific port number(s) you selected during the Tomcat install.

To verify that other computers can now access Tomcat, on Windows, from a 'cmd' window and type:

ipconfig /all
This will list all the Ethernet adapters and WiFi adapters on your system. Beneath each, you will see an IP address and other information.  e.g., there will be entries like this:

Ethernet adapter Local Area Connection:

  Connection-specific DNS Suffix  . : opendatakit.org
  IPv4 Address. . . . . . . . . . . : 192.168.15.121
  Subnet Mask . . . . . . . . . . . : 255.255.255.0
  Default Gateway . . . . . . . . . : 192.168.15.100
Go to another computer on your local area network, open a browser, and enter the IP address for your server's entry similar to the one shown above. You should see the Tomcat administration screen. For the above example, you would type this into the browser:

http://192.168.15.121
If you used a port other than port 80, you must append a ':' followed by the port on which Tomcat is running (e.g., http://192.168.15.121:8080).  If you don't see the Tomcat administration page, double-check that Tomcat is running and that your firewall and antivirus software have the port open.

Congratulations, your server is visible on your local area network!

Make your Server Visible on the Internet
If you are using a web hosting service, your server is already visible to the internet (unless the web hosting service itself requires further configuration steps).

Otherwise, once you can see Tomcat from other machines on your local area network, you have the option of making this server visible from the internet.  If the server is not visible from the internet, ODK Collect will only be able to reach your server when it is connected to your local area network or to a WiFi access point on your network (when the ODK Collect device has returned to your premises).  If your server is visible from the internet, ODK Collect will be able to use the phone carrier's network or connect to any WiFi hotspot (e.g., Starbucks) and submit or download forms from your server.

If you decide to not make your server visible on the internet, you should still assign your server a static IP address as detailed below, but do not set up port forwarding.

If you choose to make your server visible, the general steps are to set up port forwarding on any networking equipment (your routers and the Internet Service Provider (ISP)'s boxes) upstream of your computer until you reach the piece of equipment assigned the IP address through which your server will be visible on the internet. Open a browser to this site to find out what that internet-visible IP address is:

http://www.whatismyip.com/
Begin with the router to which your server is directly connected and work upwards.  If your server's IP address is 192.168.15.121, you can typically access your router's configuration page by browsing to http://192.168.15.1 -- i.e., change the last number to a one. Search the web for the user manual for your router. Cable modems generally don't have an administration page (and don't need to be configured).

Most routers are configured to be DHCP servers (to assign IP addresses to the devices connected to the router) and to have NAT (Network Address Translation) enabled (to be a firewall for the devices connected to the router).  If your router has no administration page or is configured in "Bridging Mode", it does neither of these things and can be ignored; proceed to the next piece of network equipment.

Once connected to the router administration screen, you should first go to the DHCP configuration screens and configure your server (or the router to which your server is connected, if you're working up the chain of equipment) to be assigned a static (non-changing) IP address. Follow the user guide to do so.

For a system configured with a router chain with 4 routers, two acting as DHCP servers, the end result is:

router 4 - configure to assign a static IP to router 2
  |
  V
router 3 - running in Bridging mode
  |
  V
router 2 - configure to assign a static IP to server
  |
  V
router 1 - without any administration page
  |
  V
server
Next, if NAT is enabled, there should be a screen to configure port forwarding. You should forward the Tomcat ports to the server (using the static IP address you just assigned to it). Or, to the router to which your server is connected, if you're working up the chain of equipment. If you will have an SSL certificate, you need set up forwarding of both port 80 and 443 (or the port numbers used when configuring Tomcat, if different).

For a system configured with a router chain with 4 routers, two with NAT enabled, the end result is:

router 4 - configure to forward port 80 (plus port 443 if SSL) to router 2
  |
  V
router 3 - running in Bridging mode
  |
  V
router 2 - configure to forward port 80 (plus port 443 if SSL) to server
  |
  V
router 1 - without any administration page
  |
  V
server
Once these configurations are complete, return to the administration screen of the final piece of networking equipment that you changed (e.g., router 4, above). Find the network status page for this router. It should show two IP addresses. One will be the IP address you used to get to the administration screen and the other will be labeled as either the "Internet Port" or "WAN Port" IP address (or something similar).

If this does not match the IP address reported by http://www.whatismyip.com/ then you will need to contact your ISP for further configuration.

Assuming the IP address matches, you now must consider how stable you need your connection to the internet to be. Unless you purchase a static IP address from your ISP, this IP address may change. For example, every time my laptop is restarted, it gets a different IP address (because it isn't assigned a static IP address). The same is true for this last piece of equipment (router 4). The IP address can occasionally change unless you purchase a static IP address from the ISP (it is most likely to change when rebooted or power cycled — an infrequent event for these devices).

If you need data security (e.g., an SSL certificate) or require a highly-available ODK Aggregate server, you should obtain a static IP from your ISP and configure that network equipment (router 4) to use that static IP address. Otherwise, if you don't need SSL and can tolerate periods of inaccessibility or if your surveyors can call or text when they have problems reaching the server from the field, and you will have someone with server access that is able to respond, you should be able to operate with a dynamic IP address.

To verify the settings so far, ensure that Tomcat is running, venture to a WiFi hotspot (e.g., Starbucks) outside of your organization, and browse to this IP address. You should see the Tomcat administration screen.

Congratulations, your server is visible to the internet!

Establish a DNS name for the Server
If you've completed the above configuration, your server is now reachable from anywhere in the world — provided you know its visible IP address. Or, if you have decided to only expose your server within your local area network (LAN), you have given it a static IP address (albeit one that only identifies your server when resolved in the context of your LAN).

DNS services maintain mappings of names to IP addresses; the next step is to give your server a name and assign that name to this IP address.

If your organization already has a domain name (e.g., opendatakit.org), speak with your network administrator. You will want to create a CNAME underneath this domain name (e.g., myserver.opendatakit.org, under the opendatakit.org domain) that will point to the IP address of the network equipment you configured earlier.

If you do not have a domain name, there are many free "Dynamic DNS" services. These offer the ability to give your server a domain name. You can purchase a domain name (e.g., opendatakit.org), or create a free subdomain under one of the dynamic DNS service's predefined domain names (e.g., myserver.dnsdynamic.com, under the dnsdynamic.com domain). In either case, you would then assign the IP address of the network equipment you configured earlier to this name.

Some WiMax and DSL routers (network devices supplied by your ISP or carrier), have partner DNS services that the router can continually inform of IP address changes. If you do not have a static IP address, one of these partner DNS services should be considered, as this continual update would eliminate your need to monitor and manually update the IP address of your DNS name.

Once you have set up this name and assigned it to your internet-visible IP address, you should then be able to venture to a Wi-Fi hotspot, enter this name in a browser (e.g., http://myserver.dnsdynamic.com), and see your Tomcat administration page.

Obtain and Install SSL Certificate
Refer to the documentation on the Apache Tomcat site and on a Certificate Authority site (e.g., Verisign) for how to do this. Configuration of SSL is beyond the scope of this document, but David Dyck has a two part tutorial that might help.

Select and Install Database Server
ODK Aggregate works with any of these database servers:

MySQL
PostgreSQL
Microsoft SQL Server
Azure SQL Server (requires Java 8)
A database server manages one or more databases. The database server stores and retrieves data from tables within these databases. Tables are organized as columns and rows (similar to an Excel worksheet). ODK Aggregate parses and stores submissions as individual rows in a data table, one data value per column within that table.

MySQL is a defacto standard for open source development, so there are many database administrators and software professionals that understand and use it.

PostgreSQL is less common, but has found a large following in geographical information systems (GIS) applications.

Both are free and open source.

Microsoft SQL Server and Microsoft's cloud product, Azure SQL Server, are licensed; "free" versions of these will have reduced functionality that can impact performance.

Despite MySQL's overwhelming prevalence, PostgreSQL should be considered for GIS applications or if you require very large forms. MySQL imposes a limit of 4096 columns per table and 65536 bytes per row. The later limitation is the most severe. If you have a form with more than 256 select1, string or barcode questions, it will likely require two or more tables to store all the data values because the 65536 byte limit will have been exceeded (unless you use the odk:length attribute to shorten the maximum length of these fields to less than the default 255 characters). Even when large forms are split across tables, ODK Aggregate can store and publish them; however, filtering by column value will be impacted. Only columns in the first table can be used in filters during visualization and when exporting to CSV or KML; columns in the second and subsequent overflow tables will cause the filter to fail.

For MySQL, download and install MySQL Community Server 5.7 or higher from MySQL download site. Be sure to set a root password for the database. Stop the MySQL database server, then configure the database (via the "my.cnf" or the "my.ini" file) with these lines added to the [mysqld] section:

   character_set_server=utf8
   collation_server=utf8_unicode_ci
   max_allowed_packet=1073741824
and restart the MySQL databaseserver. Then, download the MySQL Connector/J, unzip it, and copy the mysql-connector-java-x.x.x-bin.jar file into the Tomcat server's libs directory. After copying it into that directory, you should stop and restart the Tomcat server. The max_allowed_packet setting defines the maximum size of the communications buffer to the server. The value used in the snippet above is 1GB, the maximum value supported. For ODK Aggregate 1.4.11 through 1.4.7, and 1.2.x, the maximum media (e.g., image or video) attachment is limited to the value you set for max_allowed_packet minus some unknown overhead -- e.g., a storage size of something less than 1GB. For ODK Aggregate 1.4.6 and earlier (excluding 1.2.x), the maximum media attachment is unlimited and the setting for max_allowed_packet does not need to be specified. For ODK Aggregate 1.4.12 and later, the max_allowed_packet value should be set to a value greater than 16842752 (this is the minimum value that should be used -- 16MB plus 64kB); with that setting, media attachments of unlimited size are once again supported. If you are upgrading to a newer ODK Aggregate, you must continue to use the setting you already have, or 16842752, whichever is greater. If you experience problems uploading large attachments, change this setting to its maximum value, 1073741824.

Finally, if you are using MySQL 5.7 or later, some of releases expire all database passwords after 360 days. Please verify the behavior of your version of MySQL and either change the password expiration policy or create a calendar reminder to change the password before it expires. For ODK Aggregate, you will need to re-run the installer to specify the new password. If you need to do that, this might be a good time to upgrade to the newest version of ODK Aggregate. For more information, see the MySQL documentation. e.g., MySQL password expiration policy.

For PostgreSQL, download and install the appropriate binary package from PostgreSQL download site. Be sure to set the password for the postgres (root) user. And set the default character set and collation sequence.

For either database, you should ensure that the default character set is configured to be UTF-8 and that the collation sequence (dictionary order) is set appropriately for your circumstances. If it isn't, any non-Latin characters may display as question marks. Refer to the character set and collation sections of your database's documentation for how to do this.

If you need high-availability, consult with a database administrator as to whether or not you require slave databases or database clusters.

For Microsoft SQL Server or Azure SQL Server, you should configure these with UTF-8 character sets and to use Windows authentication. When using Windows authentication, the user under which the webserver executes must be granted permissions to access the SQL Server instance. The install wizard for ODK Aggregate will produce a Readme.html file that contains additional information on how to complete the configuration of the database and webserver service.

Install ODK Aggregate
Download ODK Aggregate v1.N.N. Select the latest Featured release for your operating system. These downloads are wizard-based installers for the various operating systems. If you are running OSX, you must unzip the downloaded file before running the installer within it. Consider using a non-Featured release during forms development (to help us identify issues prior to a production release).

The installer does not install anything, but will guide you through configuring ODK Aggregate for Tomcat and MySQL/PostgreSQL/SQLServer. It can be run on any machine. The installer will produce a WAR file (web archive) containing the configured ODK Aggregate server, a create_db_and_user.sql script for creating the database and user that ODK Aggregate will use to access this database, and a Readme.html file with instructions on how to complete the installation.

When asked for the fully qualified hostname of the ODK Aggregate server, you should enter the DNS name you established above. The install also asks for a database name, user and password. The user should not be root (MySQL) or postgres (PostgreSQL). ODK Aggregate will use this user when accessing this database (and it will only access this database). By specifying different databases and users, you can set up multiple ODK Aggregate servers that share the same database server, store their data in different databases, and operate without interfering with each other.

If you are upgrading to a newer version of ODK Aggregate, as long as you specify the same database name, user and password, you do not need to re-run the create_db_and_user.sql script (it only needs to be executed once).

See here for information on logging onto your ODK Aggregate instance and changing the access permissions of the server.

Multi-host and Load-balancing Considerations
If you are setting up multiple servers behind a load balancer, the load balancer should be configured to be sticky, rather than round-robin, so that requests bias toward the same server that fielded earlier requests from that browser (and device).

ODK Aggregate maintains various non-shared in-memory caches that can be out-of-date for short intervals across multiple running instances which can cause short periods of refresh glitches or other anomalies if two or more servers are responding to requests from a single browser or device. Typically, these caches expire and are refreshed roughly every 6 seconds.

The correctness and logical functioning of the system is not affected by the running of multiple server instances because there are various global database locks that are held to ensure these independent servers don't alter the same data records at the same time, and, when critically important, data is directly fetched from the database instead of from the cache.

Other Resources
Aggregate AWS Install
How to install and configure ODK Aggregate on CentOS / RHEL Using AWS EC2 Instance
Aggregate Synology NAS with DSM 6 Install

TRANSLATE
Select Language​▼
NAVIGATE
Back to Aggregate

Data Transfer
Deployment Planning
Tomcat Install
OAuth2 Service Account
Open Data Kit
