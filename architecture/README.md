# AWS 3-Tier Cloud Architecture

## Project Overview

This project is a Java Spring Boot Login Web Application designed around a 3-tier cloud architecture.

The application is developed and tested locally, while the AWS architecture is documented for learning and future deployment preparation.

## VPC Network Design

**VPC CIDR:** `10.0.0.0/16`

### Public Subnet

**CIDR:** `10.0.1.0/24`

Used for internet-facing components such as the Application Load Balancer.

### Private Application Subnet

**CIDR:** `10.0.2.0/24`

Used for the Java Spring Boot application running on EC2.

The application server is not directly accessible from the public internet.

### Private Database Subnet

**CIDR:** `10.0.3.0/24`

Used for the MySQL database.

The database is isolated from public internet access and accepts connections only from the application layer.

## Architecture Flow

```text
Users
  |
  v
Application Load Balancer
  |
  v
EC2 - Java Spring Boot Application
  |
  v
MySQL Database
```

## Network Security Design

### Public Subnet

- Internet-facing traffic is handled by the Load Balancer.
- HTTP/HTTPS traffic is controlled using security rules.

### Private Application Subnet

- Java Spring Boot application runs inside the private subnet.
- Direct public access to the application server is restricted.

### Private Database Subnet

- MySQL database is kept in a private subnet.
- Database access is allowed only from the application layer.

## AWS Services Planned

- Amazon VPC
- Public Subnet
- Private Subnet
- Amazon EC2
- Application Load Balancer
- Amazon RDS MySQL
- Auto Scaling
- Amazon CloudWatch
- AWS IAM

## Traffic Flow

```text
Internet
   |
   v
Load Balancer
   |
   v
Application Server
   |
   v
MySQL Database
```

## Current Implementation

The application is currently tested locally using:

- Java 11
- Spring Boot
- JSP
- MariaDB/MySQL
- XAMPP

The AWS infrastructure is documented as a learning architecture and has not been deployed because this project is being developed with a ₹0 AWS cost goal.

## Future Architecture

The planned production-style architecture includes:

- Multi-AZ deployment
- EC2 Auto Scaling
- Application Load Balancer
- Amazon RDS MySQL
- CloudWatch monitoring
- IAM-based access control
- Improved application and database security

## Security Groups

### ALB-SG

Purpose:
- Controls traffic to the Application Load Balancer.

Inbound:
- HTTP (80) - Internet
- HTTPS (443) - Internet

Outbound:
- Application traffic to APP-SG

### APP-SG

Purpose:
- Protects the Java Spring Boot application server.

Inbound:
- Application traffic from ALB-SG

Outbound:
- Database traffic to DB-SG

### DB-SG

Purpose:
- Protects the MySQL database.

Inbound:
- MySQL (3306) - Only from APP-SG

Outbound:
- Required database responses

## Security Flow

Internet
   |
   | HTTP / HTTPS
   v
ALB-SG
   |
   | Application traffic
   v
APP-SG
   |
   | MySQL 3306
   v
DB-SG

## EC2 Application Server

The Java Spring Boot application is planned to run on an Amazon EC2 instance inside the Private Application Subnet.

### Application Server

- OS: Linux
- Application: Java Spring Boot
- Java Version: 11
- Application Port: 8080
- Access: Only through the Application Load Balancer
- Database Connection: MySQL on port 3306

### Deployment Flow

Developer
   |
   v
GitHub
   |
   v
EC2 Application Server
   |
   v
MySQL Database

## Internet Gateway

An Internet Gateway (IGW) is planned for the VPC to provide internet connectivity for resources in the Public Subnet.

### Internet Gateway Flow

Internet
   |
   v
Internet Gateway
   |
   v
Public Subnet
   |
   v
Application Load Balancer

The Internet Gateway is attached to the VPC and is used by the public subnet route table for internet-bound traffic.

## Route Table

### Public Route Table

The Public Route Table is associated with the Public Subnet.

Route:

Destination: 0.0.0.0/0
Target: Internet Gateway

This allows internet traffic to reach the Application Load Balancer in the Public Subnet.

### Private Application Route Table

The Private Application Subnet uses a separate route table.

The application server does not have a direct route to the internet.

Application traffic is received through the Application Load Balancer.

### Private Database Route Table

The Private Database Subnet uses a separate route table.

The database is isolated from direct internet access and accepts connections only from the application layer.

## Network Flow

Internet
   |
   v
Internet Gateway
   |
   v
Public Subnet
   |
   v
Application Load Balancer
   |
   v
Private Application Subnet
   |
   v
EC2 Application Server
   |
   v
Private Database Subnet
   |
   v
MySQL Database

## NAT Gateway

A NAT Gateway is planned as an optional component for the private application subnet.

It allows private EC2 instances to access the internet for required outbound connections without allowing direct inbound internet access.

### NAT Gateway Flow

Private EC2
   |
   v
Private Route Table
   |
   v
NAT Gateway
   |
   v
