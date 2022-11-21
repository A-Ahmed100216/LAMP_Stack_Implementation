# Project 7 - DevOps Tooling Website Solution

## Introduction
The following project will create a single DevOps Solution consiting of the following tools:
1. Jenkins – open source automation server used to build CI/CD pipelines.
2. Kubernetes – an open-source container-orchestration system for automating computer application deployment, scaling, and management.
3. Jfrog Artifactory – Universal Repository Manager supporting all major packaging formats, build tools and CI servers. Artifactory.
4. Rancher – an open source software platform that enables organizations to run and manage Docker and Kubernetes in production.
5. Grafana – a multi-platform open source analytics and interactive visualization web application.
6. Prometheus – An open-source monitoring system with a dimensional data model, flexible query language, efficient time series database and modern alerting approach.
7. Kibana – Kibana is a free and open user interface that lets you visualize your Elasticsearch data and navigate the Elastic Stack.

### Storage 
* Network-attached storage (NAS)
  * Storage device connected to a network. 
  * Allows storage and retrieval of data from a central location for authorised users. 
  * Flexible and scalable - can add more storage and demand dictates
  * Simple to operate 
  * Low cost 
  * Easy to backup data 
  * Central data storage
  * Data continuously accessible 
* Storage Area Network (SAN)
  * Dedicated high-speed network that connects storage devices and presents as shared pools to multiple servers.
  * Focusses on making storage highly available and accessible.
  * Address demand for advanced storage
  * Provide dedicated, highly scalable, high performance networks.
  * Storage can be treated as single collective resource 
  * SAN is a network of disks that are accessed by a network of servers. 
  * The network of interconnected computers means that if one path is disrupted, an alternative can typically be employed allowing for high availability. 
  * 2 principle networking technologies employed for SANs:
    1. Fibre Channel (FC) - high speed network with high throughput and low latency. Offers speeds up to 128 Gbps up to 6 miles (10km). Enables block level storage to be in one location. 
    2. iSCSI - tyope of network designed to connect computing with shared storage. Can run at speeds up to 100 Gbps. Merges traditional SCSI block data with Ethernet and TCP/IP networking. Allows use of same tools (cables, adaptors etc.) as Ethernet. 
#### Block Storage
* 'Chop' data into block
* Each block has a unique identifier allowing it to be stored wherever is most convenient. 
* Often configured to decouple user environment and spread it out across multiple environment.
* Usually deployed in SAN environments and tied to a functioning server. 
* Quickly retrieved. 
* Easily partitioned as each block lives on its own. 
* Downsides include:
    * Can be expensive
    * Limited capacity to handle metadata 
#### Object Storage 
* Flat structure, files broken into pieces and spread amongst hardware.
* Data broken into discrete structures, objects, which are often kept in a single repo.
* Object storage volumes are modular - each is a self-contained repo.
* Unique identifier allows objects to be found, along with their metadata.
* Object storage metadata can be very detailed, with the ability to include data such as when a video was shot, type of camera etc. 
* Data retrieval relies on metadata and identifiers. 
* Requires a simple HTTP API 
* Cost efficient - only pay for what you use. 
* Easily scalable therefore often used by public cloud providers such as AWS.
* Well suited for static data 
* Drawbacks:
    * Objects cannot be modified- must be written entirely.
    * Does not work well with traditional databases - writing objects is slow and writing an app is not as easy as simply using file storage. 

### File Transfer Protocols
* Network File System (NFS)
  * File system distribution protocol.
  * Enable user to connect to network and access files. 
  * Open standard therefore anyone can execute protocol, provided they use a client computer. 
  * All people within the network have access to the files - ideal for file sharing. 
  * First introduced in 1984 by Sun Microsystems. 
* Server Message Block (SMB)
  * File sharing protocol over a local area network 
  * Also known asCommon Internet File System (CIFS)
  * Uses client-server architecture.
  * Files are accessed by entering IP address of host computer. 
  * Network mounted directories are not treated as local directories.
  * First launched by IBM but developed by Microsoft. 
#### Differences
* A key difference between these two protocols is authentication 
  * NFS - host based verification system i.e. all users can access shared files
  * SMB - Strictly user based. 
* NFS is considered superior with small/medium files. 
* For large files, performance is relatively similar with both protocols. 
* NFS better suited for Linux, SMB more appropriate for Windows. 
* NFS is case sensitive, SMB is not
* Overall NFS is faster than SMB when it comes to operations like read, write, and browsing. 


## References
[Seagate](https://www.seagate.com/gb/en/tech-insights/what-is-nas-master-ti/)
[Bigelow SJ (2020)](https://www.techtarget.com/searchstorage/definition/storage-area-network-SAN)
[Jethva H](https://cloudinfrastructureservices.co.uk/nfs-vs-smb/)
[RedHat (2018)](https://www.redhat.com/en/topics/data-storage/file-block-object-storage)