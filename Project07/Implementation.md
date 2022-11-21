# Project 7 - DevOps Tooling Website Solution 

This project consists of the following components:
* Infrastructure: AWS
* WebServer Linux: Red Hat Enterprise Linux 8
* Database Server: Ubuntu 20.04 + MySQL
* Storage Server: Red Hat Enterprise Linux 8 + NFS Server
* Programming Language: PHP
* Code Repository: GitHub

## 1. Prepare NFS Server
1. Launch an instance using the RHEL Linux 8 Operating System. This will be the NFS server, name as such. 
2. Configure LVM on this server. See Project 6 for a thorough guide but the overall steps are as follows:
    a. Create volumes in same AZ and attach to instance. Confirm attachments by running `lsblk`
    b. Create partitions using the gdisk utility
    ```
    sudo gdisk /dev/nvme1n1
    sudo gdisk /dev/nvme2n1
    sudo gdisk /dev/nvme3n1
    ```
    ![partitions](/Project07/images/partitions.png)
    c. Create pyhsical volumes:
    ```
    sudo yum install lvm2
    sudo pvcreate /dev/nvme1n1p1
    sudo pvcreate /dev/nvme2n1p1
    sudo pvcreate /dev/nvme3n1p1
    sudo pvs
    ```
    ![physcial volumes](/Project07/images/physical_vols.png)
    d. Create a volume group called `webdata-vg`
    ```
    sudo vgcreate webdata-vg /dev/nvme1n1p1 /dev/nvme2n1p1 /dev/nvme3n1p1
    sudo vgs
    ```
    e. Create 3 logical volumes - `lv-apps`,`lv-opt`, and `lv-logs`
    ```
    sudo lvcreate -n lv-apps -L 9G webdata-vg
    sudo lvcreate -n lv-opt -L 9G webdata-vg
    sudo lvcreate -n lv-logs -L 9G webdata-vg
    sudo lvs
    ```
    ![logical volumes](/Project07/images/logical_vols.png)
    f. Confirm setup 
    ```
    sudo vgdisplay -v
    sudo lsblk
    ```
    6. Format discs using the XFS filesystem. This is a high performance, 64-bit, journalling filesystem.
    ```bash
    sudo mkfs -t xfs /dev/webdata-vg/lv-apps
    sudo mkfs -t xfs /dev/webdata-vg/lv-opt
    sudo mkfs -t xfs /dev/webdata-vg/lv-logs
    ``` 
    ![disc formatting](/Project07/images/disk_format.png)
    7. Create mount points for the logical volumes on the `/mnt` directory.
    ```bash
    # Navigate to /mnt directory and create directories
    cd /mnt/
    sudo mkdir apps logs opt

    # Mount lv-apps on /mnt/apps for webserver
    sudo mount /dev/webdata-vg/lv-apps /mnt/apps
    # Mount lv-logs on /mnt/logs for webserver logs
    sudo mount /dev/webdata-vg/lv-logs /mnt/logs
    # Mount lv-opt on /mnt/opt for use in Project 8
    sudo mount /dev/webdata-vg/lv-opt /mnt/opt
    ```
    ![NFS Server mounts](/Project07/images/nfs_mounts.png)


