## Table of contents
[1.Apache Tomcat](#Apache-Tomcat)

[2.Install JDK](#Install-JDK)

[3.Install Tomcat 9](#Install-Tomcat-9)


## Apache Tomcat 
* Apache Tomcat is an open-source application server developed by the Apache Software Foundation. It serves as a Java Servlet and JavaServer Pages (JSP) container, providing a reliable platform for deploying and managing Java-based web applications. In essence, Tomcat functions as a web server that facilitates the execution of dynamic content.

* It can handle both static and dynamic pages.The static pages are generated using HTML and dynamic pages are generated using servelet and JSP(Java Server Pages).

* It can be used only for hosting java based code.

* It is slower as comparing to Apace web server.

## Install JDK
## Step 1 — Installing JDK

**Tomcat 9 requires Java 8 or later versions to work. You can check and verify that Java is installed with the following command.**

```
java -version
```
If Java is not installed run the below commands to install Java.
```
sudo apt-get update
sudo apt-get install default-jdk -y
```

*After installation check, if java is installed correctly by executing below command.*

```
java -version
```
**Output**
* In my case java is already installed 
```
rajshakya@thunder:/$ sudo apt-get install default-jdk -y
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
default-jdk is already the newest version (2:1.11-72build2).
0 upgraded, 0 newly installed, 0 to remove and 4 not upgraded.
rajshakya@thunder:/$ 

```


*If java is installed, the output should look similar to above depending upon what is the latest version of java at that time.*
## Install Tomcat 9
## Step 1 — Creating a Tomcat user and group

* We should not run tomcat under the root user for security reasons. Let’s create a group tomcat and add a user tomcat to it. Additionally, we are going to install tomcat under /opt/tomcat directory which will be tomcat user home directory:

```
sudo groupadd tomcat
sudo useradd -s /bin/false -g tomcat -d /opt/tomcat tomcat
```
## Step 2 — Install Tomcat 9

* Now we need to download and install Tomcat 9. You need to download the Tomcat 9 archive from its official download website or mirrors. Download and Install Tomcat 9.0.21 archive file using the following commands. You can also visit the official download page to download the latest available version.

* We are going to install Tomcat to /opt directory. So we will download the Tomcat 9.0.21 package to that location.

* Change directory to /opt and download Tomcat 9 to that directory.

```
cd /opt

sudo wget https://archive.apache.org/dist/tomcat/tomcat-9/v9.0.21/bin/apache-tomcat-9.0.21.tar.gz
```
**Output**
```
rajshakya@thunder:/opt$ sudo wget https://archive.apache.org/dist/tomcat/tomcat-9/v9.0.21/bin/apache-tomcat-9.0.21.tar.gz
--2023-12-16 12:04:38--  https://archive.apache.org/dist/tomcat/tomcat-9/v9.0.21/bin/apache-tomcat-9.0.21.tar.gz
Resolving archive.apache.org (archive.apache.org)... 65.108.204.189, 2a01:4f9:1a:a084::2
Connecting to archive.apache.org (archive.apache.org)|65.108.204.189|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 10874669 (10M) [application/x-gzip]
Saving to: ‘apache-tomcat-9.0.21.tar.gz’

apache-tomcat-9.0.21.tar 100%[==================================>]  10.37M  2.04MB/s    in 8.7s    

2023-12-16 12:04:48 (1.19 MB/s) - ‘apache-tomcat-9.0.21.tar.gz’ saved [10874669/10874669]


```

**Extract the tar package and rename the extracted directory to tomcat.**

```
sudo tar -xvzf apache-tomcat-9.0.21.tar.gz
sudo mv apache-tomcat-9.0.21 /opt/tomcat
```

**Output**
```
rajshakya@thunder:/opt$ sudo tar -xvzf apache-tomcat-9.0.21.tar.gz
apache-tomcat-9.0.21/conf/
apache-tomcat-9.0.21/conf/catalina.policy
apache-tomcat-9.0.21/conf/catalina.properties
apache-tomcat-9.0.21/conf/context.xml
apache-tomcat-9.0.21/conf/jaspic-providers.xml
apache-tomcat-9.0.21/conf/jaspic-providers.xsd
apache-tomcat-9.0.21/conf/logging.properties
apache-tomcat-9.0.21/conf/server.xml
apache-tomcat-9.0.21/conf/tomcat-users.xml
apache-tomcat-9.0.21/conf/tomcat-users.xsd
apache-tomcat-9.0.21/conf/web.xml
apache-tomcat-9.0.21/bin/
apache-tomcat-9.0.21/lib/
apache-tomcat-9.0.21/logs/
apache-tomcat-9.0.21/temp/
apache-tomcat-9.0.21/webapps/
apache-tomcat-9.0.21/webapps/ROOT/
apache-tomcat-9.0.21/webapps/ROOT/WEB-INF/
apache-tomcat-9.0.21/webapps/docs/
apache-tomcat-9.0.21/webapps/docs/WEB-INF/
apache-tomcat-9.0.21/webapps/docs/api/
apache-tomcat-9.0.21/webapps/docs/appdev/
apache-tomcat-9.0.21/webapps/docs/appdev/sample/
```
```
rajshakya@thunder:/opt$ sudo mv apache-tomcat-9.0.21 /opt/tomcat
```

 
## Step 3 — Change Permission and Ownership of the Tomcat home directory

* Next, we will modify ownership and permission of /opt/tomcat directory. We will also give executed permission to opt/tomcat/bin/ directory.

```
sudo chown -R tomcat: /opt/tomcat/bin/
```
**Output**
```
rajshakya@thunder:/$ sudo chown -R tomcat: /opt/tomcat/bin/
rajshakya@thunder:/$ 
```

## Step 4 — Creating a SystemD Service File for Tomcat

* To install Tomcat as system service we will create a file called tomcat.service in the /etc/systemd/system directory.

```
sudo vim /etc/systemd/system/tomcat.service
```

* Add the following to tomcat.service file

```

[Unit]
Description=Tomcat 9 Server
After=network.target
[Service]
Type=forking
User=tomcat
Group=tomcat
Environment="JAVA_HOME=/usr/lib/jvm/default-java"
# java 17 path /usr/lib/jvm/java-1.11.0-openjdk-amd64
Environment="JAVA_OPTS=-Xms512m -Xmx512m"
Environment="CATALINA_BASE=/opt/tomcat"
Environment="CATALINA_HOME=/opt/tomcat"
Environment="CATALINA_PID=/opt/tomcat/temp/tomcat.pid"
Environment="CATALINA_OPTS=-Xms512M -Xmx1024M -server -XX:+UseParallelGC"
ExecStart=/opt/tomcat/bin/startup.sh
ExecStop=/opt/tomcat/bin/shutdown.sh
# use when you want to run as root ExecStart=/bin/sh -c '/opt/tomcat/bin/startup.sh'
# use when you want to run as root ExecStop=/bin/sh -c '/opt/tomcat/bin/shutdown.sh'
UMask=0007
RestartSec=10
Restart=always
[Install]
WantedBy=multi-user.target
```

**Save and exit.**

* Restart systemctl daemon.
```
sudo systemctl daemon-reload
```
* To start the tomcat service.
```
sudo systemctl start tomcat
```
* To monitor the Tomcat log file.
```
sudo tail -f /opt/tomcat/logs/catalina.out
```
* If there is no error, you will get similar to below output:

**Output**
```
rajshakya@thunder:/opt$ sudo tail -f /opt/tomcat/logs/catalina.out
16-Dec-2023 12:09:58.095 INFO [main] org.apache.catalina.startup.HostConfig.deployDirectory Deployment of web application directory [/opt/tomcat/webapps/docs] has finished in [28] ms
16-Dec-2023 12:09:58.096 INFO [main] org.apache.catalina.startup.HostConfig.deployDirectory Deploying web application directory [/opt/tomcat/webapps/ROOT]
16-Dec-2023 12:09:58.122 INFO [main] org.apache.catalina.startup.HostConfig.deployDirectory Deployment of web application directory [/opt/tomcat/webapps/ROOT] has finished in [26] ms
16-Dec-2023 12:09:58.122 INFO [main] org.apache.catalina.startup.HostConfig.deployDirectory Deploying web application directory [/opt/tomcat/webapps/manager]
16-Dec-2023 12:09:58.163 INFO [main] org.apache.catalina.startup.HostConfig.deployDirectory Deployment of web application directory [/opt/tomcat/webapps/manager] has finished in [41] ms
16-Dec-2023 12:09:58.163 INFO [main] org.apache.catalina.startup.HostConfig.deployDirectory Deploying web application directory [/opt/tomcat/webapps/host-manager]
16-Dec-2023 12:09:58.193 INFO [main] org.apache.catalina.startup.HostConfig.deployDirectory Deployment of web application directory [/opt/tomcat/webapps/host-manager] has finished in [30] ms
16-Dec-2023 12:09:58.199 INFO [main] org.apache.coyote.AbstractProtocol.start Starting ProtocolHandler ["http-nio-8080"]
16-Dec-2023 12:09:58.216 INFO [main] org.apache.coyote.AbstractProtocol.start Starting ProtocolHandler ["ajp-nio-8009"]
16-Dec-2023 12:09:58.237 INFO [main] org.apache.catalina.startup.Catalina.start Server startup in [833] milliseconds

```

* We can now access tomcat at:
```
http://localhost:8080/
```
**Tomcat home screen**

![Screenshot from 2023-12-16 13-32-57](https://github.com/shakyaraj9569/Tomcat_document/assets/118537904/9adaab04-a402-48f7-97c2-94a1450a1134)





**To enable Tomcat service on system boot:**
```
sudo systemctl enable tomcat
```

**The default Tomcat port is 8080, so we need to allow that port on Ubuntu firewall.**
```
sudo ufw allow 8080/tcp
```

* Check firewall status:

```
sudo ufw status
```

**Output**
```
rajshakya@thunder:/$ sudo ufw status
Status: inactive
rajshakya@thunder
```
  **Enable ufw**

```
sudo systemctl enable ufw
sudo systemctl start ufw
```
**Output**
```
rajshakya@thunder:/$ sudo systemctl enable ufw
Synchronizing state of ufw.service with SysV service script with /lib/systemd/systemd-sysv-install.
Executing: /lib/systemd/systemd-sysv-install enable ufw
rajshakya@thunder:/$ sudo systemctl status ufw
● ufw.service - Uncomplicated firewall
     Loaded: loaded (/lib/systemd/system/ufw.service; enabled; vendor preset: enabled)
     Active: active (exited) since Sat 2023-12-16 10:21:54 IST; 3h 15min ago
       Docs: man:ufw(8)
   Main PID: 619 (code=exited, status=0/SUCCESS)
        CPU: 3ms

Dec 16 10:21:54 thunder systemd[1]: Starting Uncomplicated firewall...
Dec 16 10:21:54 thunder systemd[1]: Finished Uncomplicated firewall.
```

## Step 5 — Enable and Secure Tomcat Web Application and Virtual Host Manager

* Tomcat has a Web Application Manager and Virtual Host Manager app that come preinstalled. In order to use these, we have to first secure them with authentication and authorization. This is done via the tomcat-users.xml file. Open and edit tomcat-users.xml:

```
sudo vim /opt/tomcat/conf/tomcat-users.xml
```
* Add the following between <tomcat-users> tags and save. The roles required to access Web Application Manager and Virtual Host Manager are manager-gui and admin-gui respectively.

*In this case you have to change your username and password according to you*

```
<role rolename="admin-gui"/><role rolename="manager-gui"/><user username="admin" password="password" roles="admin-gui,manager-gui"/>
```

![image](https://github.com/shakyaraj9569/Tomcat_document/assets/118537904/906424b3-e4d8-47a0-9169-346d36f7c136)


## Step 6 — Configure Tomcat Web Application Manager

* The Tomcat web application manager is configured to allow access only from the localhost. To allow remote access we have to edit the /opt/tomcat/webapps/manager/META-INF/context.xml

* This is not recommended for production environments!!

* Open the context.xml file
```
sudo vim /opt/tomcat/webapps/manager/META-INF/context.xml
```

* Comment the lines as shown below

```
 <Valve className="org.apache.catalina.valves.RemoteAddrValve"
        allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1" />
```

![image](https://github.com/shakyaraj9569/Tomcat_document/assets/118537904/c7bb639e-c4b7-4420-aa17-2480b0381892)



* Save and exit.

* Restart tomcat service for changes to take effect.
```
sudo systemctl restart tomcat
```

* After Tomcat is restarted we can access the Web Application Manager console at the following link.


```
http://localhost:8080/manager/html
```
* Enter the user and password as configured in Step 5.
![image](https://github.com/shakyaraj9569/Tomcat_document/assets/118537904/fa42b04a-a5a2-4522-9926-0d75c2ccb617)

## Step 8 — Configure Tomcat Virtual Host Manager

* The Tomcat virtual host manager is configured to allow access only from the localhost. To allow remote access we have to edit the

* /opt/tomcat/webapps/host-manager/META-INF/context.xml

* This is not recommended for production environments!!

* Open the context.xml file
```
sudo vim /opt/tomcat/webapps/host-manager/META-INF/context.xml
```![Alt text](<Screenshot from 2023-12-16 15-15-51.png>)


* Comment the lines as shown below:

```
 <Valve className="org.apache.catalina.valves.RemoteAddrValve"
        allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1" />
```

![image](https://github.com/shakyaraj9569/Tomcat_document/assets/118537904/8deafb08-acea-4145-a7eb-c5756c987f46)



* Save and exit.

* Restart tomcat service for changes to take effect.
```
sudo systemctl restart tomcat
```
After Tomcat is restarted we can access the Virtual Host Manager console at the following link.

```
http://localhost:8080/host-manager/html
```
![image](https://github.com/shakyaraj9569/Tomcat_document/assets/118537904/3ac63190-fddf-4ddf-9da5-fa1699f95e5c)

