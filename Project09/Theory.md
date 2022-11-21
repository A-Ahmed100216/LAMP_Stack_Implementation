# Project 9 - Tooling Website Deployment Automation

## Introduction
This project will build upon the architecture from Projects 7 and 8. A Jenkins server will be added and a job will be configured to automatically deploy source code changes to the NFS Server therein implementing the DevOps principle of Continuous Integration.
![Architecture](/Project09/images/architecture.png)

## CI/CD
CI/CD is an acronym for Continuos Integration, Continuos Delivery and Continuos Deployment - key principles in DevOps to deliver apps to customer by introducing automation into the development stages.   
*  Continuous Integration - focusses on automating the process of merging code to a central repository. This is particularly useful when teams are based internationally and time zones may differ. Once changes are committed, they are validated by building the app and testing. 
* Continuos Delivery - The validated code is then delivered to the repo
* Continuous Deployment - is an extension of Continuous Delivery. It automates the release of app to production. Theoretically, code is testing during the CI stage so it should be production-ready. Because of this, robust testing is key during the CI stage. 

