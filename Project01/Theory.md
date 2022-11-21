# LAMP Stack Implementation 

[Project Implementation](https://github.com/A-Ahmed100216/LAMP_Stack_Implementation/blob/main/Project1/Implementation.md)

## Technology Stacks
* Set of frameworks and tools used to develop software 
* Common examples include:
  * LAMP (Linux, Apache, MySQL, PHP or Python, or Perl)
  * LEMP (Linux, Nginx, MySQL, PHP or Python, or Perl)
  * MERN (MongoDB, ExpressJS, ReactJS, NodeJS)
  * MEAN (MongoDB, ExpressJS, AngularJS, NodeJS)

## Software Development Life Cycle (SDLC) 
* Typically 6 to 8 steps which are used to develop software.
* The purpose of SDLC is to measure and analyse the process in order to reduce inefficiency therein reducing overall costs. 
* The steps include: 
    1. Planning - Define, the scope and purpose of the project. Evaluate project objectives, create timelines and delegate roles. 
    2. Requirements - Identify key criteria and how they will be delivered. 
    3. Design - Create architecture, mock up UIs, determine platforms and languages. 
    4. Build - Develop the code
    5. Document - Document process, user guides, known problems etc. 
    6. Test - Conduct smoke testing to ensure end user does not face issues. 
    7. Deploy - Make application available to users. 
    8. Maintain - Continue to monitor application, fix any bugs which were not addressed, release updates. 
* Various different models of SDLC, include Waterfall, Agile, and DevOps. 

## LAMP Stacks 
* Simple, stable and powerful web stack for delivering web applications. 
* Consists of the following elements:
  * Linux - Operating System
  * Apache - Web Server
  * MySQL - Database
  * PHP/Python/Perl - Programming Language 
* LAMP has a layered architecture - Linux at lowest level, followed by Apache, MySQL, and PHP/Python. 
### How it works?
1. Apache web servers receives a request from a user
2. If request is PHP file, Apache passes request to PHP. File is loaded and code executed.
3.  PHP communicates with database (MySQL) to collect data. 
4. PHP uses code and data to create HTML.
5. PHP passes back to Apache Web Server to send to browser and MySQL to store new data. 
All these operations enabled by Linux OS at the base of the stack. However, other OS can be used such as Microsoft Windows (WAMP) or MacOS (MAMP).

### Why LAMP?
* Flexible 
    * Can use alternative OS
    * LAMP is open source therefore avoiding lock in
    * Can select components based upon business requirements
* Secure Architecture - Well established encryption practices. 
* Reduced development time - open source stack which has been around for some time therefore plenty of material and resources available to build upon. 

## Linux Permission 
File permissions are used to control access. There are 3 types of policies:
1. Owner - Person or process who created the file 
2. Group - Primary group
3. Others - World permissions 
### Commands
* Change mode - `chmod` - Defines what the user/group that owns a file can do to that file i.e. whether they can/cannot read/write/execute the file. 
* Change owner - `chown` - Defines who owns a file.


## TCP vs UDP
### TCP
* Transmission Control Protocol
* Connection oriented - once a connection has been established, data transmitted in two directions. 
* Built in checks for errors and to check data was delivered in the order it was sent.
* Ideal for transferring images, data files, web pages etc. 
* Larger overheads - greater use of available bandwidth 
### UDP
* User Datagram Protocol 
* Connectionless internet protocol
* Error checking and recovery services not required. 
* Data continuously sent to recipient, whether they receive it or not. 
* No overhead for opening, maintaining, or terminating a connection. 
* Not ideal for sending emails, viewing webpages etc but preferred for real time communication (broadcast)
### Comparison

|         Feature        |                                       TCP                                      |                                             UDP                                             |
|:----------------------:|:------------------------------------------------------------------------------:|:-------------------------------------------------------------------------------------------:|
| Connection             | Requires established connection (must be closed once transmission is complete) | Connectionless - no protocol for opening, maintaining, or terminating connection            |
| Data Sequencing        | Able to sequence                                                               | Unable to sequence                                                                          |
| Delivery               | Can guarantee delivery to destination                                          | Cannot guarantee delivery to destination                                                    |
| Retransmission of Data | Possible to retransmit lost packets                                            | No retransmission of lost packets                                                           |
| Error Checking         | Extensive error checking and acknowledgement of data                           | Basic error checking using checksums                                                        |
| Method Transfer        | Data read as byte stream; messages transmitted to segment boundaries           | UDP packets with defined boundaries; sent individually and checked for integrity on arrival |
| Speed                  | Slower than UDP                                                                | Faster than TCP                                                                             |
| Broadcasting           | Doesn't support                                                                | Does support                                                                                |
| Usage                  | HTTPS, HTTP, SMTP, POP, FTP                                                    | Broadcasting, video conferencing, streaming, DNS, VoIP                                      |




## References:  
[Cook, 2017. TCP vs. UDP: What’s the Difference?](https://www.lifesize.com/en/blog/tcp-vs-udp/)  
[IBM, 2019. LAMP Stack](https://www.ibm.com/uk-en/cloud/learn/lamp-stack-explained)   
[Zomaya, 2020. When to Use chmod vs chown](https://www.cbtnuggets.com/blog/technology/system-admin/when-to-use-chmod-vs-chown)   

