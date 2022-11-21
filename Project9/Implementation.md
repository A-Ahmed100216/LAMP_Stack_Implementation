# Project 9 - Tooling Website Deployment Automation

## 1. Install Jenkins Server
1. Launch an Ubuntu 20.04 EC2 Instance
2. Jenkins uses TCP Port 8080 therefore amend the instance SG to allow inbound connections from this source. 
3. Connect to the instance and install JDK (Java Development Kit) which is a prerequisite to installing Jenkins, a Java based application. 
```bash
sudo apt update
sudo apt install default-jdk-headless
```
4. Install Jenkins
```bash 
wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > \
    /etc/apt/sources.list.d/jenkins.list'
sudo apt update
sudo apt-get install jenkins
```
5. Confirm Jenkins is running
```bash
sudo systemctl status jenkins
```
6. Access Jenkins. Navigate to the following address `http://<Jenkins-Server-Public-IP-Address-or-Public-DNS-Name>:8080` and complete the initial setup wizard. 
    a. Unlock Jenkins - Paste the initial admin password - `sudo cat /var/lib/jenkins/secrets/initialAdminPassword`
    b. Customise Jenkins - Select `Install Suggested Plugins`
    c. Admin User - Create an admin user 

## 2. Configure Jenkins to retrieve source codes from GitHub using Webhooks
1. Create a webhook in your GitHub repo : GitHub repo > Settings > `Webhooks` tab > Select `Add Webhook`
    * Payload Url = `http://Jenkins-Server-Pubic-IP:8080/github-webhook`
    * Content type = `application/json`
![Webook Setting]()
2. Navigate to Jenkins and select `New Item`. Name your project and select `Freestyle project`
3. Scroll to or select the `Source Code Management' section. Select `Git` and populate with your repo info and credentials. 
4. Save the configuration and trigger a manual build. 
![Build 1](/Project9/images/Build1.png)
5. The next stage is to configure the build trigger so changes to the GitHub repo automatically trigger a new build. Navigate to Jenkins project configuration.
6. Scroll to or select the `Build Triggers` section. Select the option for `GitHub hook trigger for GITScm polling`.
7. Next configure `Post-Build Actions`. From the drop down, select `Archive the Artifacts` and set files to archive as `**`. These archived files are known as artifacts. 
8. Make a change to the README and commit this change. This should trigger a build in Jenkins and the artifacts should be stored on the Jenkins Server. 
![Build 2]()
9. Confirm artifacts have been stored locally
```
ls /var/lib/jenkins/jobs/tooling_github/builds/<build_number>/archive/
``` 
![Artifacts]()
## 3. Configure Jenkins to copy files to NFS server via SSH
The artoifacts are now locally stored, the next stage is to copy them into the /mnt/apps directory on the NFS Server. 
1. Install `Publish Over SSH` plugin : Jenkins dashboard > Manage Jenkins > Manage Plugins > `Available` tab > Search for `Publish Over SSH ` > `Install without restart`
2. Configure SSH Server : Jenkins Dashboard > Manage Jenkins > Configure System > Scroll down to `POublish Over SSH`
3. Select Add SSH Server and populate the fields. 
    * Key = Paste the key used to connect to the NFS Server (.pem key)
    * SSH Server
        * Name - `ec2-user@<NFS_Private_IP>`
        * Hostname - `<NFS_Private_IP>`
        * Username – `ec2-user`
        * Remote directory – `/mnt/apps`
4. Test the configuration. If successful, you should see a `Sucess`, otherwise, check config.
5. Save the configuration and return to project Configuration. 
6. Scroll to `Post-Build Actions` and add the following `Send Build artifacts over SSH`
7. Select the SSH configured just prior and set `Source files` as `**` as we want to copy all files and directories to the NFS Server. 
8. Save the config and make another change to the README. Commit this change.
9. Confirm a build has been triggered in Jenkins and it runs successfully. The console output should display the following message.
![Console Output]()

10. Also, check the files have indeed been copied to the NFS Server. The files have already been copied as part of previous projects however the README should be altered slightly due to the changes made as part of this project. This will be used to confirm the build was actually successful. 
11. Connect to the NFS Server and run the command `tail /mnt/apps/README.md`.The output should contain the additions made to the README as part of this project.
![NFS Server]()

