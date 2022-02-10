## Description


Project on End to End CI/CD pipeline for java based application using Git,Github,Jenkins,Maven,Sonarqube,Nexus,Slack,Docker and Kuberenetes with ECR as private docker registry, HAproxy Load balancer and Zero Downtime Deployment.

![arch](https://github.com/pingtotgp/CICD/blob/main/images/arch.JPG)

----

## Project Flow

```sh
1. Developer pushes code into Github.
2. Webhook triggers Jenkins when there is a change in the code
3. Jenkins Pulls the Code from the Github
4. Maven builds the code and generates artifacts
5. Code quality is measured with Sonarqube
6. Quality Gate Check , If Quality Gate Fails Jenkins Job will fail !!!!!! (Triggered by Sonarqube Webhooks)
7. Upload artifact generated into Sonatype Nexus . It will dynamically choose Snapshot or release repository based on the version tag in pom.xml
8. Build Docker Image based on the Dockerfile with projectname && commit-id as tag . So each time it will be different.
9. Push Docker Image to private ECR docker registry.
10. Dynamically change image in pod template in manifest file.
11. Deploy to K8s cluster created with kubeadm . It will pull image from private registry.
12. Send Build Notification over Slack Channel and email notification when build is success/failure.

Note: We can add an approval step before deploying to K8s cluster as an input from user.

```
----

## Features
```sh
1. Zero downtime deployment with rolling update as deployment strategy
2. Complete automation as when developer check in code , deployed to k8s cluster
3. Versioning of docker images , build artifacts.
4. Code checked against Code coverage and whether coding stantards are met.

```
----



## Installing Let's Encrypt



Install certbot
-----------

```sh

[ec2-user@haproxy-lb ~]$ sudo yum install certbot

```


Now give the below configuration in the /etc/haproxy/haproy.cfg file.

 
```sh
-------------------------------------------

frontend http_front

 

  bind *:80

  acl letsencrypt-acl path_beg /.well-known/acme-challenge/

  use_backend letsencrypt-backend if letsencrypt-acl

 

backend letsencrypt-backend

 

  server letsencrypt 127.0.0.1:8888

```

The complete HAproxy.cfg file is provided in the repo.
 

Make sure that the port 8888 , 443 is allowed in your securitygroup.

 
```sh
[ec2-user@haproxy-lb ~]$ systemctl restart haproxy
```
