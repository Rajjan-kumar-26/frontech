# frontech



#Project Architecture

![project architecture](https://github.com/Rajjan-kumar-26/frontech/blob/f89e060c461b66337d59b580780fd6d669ae07f3/frontech%20architecture.png)

This project demonstrates a highly available and auto-scalable web application architecture built on AWS. It combines networking, load balancing, Auto Scaling, monitoring, alerting, and automated instance registration to provide a reliable and scalable infrastructure.

🔄 Architecture Flow

Internet Users → Route 53 → CloudFront Distribution → Internet Gateway → VPC → ALB → Target Group → EC2 Instances managed by ASG






### 1. VPC
![create vpc](https://github.com/Rajjan-kumar-26/frontech/blob/f466ff7ccbc28599a61784c7b978ccb90c4c422f/screenshot%20project%20archtech/vpc.png)

A **Virtual Private Cloud (VPC)** is created to provide an isolated and secure networking environment for the AWS infrastructure. The VPC acts as the foundation of the project where resources such as the **Application Load Balancer and EC2 instances** are deployed.

The VPC is configured with the required **CIDR block, subnets, route tables, and Internet Gateway** to enable controlled communication between the application resources and the internet.

**Purpose:**

* Provides an isolated AWS network.
* Defines the IP address range using CIDR.
* Supports public/private subnet architecture.
* Enables controlled internet connectivity.
* Provides the foundation for deploying and securing AWS resources.








###### 2. Security Group

![Security Group](https://github.com/Rajjan-kumar-26/frontech/blob/f466ff7ccbc28599a61784c7b978ccb90c4c422f/screenshot%20project%20archtech/sg.png)

A **Security Group** is created to act as a virtual firewall for the AWS resources. It controls inbound and outbound traffic and ensures that only required network traffic is allowed to access the application.

The Security Group is configured with the necessary rules for **HTTP (80)**, **HTTPS (443)**, and **SSH (22)** access as required.

**Purpose:**

* Controls inbound and outbound traffic.
* Allows only required ports.
* Protects EC2 instances from unauthorized access.
* Provides network-level security for the application.








#### 3 Launch Template
![Launch ec2 Template](https://github.com/Rajjan-kumar-26/frontech/blob/f466ff7ccbc28599a61784c7b978ccb90c4c422f/screenshot%20project%20archtech/templete.png)

A **Launch Template** is created to define the configuration required to launch EC2 instances automatically through the Auto Scaling Group.

It contains the **AMI, instance type, key pair, Security Group, and User Data** configuration. Whenever the ASG needs a new instance, it uses this Launch Template to launch an instance with the same configuration.

**Purpose:**

* Provides a standard EC2 configuration.
* Enables automatic instance launching.
* Works with the Auto Scaling Group.
* Ensures all new instances have the same application setup.
* Reduces manual EC2 configuration.







### 4 Target Group
![target group](https://github.com/Rajjan-kumar-26/frontech/blob/f466ff7ccbc28599a61784c7b978ccb90c4c422f/screenshot%20project%20archtech/register%20target.png)

A **Target Group** is created to manage the EC2 instances that receive traffic from the Application Load Balancer (ALB).

The Target Group performs **health checks** on the registered EC2 instances and ensures that the ALB sends traffic only to healthy instances.

**Purpose:**

* Connects the ALB with EC2 instances.
* Performs health checks.
* Routes traffic only to healthy instances.
* Supports automatic registration of new ASG instances.





##### AMI Creation & EC2 Auto Deployment

![ami](https://github.com/Rajjan-kumar-26/frontech/blob/eccdf279c2878a31987f58da83c7bcfee824fff2/screenshot%20project%20archtech/ami.png)

A custom **Amazon Machine Image (AMI)** is created from a configured EC2 instance. The AMI contains the required operating system configuration, installed Nginx web server, application files, and other required settings.

The custom AMI is then used in the **Launch Template**. When the Auto Scaling Group requires a new instance, it automatically launches an EC2 instance from this AMI with the same pre-configured environment.

**Deployment Flow:**

Configured EC2 Instance
↓
Create Custom AMI
↓
AMI Added to Launch Template
↓
Auto Scaling Group
↓
Automatically Launch EC2 Instance
↓
Target Group Auto Registration
↓
Health Check
↓
ALB Traffic Distribution

**Benefits:**

* Faster EC2 deployment.
* Consistent server configuration.
* No need to manually install Nginx on every new instance.
* New instances are ready with the required application environment.
* Useful for reliable Auto Scaling deployments.







### 5 Application Load Balancer (ALB)

![alb](https://github.com/Rajjan-kumar-26/frontech/blob/f466ff7ccbc28599a61784c7b978ccb90c4c422f/screenshot%20project%20archtech/alb.png)

The **Application Load Balancer (ALB)** acts as the entry point for incoming application traffic. It receives requests from users and forwards them to healthy EC2 instances through the Target Group.

**Purpose:**

* Distributes incoming traffic across EC2 instances.
* Improves application availability.
* Sends traffic only to healthy instances.
* Works with the Target Group and Auto Scaling Group.









### 6  Auto Scaling Group (ASG)
![asg](https://github.com/Rajjan-kumar-26/frontech/blob/f466ff7ccbc28599a61784c7b978ccb90c4c422f/screenshot%20project%20archtech/asg.png)

The **Auto Scaling Group (ASG)** automatically manages the EC2 instances based on the application's demand. It launches new instances when additional capacity is required and can terminate instances when demand decreases.

The ASG uses the **Launch Template** to create new EC2 instances and automatically registers them with the **Target Group**.

**Purpose:**

* Automatically launches EC2 instances.
* Maintains the configured minimum and desired capacity.
* Scales out when demand increases.
* Scales in when demand decreases.
* Replaces unhealthy instances.
* Automatically registers new instances with the Target Group.








### 7  EC2 Connect & Nginx Setup
![ec2 connect](https://github.com/Rajjan-kumar-26/frontech/blob/f466ff7ccbc28599a61784c7b978ccb90c4c422f/screenshot%20project%20archtech/connect%20ec2.png)


![install nginx](https://github.com/Rajjan-kumar-26/frontech/blob/f466ff7ccbc28599a61784c7b978ccb90c4c422f/screenshot%20project%20archtech/install%20nginx.png)

![nginx status](https://github.com/Rajjan-kumar-26/frontech/blob/f466ff7ccbc28599a61784c7b978ccb90c4c422f/screenshot%20project%20archtech/ec2%20status.png)

An **EC2 instance** is launched using the configured Launch Template. After launching the instance, **EC2 Instance Connect** is used to access the server and configure the web application environment.

The system packages are updated and **Nginx** is installed and started to serve the web application.

**Steps performed:**

```bash
# Update packages
sudo dnf update -y

# Install Nginx
sudo dnf install nginx -y

# Start Nginx
sudo systemctl start nginx

# Enable Nginx on boot
sudo systemctl enable nginx

# Check Nginx status
sudo systemctl status nginx
```

After Nginx is started, the EC2 instance works as a web server and can serve the application through the **Application Load Balancer**.

**Flow:**

EC2 Launch → EC2 Connect → System Update → Nginx Install → Start Nginx → Health Check → Target Group → ALB









### 8 GitHub to EC2 Deployment

![git clone ](https://github.com/Rajjan-kumar-26/frontech/blob/f466ff7ccbc28599a61784c7b978ccb90c4c422f/screenshot%20project%20archtech/clone%20code%20cm.png)


The web application files are hosted on **GitHub** and cloned directly into the EC2 instance. The `index.html` file is then placed in the Nginx web root directory so that Nginx can serve the website.

**Steps:**

```bash
# Install Git
sudo dnf install git -y

# Clone GitHub repository
git clone <YOUR-GITHUB-REPOSITORY-URL>

# Enter project directory
cd <YOUR-PROJECT-FOLDER>

# Copy index.html to Nginx web root
sudo cp index.html /usr/share/nginx/html/

# Restart Nginx
sudo systemctl restart nginx

# Check Nginx status
sudo systemctl status nginx
```

After deployment, Nginx serves the `index.html` file through the EC2 instance.

**Deployment Flow:**

GitHub Repository → Git Clone → EC2 Instance → `index.html` → Nginx → Target Group → ALB → User








#### 9 Health Check
![health check](https://github.com/Rajjan-kumar-26/frontech/blob/f466ff7ccbc28599a61784c7b978ccb90c4c422f/screenshot%20project%20archtech/register%20target.png)

The **Health Check** continuously monitors the health of EC2 instances registered in the Target Group. It checks whether an instance is responding correctly and marks it as **Healthy** or **Unhealthy**.

If an instance becomes unhealthy, the **Auto Scaling Group** can replace it with a new healthy instance, helping maintain application availability.

**Purpose:**

* Monitors EC2 instance health.
* Sends traffic only to healthy instances.
* Detects failed or unhealthy instances.
* Helps ASG replace unhealthy instances automatically.
* Improves application availability and reliability.





### 10 SNS Topic & Email Notification

![topic](https://github.com/Rajjan-kumar-26/frontech/blob/f466ff7ccbc28599a61784c7b978ccb90c4c422f/screenshot%20project%20archtech/sns%20topic.png)
![subscription](https://github.com/Rajjan-kumar-26/frontech/blob/f466ff7ccbc28599a61784c7b978ccb90c4c422f/screenshot%20project%20archtech/sns%20subscription.png)


An **Amazon SNS (Simple Notification Service) Topic** is created to receive notifications from CloudWatch alarms. An email subscription is added to the SNS topic so that high CPU utilization alerts can be received through email.

**Steps:**

1. Create an **SNS Topic** for EC2 monitoring notifications.
2. Create an **Email Subscription** using the required email endpoint.
3. AWS sends a **Subscription Confirmation** email to the subscribed email address.
4. Open the email and click **Confirm subscription**.
5. After confirmation, the subscription status changes to **Confirmed**.
6. When the CloudWatch alarm is triggered, SNS sends the alert to the confirmed email subscription.

**Notification Flow:**

CloudWatch Alarm → SNS Topic → Email Subscription → Confirmation Email → Confirm Subscription → Email Alert

**Purpose:**

* Sends EC2 monitoring alerts through email.
* Provides real-time notification when an alarm is triggered.
* Integrates CloudWatch with SNS for automated monitoring.









 
### 11 CloudWatch EC2 CPU Utilization Alarm

**Amazon CloudWatch** is configured to monitor the CPU utilization of the EC2 instances. A CPU utilization alarm is created to detect when an instance is experiencing high CPU usage.

For this project, the alarm can be configured with a threshold such as **CPU Utilization ≥ 70%**. When the threshold is reached, CloudWatch changes the alarm state to **ALARM** and sends a notification to the configured SNS Topic.

**Configuration:**

* **Metric:** EC2 → CPUUtilization
* **Statistic:** Average
* **Threshold:** ≥ 70%
* **Evaluation Period:** 1 period
* **Alarm Action:** Send notification to SNS Topic

**Monitoring Flow:**

EC2 Instance → CPU Utilization → CloudWatch Alarm → Threshold Crossed → SNS Topic → Email Notification

**Purpose:**

* Monitors EC2 CPU utilization.
* Detects high CPU usage.
* Sends automated email notifications.
* Works with the Auto Scaling architecture to trigger scaling actions.






### 12 High CPU Utilization & CPU Decrease Test

![inceree utilization](https://github.com/Rajjan-kumar-26/frontech/blob/f466ff7ccbc28599a61784c7b978ccb90c4c422f/screenshot%20project%20archtech/cloudwatch%20high%20utilization.png)

![decrease utilization](https://github.com/Rajjan-kumar-26/frontech/blob/f466ff7ccbc28599a61784c7b978ccb90c4c422f/screenshot%20project%20archtech/utilization%20decrease.png)

![utilization high command](https://github.com/Rajjan-kumar-26/frontech/blob/f466ff7ccbc28599a61784c7b978ccb90c4c422f/screenshot%20project%20archtech/high%20utilization%20command.png)




To test the Auto Scaling and CloudWatch monitoring configuration, CPU load is intentionally generated on the EC2 instance. When CPU utilization crosses the configured threshold, the **CloudWatch alarm** is triggered and sends a notification through **SNS**.

After the CPU load is stopped, CPU utilization gradually decreases and the instance returns to normal operating conditions.

**Test Flow:**

EC2 Instance → Generate High CPU Load → CPU Utilization Increases → CloudWatch Alarm → SNS Email Alert → ASG Scale Out

After stopping the CPU load:

CPU Load Stopped → CPU Utilization Decreases → CloudWatch Returns to OK → Normal Operation

**Commands used:**

```bash
# Generate high CPU utilization
stress-ng --cpu 2 --timeout 10m

# Stop CPU load
sudo pkill stress-ng

# Monitor CPU utilization
top
```

**Purpose:**

* Test CloudWatch CPU monitoring.
* Verify SNS email notifications.
* Test ASG scale-out behavior.
* Verify that CPU utilization decreases after the load is stopped.








### 13 Automatic Instance Increase & Decrease

![ec2 instance increse auto](https://github.com/Rajjan-kumar-26/frontech/blob/f466ff7ccbc28599a61784c7b978ccb90c4c422f/screenshot%20project%20archtech/auto%20create%20instance.png)
![ec2 instance decrease auto ](https://github.com/Rajjan-kumar-26/frontech/blob/f466ff7ccbc28599a61784c7b978ccb90c4c422f/screenshot%20project%20archtech/terminate%20instance.png)



The **Auto Scaling Group (ASG)** automatically adjusts the number of EC2 instances according to application demand and CPU utilization.

When CPU utilization becomes high and crosses the configured scaling threshold, the ASG performs a **Scale Out** operation and automatically launches a new EC2 instance using the Launch Template.

When CPU utilization decreases and remains below the configured scale-in threshold, the ASG performs a **Scale In** operation and removes an unnecessary EC2 instance.

**Scale Out:**

High CPU Utilization → CloudWatch Alarm → ASG Scale Out → New EC2 Instance → Target Group → ALB

**Scale In:**

Low CPU Utilization → CloudWatch Alarm/Scaling Policy → ASG Scale In → Unnecessary Instance Terminated

**Example Capacity:**

* Minimum Capacity: **1**
* Desired Capacity: **1**
* Maximum Capacity: **3**

This automation helps maintain **application availability, performance, and efficient resource utilization** without manually launching or terminating EC2 instances.









## 🎯 Final Project Explanation

This project demonstrates a complete **scalable and highly available AWS web application architecture**.

The infrastructure starts with a **VPC** that provides the networking environment. The **Route Table and Internet Gateway** enable internet connectivity, while **Security Groups** control access to the resources.

The **Launch Template** defines the EC2 configuration used by the **Auto Scaling Group**. The ASG launches EC2 instances automatically and registers them with the **Target Group**. The **Application Load Balancer (ALB)** distributes incoming traffic across healthy instances.

**Health Checks** continuously monitor the instances. If an instance becomes unhealthy, the Auto Scaling architecture can replace it.

For monitoring, **CloudWatch** tracks EC2 CPU utilization. When CPU utilization becomes high, the configured alarm can trigger an **SNS notification**, which sends an email alert to the confirmed subscription.

The ASG can also automatically perform **Scale Out** when demand increases and **Scale In** when demand decreases.

### 🔄 Complete Architecture

```text
User
 ↓
Application Load Balancer
 ↓
Target Group
 ↓
EC2 Instances
 ↑
Auto Scaling Group
 ↓
Launch Template

EC2 CPU Utilization
 ↓
CloudWatch Alarm
 ↓
SNS Topic
 ↓
Email Notification

High CPU
 ↓
ASG Scale Out
 ↓
New EC2 Instance
 ↓
Auto Registered in Target Group

Low CPU
 ↓
ASG Scale In
 ↓
Unnecessary Instance Removed
```

