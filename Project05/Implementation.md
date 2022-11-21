# Project 5 Implementation
This project will demonstrate a server-client relationship.
* Create two Linux EC2 instances. One will act as the server, and the other, the client. Name as such:
```
mysql server
mysql client
```
* Connect to `mysql server` and install mysql server:
```
sudo apt update
sudo apt install mysql-server
```
![install mysql-server](/Project05/images/install_mysql_server.png)
* Connect to `mysql client` and install mysql client
```
sudo apt update
sudo apt-get install mysql-client
```
![install mysql-client](/Project05/images/install_mysql_client.png)
* Confirm mysql is running on `mysql server`. It should 
```bash
sudo systemctl status mysql
```
![status mysql](/Project05/images/status.png)
* Connect to mysql and create a new user which will be used to connect via the client
```
sudo mysql
mysql> CREATE USER 'client'@'ip' IDENTIFIED BY 'password';
mysql> GRANT CREATE, ALTER, DROP, INSERT, UPDATE, DELETE, SELECT, REFERENCES, RELOAD on *.* TO 'client'@'ip' WITH GRANT OPTION;
mysql> FLUSH PRIVILEGES;
mysql> exit
```
* Next, amend the Security Group of the mysql server to allow inbound connections from the client instance only. MySQL uses TCP port 3306.
![SG](/Project05/images/sg.png)
* Return to `mysql server` and configure the server to allow connections from remote hosts. Navigate to the file:
```
sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf
```
* Change the bind address from `127.0.0.1` to `0.0.0.0`
![conf](/Project05/images/conf.png)
* The server should now be accesible from the remote client. This can be checked by connecting via the new user:
```
mysql -u client -h server_ip -p 
```
* A connection should be established. This can be tested by running the following command:
```
mysql> SHOW Databases;
```
![client connected](/Project05/images/connect_client.png)