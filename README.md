# Apache Tomcat Installation and Configuration on Ubuntu

This guide outlines the steps to install, configure, and deploy a sample application on **Apache Tomcat 10** using **Ubuntu Linux**.

## 🧰 Prerequisites

- Ubuntu server (20.04 or newer recommended)
- Root or sudo privileges
- Internet access

---

## 🚀 Install Tomcat and Java
nstalling and configuring Apache Tomcat on a Debian-based Linux system:

Update package lists to ensure the system has the latest information about available packages.
```bash
sudo apt update
```
Install the default Java Development Kit (JDK), which is required to run Tomcat.
```bash
sudo apt install default-jdk -y
```
Create a dedicated group (tomcat) to manage permissions securely.
```bash
sudo groupadd tomcat
```
Create a non-login user (tomcat) assigned to the tomcat group, with /opt/tomcat as the home directory. This user will be used to run the Tomcat service, improving system security by isolating privileges.
```bash
sudo useradd -s /bin/false -g tomcat -d /opt/tomcat tomcat
```

### Download and extract the latest version of Tomcat:

```bash
wget https://dlcdn.apache.org/tomcat/tomcat-10/v10.1.41/bin/apache-tomcat-10.1.41.tar.gz

sudo tar xzvf apache-tomcat-10.1.41.tar.gz -C /opt/tomcat --strip-components=1

sudo chown -R tomcat:tomcat /opt/tomcat/
sudo chmod -R u+x /opt/tomcat/bin
```

## 🔐 Configure Admin Users
Edit the tomcat-users.xml file:
```bash
sudo vim /opt/tomcat/conf/tomcat-users.xml
```
Add the following lines before "</ tomcat-users>" tag:
```bash
<role rolename="manager-gui"/>
<user username="manager" password="manager_password" roles="manager-gui"/>

<role rolename="admin-gui"/>
<user username="admin" password="admin_password" roles="manager-gui,admin-gui"/>
```

## 🔧 Enable Remote Access to Management Interfaces
To allow web access to the manager and host-manager apps, edit:
```bash
sudo vim /opt/tomcat/webapps/manager/META-INF/context.xml
```
Comment out or remove the <Valve> line:
```bash
<!--
<Valve className="org.apache.catalina.valves.RemoteAddrValve"
       allow="127\.\d+\.\d+\.\d+|::1"/>
-->
```
Repeat the same for:
```bash
sudo vim /opt/tomcat/webapps/host-manager/META-INF/context.xml
```
## ⚙️ Create a systemd Service
Check installed Java alternatives:
```bash
sudo update-java-alternatives -l
```
the Java installation package path (JAVA_HOME):


java-1.11.0-openjdk-amd64      1111       <code> /usr/lib/jvm/java-1.11.0-openjdk-amd64 </code>

Create and edit a new service file:
```bash
sudo vim /etc/systemd/system/tomcat.service
```

Add the following content:
Environment="JAVA_HOME=<code> /usr/lib/jvm/java-1.11.0-openjdk-amd64 </code>

```bash
[Unit]
Description=Tomcat
After=network.target

[Service]
Type=forking

User=tomcat
Group=tomcat

Environment="JAVA_HOME=/usr/lib/jvm/java-1.11.0-openjdk-amd64"
Environment="JAVA_OPTS=-Djava.security.egd=file:///dev/urandom"
Environment="CATALINA_BASE=/opt/tomcat"
Environment="CATALINA_HOME=/opt/tomcat"
Environment="CATALINA_PID=/opt/tomcat/temp/tomcat.pid"
Environment="CATALINA_OPTS=-Xms512M -Xmx1024M -server -XX:+UseParallelGC"

ExecStart=/opt/tomcat/bin/startup.sh
ExecStop=/opt/tomcat/bin/shutdown.sh

RestartSec=10
Restart=always

[Install]
WantedBy=multi-user.target
```

Reload systemd and start the service:
```bash
sudo systemctl daemon-reload
sudo systemctl start tomcat
sudo systemctl status tomcat
```
Tomcat is <code> active (running) </code> :
```bash
 systemctl status tomcat.service
● tomcat.service - Tomcat
     Loaded: loaded (/etc/systemd/system/tomcat.service; enabled; vendor preset: enabled)
     Active: active (running) since Wed 2025-05-28 09:09:10 UTC; 37min ago
    Process: 681 ExecStart=/opt/tomcat/bin/startup.sh (code=exited, status=0/SUCCESS)
   Main PID: 695 (java)
      Tasks: 30 (limit: 2237)
     Memory: 203.1M
        CPU: 14.781s
     CGroup: /system.slice/tomcat.service
             └─695 /usr/lib/jvm/java-1.11.0-openjdk-amd64/bin/java -Djava.util.logging.config
```
## 🌐 Access the Tomcat Web Interface
Allow Tomcat through the firewall:
```bash
sudo ufw allow 8080
```

Open your browser and navigate to:
```bash
http://your_server_ip:8080
```
<p align="center">
  <a name="top" href="#">
     <img alt="xshin404/lampTermux" height="50%" width="90%" src="https://github.com/mehdi-khaksari/tomcat-ubuntu-setup/blob/main/Images/home%20page.png"/>
  </a>
</p>

```bash
http://your_server_ip:8080/manager/status
```
<p align="center">
  <a name="top" href="#">
     <img alt="xshin404/lampTermux" height="50%" width="90%" src="https://github.com/mehdi-khaksari/tomcat-ubuntu-setup/blob/main/Images/app%20manager.png"/>
  </a>
</p>

## 📦 Deploy a Sample WAR File
Download and deploy a sample .war file:
```bash
cd /opt/tomcat/webapps

sudo wget https://tomcat.apache.org/tomcat-7.0-doc/appdev/sample/sample.war
```

Access the deployed app:
```bash
http://your_server_ip:8080/sample
```
<p align="center">
  <a name="top" href="#">
     <img alt="xshin404/lampTermux" height="50%" width="90%" src="https://github.com/mehdi-khaksari/tomcat-ubuntu-setup/blob/main/Images/warfile.png"/>
  </a>
</p>

### ✅ Done!
You have successfully set up Apache Tomcat 10 on Ubuntu, configured admin users, enabled remote management, and deployed a sample web application.



# 📬 Contact
Feel free to fork, improve, and contribute! 

Author: mehdi khaksari Email: mahdikhaksari36@gmail.com
