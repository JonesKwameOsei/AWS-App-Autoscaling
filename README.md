
# Creating a load-balanced Web Server with Auto Scaling.

AWS Auto Scaling is a service provided by Amazon Web Services that allows users to automatically adjust the capacity of their applications in order to maintain consistent and predictable performance while minimizing costs. This is achieved through the monitoring of various metrics and the automatic scaling of resources such as EC2 instances, ECS tasks, DynamoDB tables, and more. By using AWS Auto Scaling, users can ensure that their applications are able to handle fluctuations in demand without over-provisioning resources, ultimately leading to cost savings and improved performance. 

## Overview
This project seeks to expose cloud engineers and soultions architects through the methodology of creating a web server that is well managed by an auto scaling group. With a launch template, the auto scaling group launches the server into a target group where an internet-facing load balancer directs traffic to the target group. 

# Architecture Diagram
![autoscale-diagram-Page-1 drawio2](https://github.com/JonesKwameOsei/AWS-App-Autoscaling/assets/81886509/342410ae-24ed-4ecd-adb3-20f2983d888b)

# Create VPC
Amazon **Virtual Private Cloud** or **Amazon VPC** is a logicaly isolated **virtual network** dedicated to give full control over virtual networking environment including security, connectivity and resource placement. A VPC allows you to isolate and organize your resources within the cloud, providing a private network space where you can deploy and run your virtual machines, databases, and other services. As an isolated private network, you can create or use a default VPC to run workloads. Creating a VPC means having a complete control over it as compared to the default vpc. 

To Create a VPC, we follow these steps:
1. In the AWS console, search for VPC in the search bar and click on it to open the VPC dashboard

![image](https://github.com/JonesKwameOsei/AWS-App-Autoscaling/assets/81886509/1bb6a2d8-8ce1-41dd-8e99-08eeae7f37b2)

1a. Resource tags: Key: Name, Value: autoscale-webserver. <p>
2. In the Dashboard, select **Ctreate VPC**. <p>

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

![image](https://github.com/JonesKwameOsei/AWS-App-Autoscaling/assets/81886509/8c4d62b0-31b1-4e09-a005-5fa123e7cebc)


### For Network Congurations:
9. For Subnet, we won't include in the launch template.

![image](https://github.com/JonesKwameOsei/AWS-App-Autoscaling/assets/81886509/e106e1df-23e9-4b3b-a4ec-5f6268ddcd3a)

10. VPC, Select the **App-Autoscale-webserver VPC created**.
11. Under Advanced network configuration, choose "Enable" under Auto-assign public IP <p>

![image](https://github.com/JonesKwameOsei/AWS-App-Autoscaling/assets/81886509/f2853988-8f4b-4985-b9ef-e9367c9b6583)

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
```
#!/bin/bash -ex
sudo su
yum -y update
yum install httpd -y
systemctl start httpd
systemctl enable httpd
systemctl status httpd
echo "<html>Hello World, welcome to my server</html>" > /var/www/html/index.html
systemctl restart httpd
amazon-linux-extras install epel -y
yum install stress -y
```

Launch Template created! Click **View Launch Template**
![image](https://github.com/JonesKwameOsei/AWS-App-Autoscaling/assets/81886509/f40072fe-f881-4d62-a737-3580effbe1bc) <p>
![image](https://github.com/JonesKwameOsei/AWS-App-Autoscaling/assets/81886509/ec9c2553-d872-402b-9f63-e6aa17a1edc2)

# Create Target Group 
A target group is responsible for directing requests to the web servers we establish. The load balancer relies on this target group to determine the specific set of servers to distribute traffic to. Additionally, our auto scaling group will be linked to this target group, ensuring that it launches our servers into the designated target group. <p>
1. Click on Create target group from the EC2 console to create a target group <p><p>

![image](https://github.com/JonesKwameOsei/AWS-App-Autoscaling/assets/81886509/5f94235d-ee9c-48a1-9630-5ff318f347ce) <p>

2. Choose a target type: **Instances**.<p><p>

![image](https://github.com/JonesKwameOsei/AWS-App-Autoscaling/assets/81886509/7b1aa053-65ef-43b9-9c14-d115480d69ab) <p>

3. Target group name: **app-autoscale-webserver** 
4. Protocol: **HTTP**
5. Port: **80**
6. VPC: Select the **app-autoscale-webserver VPC** we created earlier.
7. We will leave every other value on this page as default. <p><p>
![image](https://github.com/JonesKwameOsei/AWS-App-Autoscaling/assets/81886509/2a50e04d-a3e5-4f6b-993d-d39584b61dfe) <p>

8. Click **Next**<p>
![image](https://github.com/JonesKwameOsei/AWS-App-Autoscaling/assets/81886509/025df5a2-6f24-4339-805f-09bc27f29f5f) <p>

9. Register Targets: Leave as is.<p>
![image](https://github.com/JonesKwameOsei/AWS-App-Autoscaling/assets/81886509/77ba54f0-8683-4d0c-8af1-16864a699425) <p>
10. Click **Create Target Group**
![image](https://github.com/JonesKwameOsei/AWS-App-Autoscaling/assets/81886509/5fede922-2929-4c38-8cc3-5a1a83747520)

# Create Load Balancer
The application load balancer serves as the primary access point for directing traffic to our web servers. Rather than users accessing our application directly, the load balancer is utilised to distribute traffic across our autoscaling group of web servers evenly. This approach enhances load management, security, and the overall reliability of our application. Here, we will: <p>
1. Click on Create load balancer from the EC2 console to create a load balancer. <p>
![image](https://github.com/JonesKwameOsei/AWS-App-Autoscaling/assets/81886509/e7b87337-dc9f-482b-a400-4bbba4703020)

2. Enter: **"App-Load-Balancer**.
3. Scheme: **Internet-facing**.
4. IP address type: **IPV4**. <p>
![image](https://github.com/JonesKwameOsei/AWS-App-Autoscaling/assets/81886509/1ce60387-35eb-49f4-ae1d-88afa9387cdc)

5. VPC: We will select the VPC we created.
6. Mappings: Check the box beside the two AZs listed.<p>
7. Subnet: For each AZ selected, choose the public subnet in the dropdown menu. <p>
![image](https://github.com/JonesKwameOsei/AWS-App-Autoscaling/assets/81886509/558a5ae2-8250-4597-a088-4f10ea270fc1)

8. At this point, go to the Security groups console and create a new security group for the load balancer. The inbound rule should allow HTTP traffic from anywhere.
9. Select this security group as the load balancer security group
10. Listeners and routing: Leave protocol and port as HTTP:80. Select the target group you created as target group. <p>
![image](https://github.com/JonesKwameOsei/AWS-App-Autoscaling/assets/81886509/082e1f58-7b8c-4707-9584-beac2a8977d9)

11. Leave every other configuration as default, review and click Create load balancer. <p>
![image](https://github.com/JonesKwameOsei/AWS-App-Autoscaling/assets/81886509/e2f086f0-bb3d-4561-a2d5-5fa8305491b9)

12. Application Load Balancer created successfuly. Let's view it. <p><p>
![image](https://github.com/JonesKwameOsei/AWS-App-Autoscaling/assets/81886509/8633a42b-bfd1-48e2-9dc1-36b80891cc57)
![image](https://github.com/JonesKwameOsei/AWS-App-Autoscaling/assets/81886509/17a15a63-7e1b-4d68-9875-a03296f8f748)

# Create Auto Scaling Group
The auto scaling group configures and controls how your application scales automatically in response to varying traffic situations.<p>
1. Click on Create Auto Scaling group from the EC2 console to create an auto scaling group. <p>
![image](https://github.com/JonesKwameOsei/AWS-App-Autoscaling/assets/81886509/a0dad026-82a2-4600-a1d5-ec6bcefeed01) <p>
2. Enter a name, **Webserver-autoscale** <p>
2a. Choose the launch template you created. <p>
![image](https://github.com/JonesKwameOsei/AWS-App-Autoscaling/assets/81886509/66e551df-2dac-4439-a24b-d0d45e642b8e) <p>
![image](https://github.com/JonesKwameOsei/AWS-App-Autoscaling/assets/81886509/b273e758-fe77-4472-a49e-5c1fcb4339b1)

3. Then, Click Next
4. Select your webserver VPC created from the VPC step. <p>
5. Under Availability Zones and subnets, select the two public subnets in your VPC, in different AZs.Then, Click Next. <p>
![image](https://github.com/JonesKwameOsei/AWS-App-Autoscaling/assets/81886509/a0f76b41-4ae5-4b8a-abd8-5839adce2806)

*NB:* Note that the launch template can be overwritten by using the auto scaling group to override the instance type config from Under Load balancing. 
6. Choose the option **Attach to an existing load balancer**.
7. Select **Choose from your load balancer target groups**.
8. Select the **target group we created**.<p>
![image](https://github.com/JonesKwameOsei/AWS-App-Autoscaling/assets/81886509/57fac182-d7ba-4eae-84ba-4868109ccaeb)

9. Select VPC Lattice service to attach: **No VPC Lattice service**.
10. Additional health check types - optional: **Turn on Elastic Load Balancing health checks**.
11. Leave every other config as default. Set Health check grace periodHealth check grace period to **2**.<p>
![image](https://github.com/JonesKwameOsei/AWS-App-Autoscaling/assets/81886509/c1f5d7f3-c9c6-497c-a4bf-de1168e2a5d3) <p>
12. Then, click **Next**
13. Group size: Desired: "2", Minimum: "1", Maximum: "4". <p>
![image](https://github.com/JonesKwameOsei/AWS-App-Autoscaling/assets/81886509/a77d1921-4024-44c3-80f0-000a35074904)

14. Scaling policies: "Target Tracking Policy"
15. Metric type: "Average CPU Utilization"
15. Target Value: "50%"
16. Add notifications - optional (Skipped). Then click **Next** <p>
![image](https://github.com/JonesKwameOsei/AWS-App-Autoscaling/assets/81886509/0e46abe0-ddf2-42ce-8a1f-cf4f9ce1ac2c)
![image](https://github.com/JonesKwameOsei/AWS-App-Autoscaling/assets/81886509/017905d7-6d68-4829-921f-ce3979de7651)
17. Add tags - optional (Skipped). Then click **Next**.<p>
![image](https://github.com/JonesKwameOsei/AWS-App-Autoscaling/assets/81886509/d7bd31dd-2762-4152-a785-96f0cf286bab)
![image](https://github.com/JonesKwameOsei/AWS-App-Autoscaling/assets/81886509/565bb991-c0dd-495a-9aba-71759a82e5d9)

19. Review and create Auto Scaling Group. <p>
![image](https://github.com/JonesKwameOsei/AWS-App-Autoscaling/assets/81886509/292de910-ddd6-4cfc-917f-bbe012f00289)
![image](https://github.com/JonesKwameOsei/AWS-App-Autoscaling/assets/81886509/b91a9980-74b2-447b-9882-c9ac1df23cf4) <p>

#### Auto scaling group created and our 2 instances are launched as shown in the images below: <p>
![image](https://github.com/JonesKwameOsei/AWS-App-Autoscaling/assets/81886509/941abee4-0482-4805-8087-055d5dfee6d2)
![image](https://github.com/JonesKwameOsei/AWS-App-Autoscaling/assets/81886509/753ceb30-b5df-4628-bc9a-43909029954b)

# Testing the Web Server
Click on one of the web servers, copy the public IP (IPV4) or DNS name and paste it in the browser. You should see the following content:
![image](https://github.com/JonesKwameOsei/AWS-App-Autoscaling/assets/81886509/894349a4-0800-48ef-95ff-5aacfa85b2cd)

This is a verification that the apache web server is running and our application is able to reach it from the internet. 

# Restrict web traffic to serversRestrict web traffic to servers
Under the existing design, users have direct access to our web server, which is not desired. Hence, the introduction of a load balancer. To confine incoming HTTP traffic destined for our servers exclusively to the load balancer, we must modify the security group of the web servers. This adjustment ensures that no requests reach our servers unless they have passed through the load balancer first.<p><p>
1. Go to the autoscale-webserver-sg security group and click on "Edit inbound rules".
2. Delete the existing HTTP rule.
![image](https://github.com/JonesKwameOsei/AWS-App-Autoscaling/assets/81886509/796ae69e-991a-4547-90da-f0f540135def)

3. Add a new HTTP rule. In the Source box, scroll down to select the security group of the load balancer. Save rules.<p><p>
![image](https://github.com/JonesKwameOsei/AWS-App-Autoscaling/assets/81886509/aacc5bfe-0b87-4d6f-8cf1-f51217890c87)
![image](https://github.com/JonesKwameOsei/AWS-App-Autoscaling/assets/81886509/b0ba3b5a-63df-418c-a088-0104c872bf03)

5. You have successfully restricted traffic going to the servers to the load balancer.
6. You should no longer be able to access your web server using the server IPs or DNS names. You should now be able to use the load balancer DNS name to access the servers. Test this out.

# Live Autoscaling Test
We are about to simulate a situation where our web server experiences high CPU usage to observe the response of the auto-scaling group. By connecting via SSH to our server, we will execute a command to stress the server, causing the CPU usage across our auto-scaling group to exceed 50%. In turn, the auto-scaling group will dynamically respond by deploying additional servers until the maximum specified server limit is reached.<p><p>
![image](https://github.com/JonesKwameOsei/AWS-App-Autoscaling/assets/81886509/2c513ea9-8340-466a-a308-936b7b2d76dc)

Monitor your EC2 console following the execution of this command, and you will witness new instances being incorporated by the auto-scaling group to manage the simulated increase in traffic. If you halt or terminate the instance where CPU usage has been simulated, instances will be appropriately terminated from your auto-scaling group (scale-in).

![image](https://github.com/JonesKwameOsei/AWS-App-Autoscaling/assets/81886509/447fb20f-2425-4bbb-aa8e-34c42435a2de)
![image](https://github.com/JonesKwameOsei/AWS-App-Autoscaling/assets/81886509/d34b9d9e-9d31-4225-a779-c91fa8480c80)

## In conclusion
We have built and deployed a load-balancer and highly available web application that auto scales out based on the utilisation of a target CPU. 

## Acknowledgement
This project was inspired by Mawuli Denteh, JOMACS IT Solutions and Service. I would like to say thank you to him for all the guidance and support. 


**N/B:** For errors and contributions, contact [oseikwamejones@gmail.com]("Oseikwamejones@gmail.com")






















