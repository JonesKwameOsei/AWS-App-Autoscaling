
# Creating a load-balanced Web Server with Auto Scaling.

This is a structured training on AWS. AWS Auto Scaling monitors applications and automatically adjusts capacity to maintain steady, predictable performance at the lowest possible cost.


## Overview
This project seeks to expose cloud engineers and soultions architects through the methodology of creating a web server that is well managed by an auto scaling group. With a launch template, the auto scaling group launches the server into a target group where an internet-facing load balancer directs traffic to the target group. 

# Architecture Diagram
![autoscale-diagram-Page-1 drawio2](https://github.com/JonesKwameOsei/AWS-App-Autoscaling/assets/81886509/342410ae-24ed-4ecd-adb3-20f2983d888b)

# Create VPC
Amazon **Virtual Private Cloud** or **Amazon VPC** is a logicaly isolated **virtual network** dedicated to give full control over virtual networking environment including security, connectivity and resource placement. A VPC allows you to isolate and organize your resources within the cloud, providing a private network space where you can deploy and run your virtual machines, databases, and other services. As an isolated private network, you can create or use a default VPC to run workloads. Creating a VPC means having a complete control over it as compared to the default vpc. 

To Create a VPC, we follow these steps:
1. In the AWS console, search for VPC in the search bar and click on it to open the VPC dashboard

![image](https://github.com/JonesKwameOsei/AWS-App-Autoscaling/assets/81886509/1bb6a2d8-8ce1-41dd-8e99-08eeae7f37b2)

1a. Resource tags: Key: Name, Value: autoscale-webserver.

2. In the Dashboard, select **Ctreate VPC**

![image](https://github.com/JonesKwameOsei/AWS-App-Autoscaling/assets/81886509/eb635546-db5f-4c15-b5a0-549d8e1edd1a)

3. Select **VPC and more** under **Resources to create**.
**Note:**There are two options to choose from, **VPC Only** and **VPC and more**.
- **VPC Only:** Creating only the VPC (Virtual Private Cloud) resource typically involves setting up the fundamental networking infrastructure for your cloud environment.
- **VPC and more:**  creating the VPC along with other networking resources involves setting up additional components to enhance the functionality and connectivity of your cloud environment. These additional networking resources may include: Subnets, Route tables, Internet Gateways, Nat Gateways or Nat Instances, Elastic Load Balancers (ELBs) which wwe will be creating, and etc.

![image](https://github.com/JonesKwameOsei/AWS-App-Autoscaling/assets/81886509/b8f35a52-21a6-41bc-9864-fe3d297e6159)

4. Enter a name for the VPC to be created under **Name tag auto-generation**.

![image](https://github.com/JonesKwameOsei/AWS-App-Autoscaling/assets/81886509/31557bfd-b8cb-4fe9-a572-91a5a6717eb8)

5. Under **IPv4 CIDR block** and **IPv6 CIDR block** select 10.0.0.0/16 IPV4 and No IPv6 CIDR block, respectively.
  Note: Selecting IPV4 10.0.0.0/16 gives us access to 65,536 IPs.
6. Leave Tenancy on **Default**. 

![image](https://github.com/JonesKwameOsei/AWS-App-Autoscaling/assets/81886509/cac813b4-922e-4124-b1bb-d9901f4deea8)

7. Set Number of Availability Zones (AZs), Number of public subnets, and Number of private subnets all to **2**.

![image](https://github.com/JonesKwameOsei/AWS-App-Autoscaling/assets/81886509/3d9e7845-d6b6-4afa-8bed-666e55bdd44b)

8. Set NAT Gateway, select **In 1 AZ** (Note that there is a charge for each NAT gateway).
9. Set VPC Endpoints to **S3 Gateway**. This is **Optional**, however, if you chose **In 1 AZ** for step 9, the endpoints can be beneficial in reducing NAT gateway charges and improve security by accessing S3 directly from the VPC.

![image](https://github.com/JonesKwameOsei/AWS-App-Autoscaling/assets/81886509/e5a600ba-18e0-4de2-a2cc-c92f0c1ea45f)

10. Other remaining settings should be left as **Default**.

![image](https://github.com/JonesKwameOsei/AWS-App-Autoscaling/assets/81886509/c81cbc4f-5b31-4f6f-ab0b-5000b2386fe2)

12. Next, select **Create VPC**.

![image](https://github.com/JonesKwameOsei/AWS-App-Autoscaling/assets/81886509/9b6cc62d-f97c-4f64-a172-935fd0598d9e)

Finally, VPC is created! Click **View VPC**.

![image](https://github.com/JonesKwameOsei/AWS-App-Autoscaling/assets/81886509/6b5d234d-dd12-4bd1-aef6-d0c725cc09da)

We now have our **App-Autoscale-webserver VPC created with its *Resource Map** below. 

![image](https://github.com/JonesKwameOsei/AWS-App-Autoscaling/assets/81886509/cabc5ae8-2ee7-4df2-b757-8b9aef3dc239)

![image](https://github.com/JonesKwameOsei/AWS-App-Autoscaling/assets/81886509/23f8d9b9-0227-4888-89c3-3ff1d2ff52c7)

# Create a Launch Template from Ec2<p><p>
The launch template acts as the framework for generating a specific server type tailored to fulfill our web server requirements. It allows for adjustments to be made, facilitating the creation of new versions when configuration changes are necessary. 
1. Click on Create launch template from the EC2 console to create a new launch template.

![image](https://github.com/JonesKwameOsei/AWS-App-Autoscaling/assets/81886509/fce65dfd-822a-4248-b6a5-730dbd3587a0)

2. Launch template name - required = "app-autoscale-webserver".

![image](https://github.com/JonesKwameOsei/AWS-App-Autoscaling/assets/81886509/bf65a19a-f9dd-4138-91f7-ff3633f666bb)

3. Check the Provide guidance to help me set up a template that I can use with EC2 Auto Scaling box.

![image](https://github.com/JonesKwameOsei/AWS-App-Autoscaling/assets/81886509/294cb6ca-bf35-4b68-b07a-2cd49aba8f10)

5. Under Application and OS Images (Amazon Machine Image) - required, choose **Amazon Linux 2023 AMI 2023.3.20240131.0 x86_64 HVM kernel-6.1**.
6. We will leave the **Architecture** on **64-bit (x86). 

![image](https://github.com/JonesKwameOsei/AWS-App-Autoscaling/assets/81886509/24684a89-cebc-4b41-9ad9-c467ad64e28d)

7. Select **t2.micro** for the instance type.

![image](https://github.com/JonesKwameOsei/AWS-App-Autoscaling/assets/81886509/8faba772-31ca-43dc-9949-d846bf7878bf)

8. Key pair - Create a new one or use existing key pair since we will be connecting to our instance through a secure shell.

![image](https://github.com/JonesKwameOsei/AWS-App-Autoscaling/assets/81886509/7b2f6ba4-e170-46e5-aff5-fc135fa008c1)

![image](https://github.com/JonesKwameOsei/AWS-App-Autoscaling/assets/81886509/bf1ba860-dc5f-4776-aa0a-a390119200a0)

### For Network Congurations:
9. For Subnet, we won't include in the launch template.

![image](https://github.com/JonesKwameOsei/AWS-App-Autoscaling/assets/81886509/e106e1df-23e9-4b3b-a4ec-5f6268ddcd3a)

10. VPC, Select the **App-Autoscale-webserver VPC created**.
11. Under Advanced network configuration, choose "Enable" under Auto-assign public IP

![image](https://github.com/JonesKwameOsei/AWS-App-Autoscaling/assets/81886509/50755a12-a32f-4bf9-93e3-e7d40c01f091)

### For Firewall (security groups):
12. Create security group = **app-autoscale-webserver-sg** and Allow SSH and HTTP traffic from 0.0.0.0/0 (Ignore the warning about security group. We will edit it later). In the Description, enter **Allow both SSH and HTTP traffic**

![image](https://github.com/JonesKwameOsei/AWS-App-Autoscaling/assets/81886509/8e81791f-d9f3-4cb0-9545-f766f1e76799)

![image](https://github.com/JonesKwameOsei/AWS-App-Autoscaling/assets/81886509/ded117cb-760a-4740-9028-b9adc4a95f39)

### For Storage Configurations
13. Under Storage, leave all other configuration as default and choose "gp3" for Volume type.

![image](https://github.com/JonesKwameOsei/AWS-App-Autoscaling/assets/81886509/ada1b295-5c50-4c51-a92e-ed79ef8f437b)

14. Resource tags, enter this key-value pair. Key: Name, Value: app-autoscale-webserver. 

![image](https://github.com/JonesKwameOsei/AWS-App-Autoscaling/assets/81886509/2fe2d13f-d5b9-4b13-89d2-991dce3134cb)

15. For the Advanced details, scroll down to the User data section and enter the following lines of code exactly as shown:
`#!/bin/bash -ex
sudo su
yum -y update
yum install httpd -y
systemctl start httpd
systemctl enable httpd
systemctl status httpd
echo "<html>Hello World, welcome to my server</html>" > /var/www/html/index.html
systemctl restart httpd
amazon-linux-extras install epel -y
yum install stress -y`

Launch Template created! Click **View Launch Template**
![image](https://github.com/JonesKwameOsei/AWS-App-Autoscaling/assets/81886509/f40072fe-f881-4d62-a737-3580effbe1bc) <p>
![image](https://github.com/JonesKwameOsei/AWS-App-Autoscaling/assets/81886509/ec9c2553-d872-402b-9f63-e6aa17a1edc2)

# Create Target Group 
A target group is responsible for directing requests to the web servers we establish. The load balancer relies on this target group to determine the specific set of servers to distribute traffic to. Additionally, our auto scaling group will be linked to this target group, ensuring that it launches our servers into the designated target group.












