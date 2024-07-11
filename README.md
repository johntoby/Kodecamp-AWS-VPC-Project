# Kodecamp-AWS-VPC-Project
This project was designed to test my understanding of AWS VPC Networking. I will be creating a new Custom VPC network, with 2 subnets (private and public), route tables, security groups, and network access control lists (NACLs) to ensure proper communication and security within the VPC. All this will be created in the AWS EU-West-1 (Ireland) region. The entire project will be done using the AWS Console. Hopefully, in later projects, I will automate this process by using Terraform to deploy it. 

## PROJECT DESIGN 

This is the Architecture diagram of the network I will be creating: 

![architecture diagram](https://github.com/user-attachments/assets/1c923eb3-ce79-4d07-bbb4-c84ff42696f1)

<br>

## VPC CREATION 
For security reasons, it is always advised not to use the default VPC that comes with the AWS account. So I am going to create a custom VPC named KCVPC. Here, I will specify my region (Europe eu-west-1 Ireland), VPC name, CIDR block of 10.0.0.0/16 and created as shown in the picture below: 


![creating vpc](https://github.com/user-attachments/assets/19de5b84-863e-4e4d-9569-b65bea070593) 

<br> 

## SUBNET CREATION
Second task is to create 2 subnets. The first subnet will be named PublicSubnet, linked to my KCVPC, and given the CIDR Block 10.0.1.0/24. It will be created in eu-west-1 region and the availability zone will be eu-west-1a. Since this is a Public Subnet, It will have a direct access to the internet and will house my Webserver.


![creating publicsubnet](https://github.com/user-attachments/assets/03c73d98-ad2e-49d2-8120-bbded3a25341) 

<br> 

The second subnet will be named PrivateSubnet, also linked to my KCVPC, and given the CIDR block 10.0.2.0/24. Just like the PublicSubnet above, it will also be created in eu-west-1 region and the availability zone will be eu-west-1a. Since it is a Private Subnet, It will have not direct access to the internet, but will be a ble to connect to the internet through a NAT gateway that will be configured later on. This subnet will house my Database server. 

![creating privatesubnet](https://github.com/user-attachments/assets/4ca26996-7476-43bf-a1a5-e76bb20c6538) 

<br> 

My two subnets are now created, and I am ready for further configurations


![2 subnets ready](https://github.com/user-attachments/assets/4f803b54-628c-4f6f-97c9-83c2d6fd9b66)

<br> 


## Internet Gateway 
An Internet Gateway is a means of providing direct access to the internet. 


