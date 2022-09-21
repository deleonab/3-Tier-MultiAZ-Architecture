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

Create Internet Gateway ACME-igw
![IG created](./images/igw-created.JPG)

Attach Internet Gateway ACME-igw to ACME-vpc
![IG attach](./images/igw-attaching.JPG)

Successfully attached
![IG attached](./images/igw-attached.JPG)