3. Install the NFS Server and configure to ensure it is running upon reboot. 
```bash
sudo yum -y update
sudo yum install nfs-utils -y
sudo systemctl start nfs-server.service
sudo systemctl enable nfs-server.service
sudo systemctl status nfs-server.service
```
![Install NFS Server and enable](/Project07/images/install_nfs.png)
4. Set up permissions enabling Webservers to read, write and execute files on NFS:
```bash
sudo chown -R nobody: /mnt/apps
sudo chown -R nobody: /mnt/logs
sudo chown -R nobody: /mnt/opt

sudo chmod -R 777 /mnt/apps
sudo chmod -R 777 /mnt/logs
sudo chmod -R 777 /mnt/opt

sudo systemctl restart nfs-server.service
```
![Permissions](/Project07/images/permissions.png)
6. Configure access to NFS for clients within the same subnet by exporting mounts. The subnet CIDR can be located on the AWS Management Console - click the Instance, select Networking tab, select Subnet ID. 
```bash
# Navigate to file
sudo vi /etc/exports
# Paste below in file
/mnt/apps <Subnet-CIDR>(rw,sync,no_all_squash,no_root_squash)
/mnt/logs <Subnet-CIDR>(rw,sync,no_all_squash,no_root_squash)
/mnt/opt <Subnet-CIDR>(rw,sync,no_all_squash,no_root_squash)
# Save and Exit file 
sudo exportfs -arv
```
![export mounts](/Project07/images/export_mounts.png)
7. Amend Security Group Settings:
    a. Check which port is used by NFS `rpcinfo -p | grep nfs` and enable incoming connections from webservers within the subnet CIDR 
    ![NFS Port](/Project07/images/NFS_port.png)
    b. In order for NFS server to be accessible from your client, open the following ports -TCP 111, UDP 111, UDP 2049 - to the Subnet CIDR address
    ![NFS Security Groups](/Project07/images/NFS_sg.png)


## 2. Configure DB Server 
1. Launch an EC2 instance using an Ubuntu AMI. Make sure the subnet is in the same AZ (Availability Zone) as the NFS Server. This will be important later as access will be granted to Webservers within  the same Subnet.  
2. Install mysql-server. (NB: Make sure to use `apt-get` pacakge manager as this is an ubuntu instance.) 
```bash
sudo apt-get update
sudo apt-get install mysql-server
```
3. Confirm mysql-server is running. By default, it should be running but if not, it may need enabling and/or starting
```bash
sudo systemctl status mysql
# To enable
sudo systemctl enable mysql
# To start 
sudo systemctl start mysql
```
![mysql status](/Project07/images/mysql_status.png)
3. Connect to mysql and create a database called `tooling`
```bash
sudo mysql
```
```sql
CREATE DATABASE tooling;
```
4. Create a database user and name it `webaccess`. Set the host as the Subnet CIDR address.
```sql
CREATE USER `webaccess`@`<SUBNET_CIDR>` IDENTIFIED BY 'mypass';
```
5. Grant permission to webaccess user on tooling database to do anything only from the webservers subnet cidr
```sql
GRANT ALL ON tooling.* TO 'webaccess'@'172.31.32.0/20';
FLUSH PRIVILEGES;
```

## 3. Prepare Webservers
1. Launch a new EC2 instance with RHEL 8 Operating System. Make sure the subnet is in the same AZ as the other resources. 
2. Install NFS client
```
sudo yum install nfs-utils nfs4-acl-tools -y
```
![Install NFS Client](/Project07/images/install_NFS_client.png)
3. Mount /var/www/ and target the NFS server’s export for apps
```
sudo mkdir /var/www
sudo mount -t nfs -o rw,nosuid <NFS-Server-Private-IP-Address>:/mnt/apps /var/www
```
![mount /var/www](/Project07/images/mount_var_www.png)
4. Verify that NFS was mounted successfully by running df -h. Make sure that the changes will persist on Web Server after reboot by navigating to /etc/fstab file and adding the following line
```
<NFS-Server-Private-IP-Address>:/mnt/apps /var/www nfs defaults 0 0
```
5. Install Remi’s repository, Apache and PHP
```
sudo yum install httpd -y
sudo dnf install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
sudo dnf install dnf-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm
sudo dnf module reset php
sudo dnf module enable php:remi-7.4
sudo dnf install php php-opcache php-gd php-curl php-mysqlnd
sudo systemctl start php-fpm
sudo systemctl enable php-fpm
setsebool -P httpd_execmem 1
```
6. Amend Security Group settings:
    * Open port 80
7. Repeat steps 1-6 for a further two servers. 
8. Navigate back to Webserver 1. Confirm the mounts are accessible. Apache files and directories should be accessible at /var/www. Likewise, on the NFS server, the same files should be accessible at /mnt/apps. To further test, create a test.txt file in one of the servers and the file should appear in the corresponding location on the other servers. 
![test file](/Project07/images/test_file.png)
8. Mount the Apache log folder `/var/log/httpd` to the NFS Servers's export for logs. Make sure this mount point persists by amending the `/etc/fstab` file
```bash
sudo mount -t nfs -o rw,nosuid 172.31.40.8:/mnt/logs /var/log/httpd
```
```
<NFS-Server-Private-IP-Address>:/mnt/logs /var/log/httpd nfs defaults 0 0
```

