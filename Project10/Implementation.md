# Project 10 - Load Balancer Solution with Nginx and SSL/TLS

Data passing between a client and webserver typically travels through multiple networks. If unencrypted, this can be easily targeted to steal sensitive information in what is known as a Man-In-The-Middle attack. 
SSL/TLS can be used to protect against MITM attacks by creating an encrypted session between server and client. SSL uses digital certificates which the websites checks against a Certificate Authority to confirm validity. 
This project will build upon previous by registering the website with LetsEnrcypt Certificate Authority. To automate certificate issuance a shell client recommended by LetsEncrypt – cetrbot, will be used. 

## 1. Configure Nginx as a Load Balancer 
1. Connect to the Apache Load Balancer Instance 
2. Uninstall Apache as Nginx will be used instead
```bash
# Stop service 
sudo service apache2 stop
sudo service apache2 status
# Remove Apache
sudo apt-get purge apache2 apache2-utils apache2.2-bin apache2-common
sudo apt-get purge apache2 apache2-utils apache2-bin apache2.2-common
# Clean up
sudo apt-get autoremove
# Confirm Apache has been removed - following command should return a blank line. 
which apache2
```
3. Install Nginx
```
sudo apt update
sudo apt install nginx
```
4. Configure the Nginx Load Balancer. Navigate to the nginx configuration file `sudo vi /etc/nginx/nginx.conf` and paste the following:

```bash
#insert following configuration into http section

 upstream myproject {
    server Web1 weight=5;
    server Web2 weight=5;
  }

server {
    listen 80;
    server_name www.domain.com;
    location / {
      proxy_pass http://myproject;
    }
  }

#comment out this line
#       include /etc/nginx/sites-enabled/*;

```
5. Restart nginx and confirm it is running
```
sudo systemctl restart nginx
sudo systemctl status nginx
```
## 2. Register a new domain name and configure secured connection
1. Register a new domain name using any Domain Name Registrar (GoDaddy, Domain.com, BlueHost.com etc.)
2. Assign an Elastic IP to your Load Balancer. This will prevent the need to constantly change the DNS record of the domain every time the LB instance is re-started. 
    a. Create an Elastic IP Address: WS Management Console > EC2 > Select `Network & Security` from LH menu > Allocate Elastic IP Address
    b. Associate IP - Select the newly created IP address and select `Associate Elastic IP address` from the actions menu. Associate with the LB Instance. 
3. Update your Domains A Records. For GoDaddy this can be achieved by selecting Domain Settings and 'Edit'. Insert the Elastic IP into the 'Value' box.
4. Confirm your webserver is accessible via the domain name - `http://<your_domain_name.com>`
5. Update the nginx.conf to recognise the new domain name. 
```bash
sudo vi /etc/nginx/nginx.conf

# Replace this line ...
server_name www.domain.com
# With this line ...
server_name www.<your-domain-name.com>
```
6. Confirm the `snapd` service is active and running.
```
sudo systemctl status snapd
```
7. Install certbot 
```
sudo snap install --classic certbot
```
8. Request a certificate for the domain and follow the onscreen instructions 
```
sudo ln -s /snap/bin/certbot /usr/bin/certbot
sudo certbot --nginx
```
9. Confirm you can access the website via the HTTPS Protocol (make sure port 443 is open on your LB Instance Security Group).
10.  By default, LetsEncrypt certificate is valid for 90 days, so it is recommended to renew it at least every 60 days or more frequently. Therefore, it needs renewing periodically. The renewal command can be tested by running:
```
sudo certbot renew --dry-run
```
11. Renewal can be automated to a run on a specified schedule ia the crontab utility.
```bash
crontab -e

# Paste the following 
0 0 1 * *   root /usr/bin/certbot renew > /dev/null 2>&1
```

