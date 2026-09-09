# frontech
Frontech – A production-ready web deployment project using Amazon S3 and GitHub Actions CI/CD. The project demonstrates automated build and deployment of a frontend application to AWS S3, with a secure, scalable, and cost-optimized cloud architecture.




#Project Architecture

![project architecture](https://github.com/Rajjan-kumar-26/frontech/blob/f89e060c461b66337d59b580780fd6d669ae07f3/frontech%20architecture.png)

This project demonstrates a highly available and auto-scalable web application architecture built on AWS. It combines networking, load balancing, Auto Scaling, monitoring, alerting, and automated instance registration to provide a reliable and scalable infrastructure.

🔄 Architecture Flow

Internet Users → Route 53 → CloudFront Distribution → Internet Gateway → VPC → ALB → Target Group → EC2 Instances managed by ASG
