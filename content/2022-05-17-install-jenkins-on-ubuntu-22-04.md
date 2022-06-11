---
title: Install Jenkins on Ubuntu 22.04
image: /images/advanced.jpg
imageMeta:
  attribution:
  attributionLink:
featured: true
authors:
  - Ruan Bekker
date: Tue May 17 2022 17:50:55 GMT+0200 (CAT)
tags:
  - jenkins
  - devops
---

In this tutorial I will demonstrate how to install [**Jenkins**](https://www.jenkins.io/) on Linux (Ubuntu 22.04 LTS) and how to create a freestyle job.

## What is Jenkins

[Jenkins](https://www.jenkins.io/) is an open source automation server which enables developers around the world to reliably build, test, and deploy their software. (*taken from their website*)

## Dependencies

Jenkins requires Java to run and therefore we need to install the Java Runtime Environment and on this time of writing, jenkins supports [OpenJDK JDK / JRE 11 - 64 bits](https://www.jenkins.io/doc/administration/requirements/java/).

To install openjdk-11:

    sudo apt update
    sudo apt install openjdk-11-jre -y
    

Then verify if the installation succeeded by running:

    java -version
    

And in my case the output shows:

    openjdk version "11.0.15" 2022-04-19
    OpenJDK Runtime Environment (build 11.0.15+10-Ubuntu-0ubuntu0.22.04.1)
    OpenJDK 64-Bit Server VM (build 11.0.15+10-Ubuntu-0ubuntu0.22.04.1, mixed mode, sharing)
    

Now that our JDK has been installed, we can install Jenkins and I am using the [Jenkins documentation to install Jenkins on Linux](https://www.jenkins.io/doc/book/installing/linux/).

## Jenkins Installation

First get the Jenkins sources:

    $ curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo tee /usr/share/keyrings/jenkins-keyring.asc > /dev/null
    
    $ echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian-stable binary/ | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null
    

Now update the indexes to make jenkins available to install:

    $ sudo apt update
    

Then install jenkins:

    $ sudo apt install jenkins -y
    

We can verify if Jenkins is running by using systemd:

    $ sudo systemctl status jenkins
    
      jenkins.service - Jenkins Continuous Integration Server
         Loaded: loaded (/lib/systemd/system/jenkins.service; enabled; vendor preset: enabled)
         Active: active (running) since Mon 2022-05-16 19:38:43 UTC; 1min 49s ago
       Main PID: 1536 (java)
          Tasks: 35 (limit: 1034)
         Memory: 329.9M
            CPU: 37.040s
         CGroup: /system.slice/jenkins.service
                 └─1536 /usr/bin/java -Djava.awt.headless=true -jar /usr/share/java/jenkins.war --webroot=/var/cache/jenkins/war --httpPort=8080
    
    May 16 19:38:14 jenkins jenkins[1536]: This may also be found at: /var/lib/jenkins/secrets/initialAdminPassword
    May 16 19:38:43 jenkins systemd[1]: Started Jenkins Continuous Integration Server.
    

We can also check if jenkins will start on boot:

    $ sudo systemctl is-enabled jenkins
    enabled
    

We can get the initial admin password using:

    $ sudo cat /var/lib/jenkins/secrets/initialAdminPassword
    507daf8075f346768da4435d9f8a8e53
    

Before we access jenkins, we need to allow port 8080 on our firewall (I'm a die-hard iptables fan, so I won't be using ufw):

    $ sudo iptables -I INPUT -p tcp --dport 8080 -j ACCEPT
    

## Access Jenkins

Now you can access jenkins on "[http://your-public-ip:8080](http://your-public-ip:8080)"
![image](https://user-images.githubusercontent.com/567298/168670787-a69cb9a6-5d5d-4215-a0cc-783130abe563.png)
For now I will just be installing the suggested plugins:
![image](https://user-images.githubusercontent.com/567298/168670878-23ce9434-6ebe-467d-9ad9-d42245bf4543.png)
Then setup the first admin user:
![image](https://user-images.githubusercontent.com/567298/168671285-1760fb96-8036-463b-a203-1d60fe31ad76.png)
Configure the Jenkins URL (this is just for demonstration purposes, so I will keep it as its defaults, but I recommend you setup SSL):
![image](https://user-images.githubusercontent.com/567298/168671425-c437e20b-26e4-4dae-80eb-f0a57eb3b6a6.png)
Then you should see this screen:
![image](https://user-images.githubusercontent.com/567298/168671537-e44be58f-9c94-443a-8162-4846371434e6.png)
## Create a Freestyle Job

After selecting "Start using Jenkins" you should see this:
![image](https://user-images.githubusercontent.com/567298/168671626-9375af7f-fe1a-4b67-8afc-91d24cc8f70d.png)
Then create your first job, you can call it whatever you want and select "Freestyle Project":
![image](https://user-images.githubusercontent.com/567298/168671782-2e913903-0652-4d46-9903-6c185af0331d.png)
Under "Build" select "Add build step" and select "Execute shell":
![image](https://user-images.githubusercontent.com/567298/168671998-f7b5c262-129e-4a58-b5f3-36bc79ef08c3.png)
You can run any shell command, as can seen below:
![image](https://user-images.githubusercontent.com/567298/168672198-c3115f1f-21a3-4206-9451-ea2f4d87b332.png)
And we will execute the shell script:

    IP="$(curl -s ifconfig.co)"
    echo "The Public IP is ${IP}"
    

Select "Save" and you should see the following:
![image](https://user-images.githubusercontent.com/567298/168672336-d888bf5d-64af-44b5-a947-f6baa4a473ff.png)
Click on "Build Now" on the left at the bottom will show your build history, and we can see one build succeeded as it's green in color:
![image](https://user-images.githubusercontent.com/567298/168672635-7a012797-4138-4b4c-818a-aff7822c5116.png)
If we click on the green icon, it will take us directly to the "Console Output" where our build execution output will be shown:
![image](https://user-images.githubusercontent.com/567298/168672744-18e3abe4-2d1c-419e-aecf-574573f768ea.png)
## Next on Jenkins

In the [**next post**](/setup-ssl-for-jenkins-with-caddy-on-ubuntu-22-04/), I will change port 8080 to only listen on 127.0.0.1 and setup a caddy reverse proxy to provide SSL with LetsEncrypt.

## Thank You

Thanks for reading, if you like my content, check out my **[website](https://ruan.dev)**, read my **[newsletter](http://digests.ruanbekker.com/?via=ruanbekker-blog)** or follow me at **[@ruanbekker](https://twitter.com/ruanbekker)** on Twitter.