Internet Gateway
   |
   v
Internet

### Cost Consideration

The NAT Gateway is documented as part of the production architecture.

It is not deployed in AWS for this project because NAT Gateway usage incurs AWS charges.

The current project is being developed with a ₹0 AWS cost goal.


## Amazon RDS MySQL

Amazon RDS for MySQL is planned as the database layer of the application.

The database will run in the Private Database Subnet and will not be directly accessible from the internet.

### RDS Configuration

- Engine: MySQL
- Port: 3306
- Deployment: Multi-AZ (planned)
- Automated Backups: Enabled (planned)
- Database: UserDB
- Access: Only from the application layer

### Database Traffic Flow

EC2 Application Server
   |
   | MySQL 3306
   v
RDS MySQL
   |
   v
Employee Table

### Security

The RDS database security group allows MySQL traffic only from the application server security group.

Direct internet access to the database is not allowed.

### Current Status

RDS is documented as the planned AWS database layer.

For the current ₹0 project, the application uses a local MariaDB/MySQL database through XAMPP instead of deploying an AWS RDS instance.

## Application Load Balancer

An Application Load Balancer (ALB) is planned as the entry point for the application traffic.

The ALB will be deployed in the Public Subnet and will distribute incoming requests to EC2 application servers in the Private Application Subnet.

### ALB Configuration

- Type: Application Load Balancer
- Scheme: Internet-facing
- Listener: HTTP 80
- HTTPS 443: Planned
- Target: EC2 Application Servers
- Health Check: Application health endpoint

### Traffic Flow

Internet
   |
   v
Application Load Balancer
   |
   +--------> EC2 Application Server 1
   |
   +--------> EC2 Application Server 2

### Security

The ALB accepts HTTP/HTTPS traffic from the internet.

EC2 application servers accept traffic only from the ALB security group.

Direct internet access to the EC2 servers is restricted.

### High Availability

Multiple EC2 instances can be placed across different Availability Zones.

The ALB distributes traffic between healthy application instances.

### Current Status

The ALB is documented as part of the planned AWS architecture.

It has not been deployed because the project is being developed with a ₹0 AWS cost goal.

## Auto Scaling Group

An Auto Scaling Group (ASG) is planned to maintain the availability of the application servers.

The ASG will manage multiple EC2 instances running the Java Spring Boot application.

### Auto Scaling Configuration

- Minimum Instances: 2
- Desired Instances: 2
- Maximum Instances: 4
- Availability Zones: Multiple AZs
- Load Balancer: Application Load Balancer
- Health Checks: Enabled

### Scaling Flow

              Application Load Balancer
                       |
              +--------+--------+
              |                 |
              v                 v
          EC2 Instance 1    EC2 Instance 2
              |
        Auto Scaling Group
              |
        +-----+-----+
        |           |
        v           v
    New Instance   Remove Instance

### High Availability

Multiple EC2 instances across Availability Zones help maintain application availability if one instance becomes unavailable.

The ALB distributes incoming traffic only to healthy instances.

### Scaling

The ASG can launch additional EC2 instances when application demand increases and terminate unnecessary instances when demand decreases.

### Current Status

Auto Scaling is documented as part of the planned AWS architecture.

It has not been deployed because the project is being developed with a ₹0 AWS cost goal.

## CloudWatch Monitoring

Amazon CloudWatch is planned for monitoring the AWS infrastructure and application.

### Monitoring

CloudWatch will monitor:

- EC2 CPU utilization
- EC2 instance health
- Application Load Balancer metrics
- RDS CPU utilization
- RDS storage usage
- RDS database connections
- Application logs

### Alerts

CloudWatch alarms can be configured for important metrics.

Examples:

- High EC2 CPU utilization
- Low available RDS storage
- Unhealthy EC2 instances
- High ALB error rates

### Monitoring Flow

AWS Resources
   |
   v
Amazon CloudWatch
   |
   v
Metrics + Logs
   |
   v
CloudWatch Alarms
   |
   v
Notifications

### Current Status

CloudWatch monitoring is documented as part of the planned AWS architecture.

It has not been deployed because the project is being developed with a ₹0 AWS cost goal.


## IAM Roles and Permissions

AWS Identity and Access Management (IAM) is planned to control access to AWS resources.

### IAM Security Design

- IAM users are used for controlled AWS console access.
- IAM roles are used by AWS services where required.
- Permissions follow the principle of least privilege.
- Root account access is not used for daily AWS operations.

### EC2 IAM Role

The EC2 application server can use an IAM role to access required AWS services without storing AWS access keys on the server.

Example permissions may include:

- CloudWatch monitoring
- CloudWatch Logs
- Required AWS service access only

### Security

IAM policies will provide only the permissions required for each task.

Access keys should not be hard-coded in application source code or stored in GitHub.

### Current Status

IAM access control is documented as part of the planned AWS architecture.

No AWS IAM resources are being created because the project is being developed with a ₹0 AWS cost goal.
