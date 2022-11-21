# Project 8 - Load Balancer Solution with Apache 
The following project will:
* Deploy and configure an Apache Load Balancer
* Enable both Webservers to serve users via a Load Balancer. 
![Architecture](/Project08/images/architecture.png)

## 1. Configure Apache as a load balancer
1. Spin up an Ubuntu Server 20.04 EC2 instance, name it `Project-8-apache-lb`.
2. Open TCP port 80 on the SG for this instance. 
3. Connect to `Project-8-apache-lb` and install Apache 
```
#Install apache2
sudo apt update
sudo apt install apache2 -y
sudo apt-get install libxml2-dev

#Enable following modules:
sudo a2enmod rewrite
sudo a2enmod proxy
sudo a2enmod proxy_balancer
sudo a2enmod proxy_http
sudo a2enmod headers
sudo a2enmod lbmethod_bytraffic

#Restart apache2 service
sudo systemctl restart apache2
```
4. Confirm Apache is running 
```
sudo systemctl status apache2
```
![Apache status](/Project08/images/Apache_status.png)
5. Configure Load Balancing in the following file `/etc/apache2/sites-available/000-default.conf`
```bash
#Add this configuration into this section <VirtualHost *:80>  </VirtualHost>

<Proxy "balancer://mycluster">
               BalancerMember http://<WebServer1-Private-IP-Address>:80 loadfactor=5 timeout=1
               BalancerMember http://<WebServer2-Private-IP-Address>:80 loadfactor=5 timeout=1
               ProxySet lbmethod=bytraffic
               # ProxySet lbmethod=byrequests
        </Proxy>

        ProxyPreserveHost On
        ProxyPass / balancer://mycluster/
        ProxyPassReverse / balancer://mycluster/
```
The load balancing method utilised in this project is `bytraffic` which disitributes requests according to traffic. Other methods include:
        * `bybusyness` - Tracks requests each server is handling and directs new requests to server with lowest number of active requests. 
        * `byrequests` - Requests distributed so each server gets their configured share of number of requests. 
        * `heartbeat` - Favours servers with more idle capacity over time. 
The proportion in which traffic ust be distributed is determined by the `loadfactor` parameter. This is identical for both servers so requests are handled in turn as traffic is evenly distributed between them.    
![LB Config](/Project08/images/LB_config.png)
6. Unmount `/var/log/httpd/` directory from the NFS Servers. 
```
sudo umount /var/log/httpd
```
7. Open logs for both Webservers 
```
sudo tail -f /var/log/httpd/access_log
```
8. Navigate to a browser and try to access the homepage from the Load Balancers IP address `http://<Load-Balancer-Public-IP-Address-or-Public-DNS-Name>/index.php`
9. Refresh the page a few times and observe the logs. Each request should be handled by the webservers in turn. This will manifest as HTTP records in the logs. 
![Logs](/Project08/images/logs1.png)
![Logs](/Project08/images/logs2.png)

### 2. Configure Local DNS Names Resolution
IPs are not the most convenient to use, especially as the number of servers grow. Local DNS resolution can be configured within the `/etc/hosts` file. This is not a scalable method but ideal for demonstrating the concept. 
1. Open the file `/etc/hosts`

2. Add the following into the file:
```
<WebServer1-Private-IP-Address> Web1
<WebServer2-Private-IP-Address> Web2
```
![DNS Config](/Project08/images/dns_config.png)
3. Update the Load Balancing config file `/etc/apache2/sites-available/000-default.conf` with the names aliased. 
```bash
BalancerMember http://Web1:80 loadfactor=5 timeout=1
BalancerMember http://Web2:80 loadfactor=5 timeout=1
```
![LB Config with DNS](/Project08/images/LB_config_with_DNS.png)
4. The webpage should now be accessible at the following address  `http://Web1`. Confirm this via the `curl` command.
```
curl http://Web1
curl http://Web2
```

## Troubleshooting
### Unmount Error
When trying to unmount the logs, the following error was displayed. 
![Umount error](/Project08/images/umount_error.png)
This was resolved by stopping the Apache service and then unmounting.
![Umount success](/Project08/images/umount_success.png)
Once unmounted, the Apache service was started again. 