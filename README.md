Final-capstone-project
🌍 Capstone Project: AWS Multi-Tier App Deployment (Multi-Region, EKS, RDS, Route 53, CI/CD)

 📖 Table of Contents
1. [Overview](#1-overview)  
2. [Application Details](#2-application-details)  
3. [Infrastructure Strategy](#3-infrastructure-strategy)  
4. [Architecture Diagram](#4-architecture-diagram)  
5. [CloudFormation Setup – Region A](#5-cloudformation-setup--region-a)  
6. [Terraform Setup – Region B](#6-terraform-setup--region-b)  
7. [DNS and Global Routing](#7-dns-and-global-routing)  
8. [CI/CD Workflow](#8-cicd-workflow)  
9. [Monitoring & Notifications](#9-monitoring--notifications)  
10. [Security Measures](#10-security-measures)  
11. [Next Steps & Enhancements](#11-next-steps--enhancements)  
12. [Maintainer](#12-maintainer)

---

 1. Overview
This project demonstrates how to architect and deploy a production-ready, fault-tolerant multi-tier web application using **Amazon Web Services (AWS)** across **two regions**. It uses a mix of **CloudFormation and Terraform** for IaC, **Amazon EKS** for container orchestration, and **Amazon RDS MySQL** for persistent storage. **Route 53** ensures seamless routing and failover.

---
**
 2. Application Details
 
- **Repository**: [Capstone-Final-hv2](https://github.com/karthikeya964/Capstone-Final-hv2)
- **Application Framework**: Spring Boot + Thymeleaf
- **Database**: MySQL (via RDS)
- **Provisioning Tools**:
  - Region A: CloudFormation
  - Region B: Terraform

---

 3. Infrastructure Strategy

| Objective         | Approach                                                            |
|-------------------|---------------------------------------------------------------------|
| High Availability | Multi-AZ subnets, Multi-Region redundancy using Route 53           |
| Fault Tolerance   | Separate EKS clusters, health-checked ALBs                         |
| Elasticity        | EKS-managed node auto-scaling                                       |
| Disaster Recovery | Active/Passive regions with automated DNS failover                 |

---

4. Architecture Diagram

                     🌐 Internet Users
                            |
                       [ AWS Route 53 ]
                      /                 \
🔹 Region A (us-east-1)               🔹 Region B (us-west-2)
------------------------             -------------------------
[ Application Load Balancer ]      [ Application Load Balancer ]
          |                                      |
      [ EKS Cluster ]                        [ EKS Cluster ]
          |                                      |
   [ Spring Boot App ]                    [ Spring Boot App ]
          |                                      |
     [ RDS MySQL ]                          [ RDS MySQL ]


---

 5. CloudFormation Setup – Region A (Primary)

 📁 Stack Components:
- Networking: VPC, Subnets, IGW, NAT Gateway, Route Tables
- Compute: Amazon EKS and Managed Node Groups
- Database: Amazon RDS (MySQL), Multi-AZ setup
- Monitoring: CloudWatch metrics + alarms
- Permissions: IAM roles for all relevant services

 🚀 How to Deploy:
- Use AWS CloudFormation console or AWS CLI to deploy the `vpc-eks-rds.yaml`
- Alternatively, use AWS CodePipeline to automate deployment from GitHub

---
 6. Terraform Setup – Region B (Secondary)
 🛠 Resources:
- `provider` block to configure region
- Reusable modules for VPC and EKS
- `aws_db_instance` resource for RDS deployment

 Deployment Commands:
cd region-2-terraform
terraform init
terraform apply

7. DNS and Global Routing
🌐 Failover Mechanism (Route 53):
Record Type	Region	Routing Policy	Health Check
A (Alias)	us-east-1	Primary	Enabled
A (Alias)	us-west-2	Secondary	Disabled

If the primary ALB fails health checks, traffic is routed automatically to the secondary ALB.

8. CI/CD Workflow
🧰 Toolchain:
Service	Functionality
CodePipeline	Pipeline automation
CodeBuild	Build and deployment to EKS
ECR (optional)	Container image registry
CodeDeploy	(Optional) Rolling or Blue-Green deployments
S3	Stores build artifacts (optional)

🧪 Sample buildspec.yml
version: 0.2
phases:
  install:
    runtime-versions:
      java: corretto11
  build:
    commands:
      - mvn clean package
      - kubectl apply -f deployment.yaml
artifacts:
  files: ["**/*"]
  
9. Monitoring & Notifications
📡 Key Alerts:
Metric	Condition	Action
RDS CPU Usage	>70%	Email via SNS
EKS Pod CPU Utilization	>80%	Email via SNS
Use Amazon SNS Topics for notifying key stakeholders of alarm triggers.

10. Security Measures
🔐 RDS Private Access: Not exposed to the public internet
🔒 IAM Roles: Scoped with least privilege
🧾 Secrets: Stored securely using AWS SSM or Secrets Manager
🚫 Network Access Control: Only whitelisted traffic allowed via SGs and NACLs

11. Next Steps & Enhancements
Enable WAF and Shield for ALB
Integrate Prometheus + Grafana for observability
Extend pipeline with automated tests and canary deployments
Add cost optimization monitoring (e.g., via AWS Cost Explorer)

12. Maintainer
👨‍💻 D. Karthikeya
GitHub: @karthikeya964
Project: Capstone Final HV2 - AWS Multi-Region App

📌 Common Commands
aws eks update-kubeconfig --region <region> --name <cluster>
kubectl get pods --all-namespaces
kubectl get svc
kubectl logs -l app=<your-app-name>


