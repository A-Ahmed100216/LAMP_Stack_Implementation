# Project 8 - Load Balancer Solution with Apache 

## Introduction 
In project 7, 3 Webservers were configured. Why? Almost all websites are served by multiple web servers in order to address traffic. This is particularly true for websites such as Google for which it would be impossible for handle all traffic via a single webserver. The user accesses the webpage via a URL but they have no control over which webserver they are directed to. A URL consists of a domain name which is translated by a Domain Name Service (DNS) such as AWS Route 53. 

In order to deal with demand, owners could increase CPU and RAM of the server. This is known as **Vertical Scaling** but this approach is not without limitations. Eventually the user will reach the maximum capacity of CPU or RAM which can be installed. Granted, they could replace it with a more powerful server but eventually would face the same issue. 

An alternative approach is **Horizontal Scaling** - distributing load across multiple web servers
This is the favoured approach for most corporations as new servers can be added easily and are *theoretically* infinite (theoretically because more servers = more money :joy: )
Horizontal Scaling involves scaling out (adding) or scaling in (removing) webservers. 
This process can be done manually or automated based upon metrics such as CPU or Load.
Scalability is a measure of how a system can adapt to load by scaling in or out. 

The Webservers in Project 7 each have their own IP address from which they can be accessed. This is not convenient and so a single point of access is configured using a Load Balancer. 


## Load Balancers
* A Load Balancer (LB) distributes network traffic across servers.
* This is because modern websites may need to serve thousands or millions of concurrent requests and then return text, images, videos etc.
* To meet high volume demands, additional servers are generally required
* A load balancer has the following functions:
    * Distribute network traffic
    * Ensure high availability by only routing to available servers.
    * Flexibly add or remove servers to meet demand.

### Types of Load Balancers
![Types of LB](/Project08/images/LB_types.png)