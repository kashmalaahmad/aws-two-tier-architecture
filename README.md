# AWS Two-Tier Web Architecture: Secure WordPress Deployment

## 📌 Project Overview
This project demonstrates the design and deployment of a highly secure, Two-Tier Web Application Architecture on Amazon Web Services (AWS). It hosts a live WordPress content management system (CMS) where the frontend web server is publicly accessible, but the backend database is completely isolated in a private subnet, shielded from the public internet. 

This architecture ensures high security, scalability, and aligns with AWS Well-Architected Framework best practices.

##  Architecture Diagram
<img width="698" height="365" alt="image" src="https://github.com/user-attachments/assets/79ad1f87-1f05-4f17-a65c-69dfb384e81f" />


### The Two Tiers:
1. **Frontend Tier (Public):** An Amazon EC2 instance running a LAMP stack (Linux, Apache, PHP). It sits in a Public Subnet attached to an Internet Gateway, allowing it to serve web traffic.
2. **Backend Tier (Private):** An Amazon RDS MySQL instance residing in a Private Subnet. It has no public IP and is inaccessible from the internet. It only accepts traffic originating explicitly from the Frontend Tier.

## 🛠️ Technology Stack
* **Cloud Provider:** Amazon Web Services (AWS)
* **Networking:** Amazon VPC, Internet Gateway, Public/Private Subnets, Route Tables, Security Groups
* **Compute:** Amazon EC2 (Amazon Linux 2023)
* **Database:** Amazon RDS (MySQL)
* **Software Stack:** LAMP (Apache, MariaDB/MySQL Client, PHP 8.2), WordPress
* **Security:** SELinux Policies, strict Security Group ingress/egress rules

## 🚀 Deployment Steps

### Task 1: Network Infrastructure
* Created a custom Virtual Private Cloud (VPC).
* Configured a **Public Subnet** with a Route Table pointing to an Internet Gateway (IGW) for public access.
* Configured **Private Subnets** across two Availability Zones (AZs) with isolated Route Tables.
* Configured **Security Groups**:
  * `Public-Web-SG`: Allows inbound HTTP (80) and SSH (22) from anywhere.
  * `Private-DB-SG`: Allows inbound MySQL (3306) **only** from the `Public-Web-SG`.

### Task 2: Frontend Web Server
* Provisioned an EC2 instance in the Public Subnet.
* Connected via SSH to install Apache and PHP.
* Downloaded and extracted the latest WordPress core files into the `/var/www/html` directory.

### Task 3: Backend Database
* Created a **DB Subnet Group** utilizing the two private subnets to satisfy AWS Multi-AZ failover requirements.
* Provisioned an RDS MySQL database instance attached to the private subnet group.
* Disabled Public Access and attached the `Private-DB-SG` firewall.

### Task 4: Application Configuration & Connection
* Configured the `wp-config.php` file on the EC2 instance to route database queries to the private RDS Endpoint URL.
* **Security Configuration:** Adjusted SELinux boolean policies (`httpd_can_network_connect_db`) on the Amazon Linux server to permit Apache to make outbound network connections to the remote RDS instance.
* Executed the WordPress installation script via the public browser.

## 💡 Key Technical Challenges Overcome
* **VPC Routing & Subnet Isolation:** Diagnosed and corrected route table association errors to ensure the private subnet was completely cut off from the IGW.
* **Database Subnet Group Requirements:** Managed AWS AZ requirements by provisioning a secondary private subnet to allow for future Multi-AZ High Availability deployments.
* **SELinux Enforcement:** Troubleshot an Internal Server Error (HTTP 500) caused by strict SELinux defaults. Successfully implemented the `setsebool` command to allow the web server to communicate across the VPC to the database tier.

## 👨‍💻 Author
 *Kashmala Ahmad
* www.linkedin.com/in/kashmala-ahmad
