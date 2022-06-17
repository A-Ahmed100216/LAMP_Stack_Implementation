# Project 1 Implementation

## Pre-Requisites 
* Launch and connect to EC2 instance 

## 1. Installing Apache and Updating the Firewall
* Apache HTTP Server - fast, reliable, and secure open source web server software. 
* Apache can be installed using the 'apt' package manager:
```bash
sudo apt updates
sudo apt install apache 2
# Verify apache2 is running
sudo systemctl status apache2
```
![apache](/Project1/images/apache_status.png)

* The Security Group must be updated to allow inbound connection through port 80. Once complete, the server can be accessed from the internet. 
![security group](Project1/images/security_groups.png) 
* This can be confirmed by running the following commands locally ...
```bash
curl http://localhost:80
curl http://127.0.0.1:80
```
![curl localhost](Project1/images/curl_localhost.png)

* ... or opening a web browser and navigating to the public IP address of the instance (this can be found under instance details or running the following command `curl -s http://169.254.169.254/latest/meta-data/public-ipv4`)
![apache default webpage](Project1/images/apache_default_webpage.png)

## 2. Installing MySQL 
* MySQL is a popular Relational Database Management System 
* To install 
```
sudo apt install mysql-server
``` 
* Once complete, run the following command to run a security script which removes some default settings. 
```
sudo mysql_secure_installation
```
* This should prompt a VALIDATE PASSWORD PLUGIN which can be enabled. Following this, the server will prompt a password for MySQL root user. If the PLUGIN has been enabled, the guidelines should be followed, but if not, a strong password is still recommended. Following this, the server will prompt several further questions which can be answered as Y/yes. 
### Blocker
* When running the secure installation, the following error was observed 
![Blocker](Project1/images/SQL_Blocker.png)
* This was resolved by running `sudo mysql` and running the following query to set the password 
```sql
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password by 'mynewpassword'
```
![Resolve](Project1/images/Resolve_Blocker.png)
* Following this, the secure installation can be run with the password set in the SQL command above 
![Secure installation](Project1/images/Secure_SQL_installation.png)

## 3. Installing PHP
* PHP is a programming language which is used to process code.
* In addition to this, the php-mysql package needs installing to communicate with MySQL. Likewise, libapache2-mod-php enables Apache to handle PHP files.
* To install 
```
sudo apt install php libapache2-mod-php php-mysql
```

## 4. Creating a Virtual Host
* Create a domain called projectlamp in the /var/www/html directory, next to the default configuration 
```
sudo mkdir /var/www/projectlamp
```
* The current system user is assigned ownership
```
sudo chown -R $USER:$USER /var/www/projectlamp
```
* The next step is to create a new config file in Apache's site-available directory with a very simple config
```bash
sudo vi /etc/apache2/sites-available/projectlamp.conf

# Paste inside file 
<VirtualHost *:80>
    ServerName projectlamp
    ServerAlias www.projectlamp 
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/projectlamp
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

![Virtual Host](Project1/images/virtual_host_setup.png)

* To enable a new virtual host, use the command `a2ensite`
```
sudo a2ensite projectlamp
```
* To be sure, the default configuration can be disabled
```
sudo a2dissite 000-default
```
* Confirm the new config file does not have any syntax issues
```
sudo apache2ctl configtest
```
* Reload Apache for changes to take effect 
```
sudo systemctl reload apache2
```
![Enable New Virtual Host](Project1/images/Enable_virtual_host.png)

* The website is now active but contains no content so navigate back to /var/www/projectlamp and create and index.html file to render a very basic webpage.
```bash
sudo echo 'Hello LAMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectlamp/index.html
```
* The webpage should display the following output
![Basic Webpage](Project1/images/basic_webpage.png)

## Enable PHP
* Index.html files take precedence over an index.php file. This order can be changed in the DirectoryIndex directive:
```
sudo vim /etc/apache2/mods-enabled/dir.conf
```
* Before...
![Before](Project1/images/Directory_index_directive_before.png)
* After ...
![After](Project1/images/Directory_index_directive_after.png)

* Reload apache for changes to take effect 
```
sudo systemctl reload apache2
```
* To test PHP we can create a simple test script called index.php in the custom web root folder (/var/www/projectlamp)
```bash
vim /var/www/projectlamp/index.php

# Paste in file
<?php
phpinfo();
```
* Save and refresh browser...
![php Info](Project1/images/php_webpage.png)

## Close
* Once complete, the index.php file should be removed as this contains sensitive information about the server.
* The EC2 instance should be stopped to avoid incurring charges.


