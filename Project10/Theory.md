# Project 10 - load balancer solution with Nginx and SSL/TLS

Data passing between a client and webserver typically travels through multiple networks. If unencrypted, this can be easily targeted to steal sensitive information in what is known as a Man-In-The-Middle attack. 
SSL/TLS can be used to protect against MITM attacks by creating an encrypted session between server and client. SSL uses digital certificates which the websites checks against a Certificate Authority to confirm validity. 
This project will build upon previous by registering the website with LetsEnrcypt Certificate Authority. To automate certificate issuance a shell client recommended by LetsEncrypt – cetrbot, will be used. 

