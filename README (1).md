
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
![image](https://github.com/JonesKwameOsei/AWS-App-Autoscaling/assets/81886509/b41dce2d-95f6-4855-8014df5a74df7b2f)
2. In the Dashboard, select **Ctreate VPC**
![image](https://github.com/JonesKwameOsei/AWS-App-Autoscaling/assets/81886509/4812ec8e-85f9-49aa-8bf1ea3772f6b206)
3. Select **VPC and more** under **Resources to create**
**Note:**There are two options to choose from, **VPC Only** and **VPC and more**.
- **VPC Only:** Creating only the VPC (Virtual Private Cloud) resource typically involves setting up the fundamental networking infrastructure for your cloud environment.
- **VPC and more:**  creating the VPC along with other networking resources involves setting up additional components to enhance the functionality and connectivity of your cloud environment. These additional networking resources may include: Subnets, Route tables, Internet Gateways, Nat Gateways or Nat Instances, Elastic Load Balancers (ELBs) which wwe will be creating, and etc.
4. Enter a name for the VPC to be created under **Name tag auto-generation**
![image](https://github.com/JonesKwameOsei/AWS-App-Autoscaling/assets/81886509/108111a4-5de0-48e5-a4a8e7a946ce42d1)
5. Under **IPv4 CIDR block** and **IPv6 CIDR block** select 10.0.0.0/16 IPV4 and No IPv6 CIDR block, respectively.
  Note: Selecting IPV4 10.0.0.0/16 gives us access to 65,536 IPs.
6. Leave Tenancy on **Default**
![image](https://github.com/JonesKwameOsei/AWS-App-Autoscaling/assets/81886509/b232b61b-860c-4877-b424737f6a751263)




