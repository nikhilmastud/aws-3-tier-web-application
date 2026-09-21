# AWS 3-Tier Web Application

A Java Spring Boot Login Web Application designed around a 3-tier cloud architecture.

## Project Overview

This project demonstrates how a Java web application can be structured using separate presentation, application, and database layers.

The application is developed and tested locally, while the AWS architecture is documented for learning and cloud deployment preparation.

## Architecture

```text
Users
   ↓
Application Load Balancer
   ↓
EC2 – Java Spring Boot Application
   ↓
MySQL Database
```

## AWS Architecture

- VPC
- Public Subnet
- Private Application Subnet
- Private Database Subnet
- Application Load Balancer
- Amazon EC2
- Amazon RDS MySQL
- Auto Scaling
- CloudWatch
- IAM

## Application

The application provides:

- User registration
- User login
- MySQL database integration
- Spring Boot web application
- JSP-based web pages

## Technologies Used

- Java 11
- Spring Boot
- Spring MVC
- Spring Security
- JSP
- Maven
- MySQL / MariaDB
- Git & GitHub
- AWS Cloud Architecture

## Local Database

Database:

`UserDB`

Table:

`Employee`

The local application is connected to a MariaDB/MySQL database using XAMPP.

## Project Structure

```text
Java-Login-App/
├── src/
├── architecture/
│   └── README.md
├── pom.xml
├── README.md
└── .gitignore
```

## Current Status

✅ Java application running locally  
✅ User registration working  
✅ MySQL/MariaDB database connected  
✅ Login functionality tested  
✅ AWS 3-tier architecture documented  
✅ Project uploaded to GitHub  

## AWS Deployment Status

AWS services are documented as part of the planned architecture.

The application is currently tested locally to keep the project development cost at ₹0.

## Future Improvements

- Deploy application on EC2
- Use Amazon RDS MySQL
- Add Application Load Balancer
- Configure Auto Scaling
- Add CloudWatch monitoring
- Improve authentication and password security

## Learning Objective

The main objective of this project is to understand how a Java web application can be designed and prepared for deployment using AWS cloud architecture.