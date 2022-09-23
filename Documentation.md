WE shall be creating the infrastructure as shown below
![AWS Architecture](./images/AWS-Architecture.jpg)

#### Step 1 - Create VPC named ACME-vpc
![AWS Architecture](./images/vpc-create.JPG)
---



#### Step 2 - Create Subnets in ACME-vpc
- Subnets are private by default
- We shall create 2 public subnets and 6 private ones. 1 private and 3 private in each AZ (eu-west-2a and eu-west-2b)

- ACME-public-bastion-1  
- ACME-public-bastion-2
- ACME-private-proxy-1
- ACME-private-proxy-2
- ACME-private-web-1
- ACME-private-web-2
- ACME-private-data-1
- ACME-private-data-2
---
![subnets created](./images/subnets-created.JPG)
---
---
#### Step 3 - Create Internet Gateway for ACME-vpc named ACME-igw

- Create Internet Gateway ACME-igw
![IG created](./images/igw-created.JPG)

#### Step 4 - Attach Internet Gateway ACME-igw to ACME-vpc

- Attach Internet Gateway ACME-igw to ACME-vpc
![IG attach](./images/igw-attaching.JPG)

- Successfully attached
![IG attached](./images/igw-attached.JPG)

---
#### Step 5 - Create route table for public subnet to reach the internet through the internet gateway ACME-igw
- Create route table ACME-rtb-internet

![create rtb-public](./images/route-table-create-internet-public.JPG)
---
- With route table selected, go to edit routes 
![create rtb-public](./images/edit-routes.JPG)
- Create route to internet gateway 0.0.0.0/0 for all traffic
![create rtb-public](./images/route-added-igw.JPG)
---


#### Step 6 - Associate Public Subnets to the route table
- Go to edit subnet associations and select the public subnets then click on save associations
![create rtb-public](./images/edit-subnet-associations.JPG)

---
#### Step 7a - Create Elastic IP for NAT Gateway

- Create Elastic IP 


#### Step 7b - Create a NAT Gateway in the public subnet so that servers in the private subnet can reach the internet

- Create NAT Gateway
- Allocate IP Address
- Attach to public Subnet 1

![create rtb-public](./images/nat-gateway.JPG)

![create rtb-public](./images/nat-gateway2.JPG)


#### Step 8 - Creat route table for Private Subnet

- Create Route table
- Edit route
- Target is NAT-gw


![create rtb-public](./images/private-rtb1.JPG)

#### Step 9 - Create a route and point to the NAT gateway
---

#### Step 10 - Edit subnet associations for the private subnet. Associate compute and proxy layers

![create rtb-public](./images/edit-subnet-associations-private.JPG)
---
#### All subnet associations
![create rtb-public](./images/all-subnet-associations.JPG)

#### Step 11 - Create security group for Bastion. Only allow Devops Engineers to connect to server from host machine 92.40.178.36
![create sg  Bastion](./images/bastion-sg.JPG)
#### Step 12 - Create security group for external load balancer called ACME-ext-alb-sg
- Allow port 80 and 443
![create ext alb](./images/ext-alb-sg.JPG)
---
#### Step 13 - Create security group for ALB to talk to Nginx server

![create sg  Bastion](./images/nginx-sg.JPG) 
---

#### Step 14 - Create an external facing load balancer

- We need to create our target group first
#### Create Target Groups (Instances) which will be part of our autoscaling group

![Target Nginx](./images/target-group-nginx.JPG)

- create load balancer ACME-ext-alb

#### Step 15 - Create listener
- listener forwards traffic from port 80 to nginx target group
### 
--- 


![create ext alb](./images/ext-lb-listener.JPG)


#### The autoscaling grou will require a launch template tp provision our instances or resources
- I will now create a launch Template for our Nginx servers
---

#### Step 16 - Create launch Template for Nginx (Use Red Hat Linux for this)

![create ext alb](./images/Nginxlaunchtemp1.JPG)
![create ext alb](./images/Nginxlaunchtemp2.JPG)
![create ext alb](./images/Nginxlaunchtemp3.JPG)
![create ext alb](./images/Nginxlaunchtemp4.JPG)
![create ext alb](./images/Nginxlaunchtemp4.JPG)


#### Step 17 - Create Autoscaling group and associate with launch template
![create ext alb](./images/asgnginx1.JPG)
![create ext alb](./images/asgnginx2.JPG)
![create ext alb](./images/asgnginx3.JPG)
![create ext alb](./images/asgnginx4.JPG)
![create ext alb](./images/asgnginx5.JPG)
---
### ASG has launched instances successfully
![create ext alb](./images/asginstances.JPG)
---
#### Step 18 - Create launch template for Bastion which is in public subnet
![create ext alb](./images/autoscalingbastion1.JPG)
![create ext alb](./images/autoscalingbastion2.JPG)
![create ext alb](./images/autoscalingbastion3.JPG)
![create ext alb](./images/autoscalingbastion4.JPG)

---
#### Instances launched by ASG's
![create ext alb](./images/instancesbastionnginx.JPG)

#### Our public servers i.e bastion has no public IP address. 
- This needs to be set at the subnet level by checking enable auto-assign public IPv4 address

![create ext alb](./images/autoassign-ip.JPG)
---

#### We can now SSH into the bastion once it has a PUBLIC IP address
- Best to SSH using pem as a variable

![create ext alb](./images/addsshkey.JPG)

```
deles@DESKTOP-PURLK18 MINGW64 ~/Downloads
$ mykey=ACME-keypair.pem

deles@DESKTOP-PURLK18 MINGW64 ~/Downloads
$ eval $(ssh-agent)
Agent pid 1147

deles@DESKTOP-PURLK18 MINGW64 ~/Downloads
$ ssh-add $mykey
Identity added: ACME-keypair.pem (ACME-keypair.pem)
```
```
ssh -Ai $mykey ubuntu@18.130.56.49
```
```
ubuntu@ip-10-0-2-5:~$ ssh ec2-user@10.0.3.40                                                                                                                        
The authenticity of host '10.0.3.40 (10.0.3.40)' can't be established.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
[ec2-user@ip-10-0-3-40 ~]$ 
```
### Successfully connected to the Nginx server
```

#!/bin/bash
sudo yum update -y
sudo yum install -y nginx git
sudo systemctl enable nginx
sudo systemctl start nginx
sudo yum -y install https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
sudo yum install -y dnf-utils  https://rpms.remirepo.net/enterprise/remi-release-7.rpm
```
#### Nginx successfully installed
#### Load balancer DNS name in browser loads nginx page
![create ext alb](./images/nginxinstalled.JPG)

#### I UPDATED NGINX LAUNCH TEPLATE WITH  SCRIPT TO INSTALL NGINX
#### I UPDATED ASG TO USE NE- I shut down the instance so that ASG will provision a new one with the new template
- New Instance has Nginx Installed successfully
![create ext alb](./images/nginxinstalled.JPG)