9. Fork the tooling source code from [Darey.io Github Account](https://github.com/darey-io/tooling)
10. Deploy the tooling website’s code to the Webserver. Ensure that the html folder from the repository is deployed to `/var/www/html`
11. Update the functions.php file to enable connections to the database


12. Apply tooling-db.sql script to your database using the following command 
```
mysql -h <database-private-ip> -u <db-username> -p tooling < tooling-db.sql
```
![Apply tooling script](/Project07/images/tooling_script.png)
13. Navigate to DB Server and create a new admin user in the tooling database. This can also be achieved in any of the web-servers provided mysql-client has been installed. 
```sql
INSERT INTO users (id, username, password, email, user_type, status) VALUES (1, ‘myuser’, ‘password’, ‘user@mail.com’, ‘admin’, ‘1’);
```
![Add myuser](/Project07/images/add_myuser.png)
14. Open the website in your browser http://<Web-Server-Public-IP-Address-or-Public-DNS-Name>/index.php and make sure you can login into the website with `myuser` user.
![Webpage](/Project07/images/final_webpage.png)
![Myuser login](/Project07/images/my_user_login.png)


## Troubleshooting
The following section highlights blockers faced and how they were overcome.
### NFS/Webserver not connecting to DB
* mysql-client was installed on the NFS Server and the followgin command was used to connect to the database:
```
sudo mysql -u test -p -h <database_ip>
```
* This produced the following error:
![sql error](/Project07/images/sql_error.png)
* After some investigation, the issue became clear - the DB Server was in a different subnet. A new instance was spun up and the problem was resolved. (NB: I did end up spinning a 3rd DB instance because I used the RHEL OS. This wasn't so much a blocker, but contradicted the architecture set out at the head of this documentation.)

### Webpage not loading 
* After deploying the tooling code, I navigated to the webpage but it would not load. 
* I followed guidance set out in the project guide:
    * Check permissions to /var/www/html folder
    * Disable SELinux `sudo setenforce 0`. To make this permanent, amend the config file `/etc/sysconfig/selinux` by setting `SELINUX=disabled` and restarting httpd `sudo systemctl restart httpd`
* This did not resolve the issue entirely. It seems the root cause was that Apache was loaded but not active.
![Apache status](/Project07/images/apache_status.png)
* As shown above, restarting Apache was not without a hitch but after running `sudo setenforce 0` and confirming SELinux was disabled, Apache succesfully started. 
![Apache active](/Project07/images/Apache_active.png)

### Webpage - Access Denied Message
* The webpage displayed a blank white screen with 'Access Denied' written in the top corner. 
* After having a look at the logs it seemed that the problem was related to file permissions. All the files were given rwx permissions and the page rendered successfully.

### Mysql Add User 
* The SQL code in the project guide did not seem to be compatible with my version of MySQL as it led to syntax errors. 
![SQL add users error](/Project07/images/sql_add_users.png)
* As shown in the screenshot above, the resolution was to simply remove quotation marks from the field list. 

### myuser Unable to Log In
* When attempting to log in with the credentials added to the database, 'myuser' was denied access on the grounds of invalid credentials.
* After some investigation, I discovered the answer in the functions.php script - the log in function uses a MD5 hash. The value inputted into form is hashed before running a search query on the database. When I created 'myuser' I set the password as 'pass' and not the encrypted format. Thus when a search query is run, it yields no results, leading to an invalid credential message.
* A temporary solution to this was to comment out the line of code which hashes the password. This is only temporary as it prevents the admin user from logging in. 
![Login function](/Project07/images/login_function.png)
* A more robust solution is to simply create a new user, ensuring to use the hashed password (as per the original instructions :sweat_smile: ). 





