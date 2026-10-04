# TravelMemory Application Deployment on AWS

## Overview

This project demonstrates the deployment of the TravelMemory MERN application on AWS using multiple EC2 application servers behind an Application Load Balancer.

The deployment focuses on scalability, availability, network security, automated deployment, and load balancing.

## Architecture

The deployed architecture follows this flow:

Internet  
↓  
AWS Application Load Balancer  
↓  
TravelMemory App Server 1 + TravelMemory App Server 2  
↓  
MongoDB EC2 Instance (Private Network)

### AWS Infrastructure

- VPC: `travelmemory-vpc`
- VPC CIDR: `10.0.0.0/16`
- Multiple public subnets across Availability Zones
- Private subnet for database infrastructure
- Application Load Balancer: `travelmemory-alb`
- Target Group: `travelmemory-app-tg`
- Two EC2 application instances
- Private MongoDB EC2 instance
- AWS Security Groups

## EC2 Application Servers

Two EC2 application servers were deployed:

- `travelmemory-app-1`
- `travelmemory-app-2`

The TravelMemory application runs on port `3000`.

Both servers were registered with the AWS Application Load Balancer target group.

## Automated Deployment with Ansible

Ansible was used to deploy TravelMemory to both application servers simultaneously.

The automation performs tasks including:

- Installing required system packages
- Installing Node.js and npm
- Installing PM2
- Cloning the TravelMemory source code
- Installing backend dependencies
- Configuring backend environment variables
- Installing frontend dependencies
- Configuring frontend environment variables
- Starting the backend with PM2
- Starting the frontend with PM2
- Saving the PM2 process configuration
- Disabling SSH root login

The Ansible deployment completed successfully on both application servers with zero failed and zero unreachable hosts.

## Application Load Balancer

An internet-facing AWS Application Load Balancer was created:

`travelmemory-alb`

The ALB distributes incoming application traffic between the two EC2 application instances.

ALB endpoint:

`travelmemory-alb-2021764159.us-east-1.elb.amazonaws.com`

## Target Group and Health Checks

Target Group:

`travelmemory-app-tg`

Configuration:

- Protocol: HTTP
- Port: 3000
- Health check path: `/`

Both EC2 application instances successfully passed the AWS health checks and reported a **Healthy** status.

This confirms that the Application Load Balancer can successfully communicate with both application servers.

## Database

MongoDB runs on a separate EC2 instance using the private AWS network.

Database private IP:

`10.0.2.83`

The database is not intended to be directly exposed to public Internet traffic. Application servers communicate with MongoDB through the VPC private network.

## Security

The deployment includes several security measures:

- AWS Security Groups for traffic control
- SSH key-based authentication
- SSH root login disabled through Ansible
- MongoDB hosted on the private network
- Private IP communication between application and database tiers
- Load balancer used as the public application entry point

## Scalability

The application tier contains multiple EC2 instances behind an Application Load Balancer.

This architecture allows additional EC2 application servers to be registered with the target group as application traffic increases.

The ALB automatically distributes incoming traffic between healthy registered targets.

## Availability and Resilience

Multiple application servers reduce dependence on a single EC2 instance.

AWS health checks continuously verify the application instances. If one target becomes unhealthy, the load balancer can route requests to another healthy instance.

The Application Load Balancer is configured using public subnets across multiple Availability Zones.

## Cloudflare and Custom Domain

The planned production DNS architecture is:

Custom Domain  
↓  
Cloudflare DNS  
↓  
AWS Application Load Balancer  
↓  
EC2 Application Servers

A Cloudflare CNAME record can point an application hostname to the AWS ALB DNS endpoint.

A custom domain was not available during this assignment, so Cloudflare DNS activation was not performed. The deployment was instead verified directly using the AWS Application Load Balancer endpoint.

## Deployment Verification

The deployment was successfully verified through:

- Multiple running EC2 application instances
- Successful Ansible deployment to both application servers
- Application processes managed using PM2
- Application Load Balancer configured successfully
- Two application instances registered with the target group
- Both ALB targets reporting Healthy
- TravelMemory accessible through the load balancer endpoint

## Technologies Used

- AWS EC2
- AWS VPC
- AWS Application Load Balancer
- AWS Target Groups
- AWS Security Groups
- Ansible
- PM2
- Node.js
- Express.js
- React
- MongoDB
- Git
- GitHub

## Screenshots

Deployment evidence is available in the `screenshots/` directory.

The screenshots demonstrate the AWS infrastructure, EC2 instances, load balancer configuration, target group registration and health status, and the deployed TravelMemory application.

## Future Improvements

The deployment can be further improved by implementing:

- Cloudflare custom-domain integration
- HTTPS using AWS Certificate Manager
- Auto Scaling Groups
- Nginx reverse proxy
- Automated CI/CD deployment
- MongoDB replication or a managed database service
- Improved Security Group isolation

## Conclusion

The TravelMemory MERN application was successfully deployed using a scalable AWS architecture.

Two EC2 application servers were deployed using Ansible and registered behind an AWS Application Load Balancer. Both targets passed AWS health checks, while MongoDB remained on the private network.

The resulting architecture provides a foundation for scalable and resilient application deployment on AWS.
