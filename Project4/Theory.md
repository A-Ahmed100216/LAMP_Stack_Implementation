# Project 4 - MEAN Stack Implementation 

**[Project Implementation](https://github.com/A-Ahmed100216/MEAN_Stack_Implementation/blob/main/Project.md)**

A MEAN stack deployment consists of the following:
* **M**ongoDB - Database
* **E**xpress - Back-end  framework
* **A**ngular - Front-end framework
* **N**ode.js - JavaScript runtime environment


## OSI Model 
* Open Systems Interconnection Model 
* Provides a standard for computer systems to interact with each other. 
* Created by International Organisation for Standardisation 
* Based upon splitting communication systems into seven abstract layers:
* Each layer handles its specific job and communicates with the layers above and below itself. 
### Layers
* 7 - Application Layer - 
    * Human-computer interaction layer where apps can access network services 
    * Software like web browsers rely on this layer to initiate communications 
    * Protocols include HTTP and SMTP (Simple Mail Transfer Protocol)
* 6 - Presentation Layer 
    * Prepares data so it is in a usable format
    * RResponsible for translation, encryption, and compression of data. 
    * Ensures data is in usable format
    * Where data encryption occurs
* 5 - Session Layer
    * Maintains connection - opens and closes communication between two devices. 
    * Controls ports and sessions 
    * Synchronises data transfer with checkpoints - if a transfer unexpectedly crashes, transfer can pick up from last checkpoint. 
* 4 - Transport Layer 
    * Transmits data via protocols such as TCP and UDP
    * Takes data from Session Layer (layer 5) and breaks into segments before sending to Network Layer (layer 3).
    * Also responsible for flow control (optimal speed of transmission so as to not overwhelm a receiver with slow connection) and error control (checks whether data received is complete, otherwise requesting a retransmission).
* 3 - Network Layer
    * Responsible for facilitating data transfer between two different networks. 
    * Breaks up segments from the TRansport Layer (layer 4) into smaller unit,packets on the sender device. These are then reassembled on the receiving device. 
    * Also determines best physical path dara will take - routing. 
* 2 - Datalink Layer
    * Defines format of data on the network
    * Facilitates data transfer between two devices on same network 
    * Takes packets from the network layer and break into smaller pieces, frames. 
* 1 - Physical Layer 
    * Transmits raw bit stream over the physical medium 
    * Includes the physical equipment i.e. cables and switches. 

![OSI Model](/images/OSI_model.png)

# Load Balancing 
* Process of distributing network traffic across multiple servers. 
* Ensures no single server is overworked. 
* Improves application responsiveness and availability. 



# References:
[AVI Networks](https://avinetworks.com/what-is-load-balancing/)     
[Cloudflare](https://www.cloudflare.com/en-gb/learning/ddos/glossary/open-systems-interconnection-model-osi/)
