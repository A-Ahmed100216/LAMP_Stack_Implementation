# Project 5 - Client/Server architecture using MySQL

[Project Implementation]()

## Client/Server Architecture
* Architecture model where two or more computers are connected over a network and exchnage requests 
* Client - machine sending requests 
* Service - machine receiving requests 
* Example - Website trying to access the internet via browser or `curl` command
    1. Client makes HTTP (GET) request
    2. Server sends HTTP (GET) response 
* Example 2 - INtroduce a database
![Example 2](/Project5/images/example2.png)
    1. Webserver connects to database to read and write 

## Network Diagnostic Utilities
* Ping - Test reachability of a device on a network 
* Traceroute - Trace the route a packet takes when travelling. Useful to idenitfy network problems and discover where connections fail. 
### Ping 
* `ping <hostname>`
* Uses ICMP (Internet control Message Protocol) Echo messages.
* Checks if a remote host is active, how long a round trip takes, and any packet loss.
* Sends a request and waits for a reply.
* `ping <hostname>`

### Traceroute
* Send three packets of data to test 'hop'
* hop - when a packet is passed between routers
* The process is as follows:

    1. First three packers sent to unreachable port on target, each with a Time-to-Live (TTL) value of 1.
    


## Refernces
[Kaczanowski (2021), Free Code Camp](https://www.freecodecamp.org/news/traceroute-and-ping/)