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
An Internet Gateway is a means of providing direct access to the internet. The internet gateway is created named KC-IGW, and attached to KCVPC. 


![creating the internet gateway](https://github.com/user-attachments/assets/320986d6-f46f-4b8c-b3a6-0f3c95d8df1d)

<br>


![internet gateway attached to KCVPC](https://github.com/user-attachments/assets/ce27ac30-d037-4dcd-9b5d-8ca4926e5fa5) 


## ROUTE TABLES 
A route table specifies the path that will be taken by traffic within the subnet. Thus, 2 route tables will be created to handle both private/internal traffic and public traffic. 

The first Route table that will be created is the PublicRouteTable, attached to KCVPC. After creation, it will be associated with the Public Subnet under subnet associations. Also, a another route will be created that will route all traffic from 0.0.0.0/32 to the KCIGW. 

![private route table](https://github.com/user-attachments/assets/943a3b96-6819-4130-9930-aed049e60165) 


<br> 

The second Route table is the PrivateRouteTable, also attached to KCVPC and associated with the Private Subnet. 


![private route table](https://github.com/user-attachments/assets/e086c3f3-f3a6-4467-94fa-e21f940b00b8) 

<br> 

## NAT GATEWAY 
A NAT Gateway will be created in the Public Subnet, named KC-NATGW. The purpose of the NAT Gateway is to create a means for the Private subnet to access the internet. After creating the NAT Gateway, I will update the route table for my PrivateRouteTable to route traffic from 0.0.0.0 to the KC-NATGW. 


![NAT Gateway](https://github.com/user-attachments/assets/b1af4b8f-3cb5-4de1-807c-3b1588062080) 

<br> 

## SECURITY GROUPS 
Security groups are the firewalls for the VPC, filtering traffic based on allow/deny rules. AWS comes with 2 implicit rules - Default Allow all egress traffic, and default Deny all ingress traffic. Thus, I will be creating a security group for my public subnet called KC-PUBLICSG that allows ingress traffic into the network via ports 80 (HTTP) and 443 (HTTPS) from anywhere, while port 22 (SSH) traffic will only be allowed from my IP address only. 

![KC PUBLIC SG](https://github.com/user-attachments/assets/e68dedb3-8cc5-4a0a-9e3b-eb4a32c9fdb3)

<br>


The second Security Group to be created is named KC-PRIVATE-SG, which will allow only ingress traffic into port 3306 (MySQL) from the Public Subnet, and allowing all egress traffic. 


![KC PRIVATE SG](https://github.com/user-attachments/assets/c0730d05-c614-4358-b3c7-67db87c15032)


<br> 

## NETWORK ACCESS CONTROL LISTS (NACLs) 
NACLs are an extra layer of security across the network. I will be creating 2 NACLs for each subnet. 

The first NACL is the KC-Public-NACL, associated with the Public Subnet. This will allow All Outbound traffic, and only allow Inbound traffic from Port 22(SSH), port 80 (HTTP) and port 443 (HTTPS). 

![kc public nacl](https://github.com/user-attachments/assets/1085d27f-9643-450c-b456-460d663b1266)


<br> 

The second NACL is the KC-Private-NACL associated with the Private Subnet. This will allow Inbound traffic only from the Public Subnet (10.0.1.0/24), and only allow Outbound traffic to the public subnet (10.0.1.0/24) and the internet. 

![KC PRIVATE NACL](https://github.com/user-attachments/assets/648fa738-88b7-4da0-bb41-5e2bf6946691)

<br>


## LAUNCH INSTANCE 

After configuring all the VPC and network settings, it is time to deploy my ec2 instance. 2 instances will be deployed - one for my nginx Webserver and the other for my MySQL Database server. 

The Webserver instance will use Ubuntu 22.04 image, with t2.micro instance type, created in my KCVPC and PublicSubnet, using KC-Public-SG. A new keypair will be created for this server called KC-WEBSERVER-KEYPAIR. This instance has complete access to the internet. 

![WEBSERVER instance](https://github.com/user-attachments/assets/606b3a7d-7c3c-4993-ab40-65989974629b)

<br>


The Database server will be created also using an Ubuntu 22.04 image, with t2.micro instance type, created in KCVPC, PrivateSubnet, using KC-Private-SG. A new keypair will be created for this server called KC-DATABASESERVER-KEYPAIR. This instance can only access the internet via the NAT Gateway, and can also access the Webserver instance

![DATABASE SERVER INSTANCE](https://github.com/user-attachments/assets/365d5980-4f55-47a5-bf33-00ff1d90ed2b)



