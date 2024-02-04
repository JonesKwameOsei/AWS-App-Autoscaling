
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







