# ☁️ Amazon Web Services (AWS) Cloud Engineering Showcase

<div align="center">

![AWS](https://img.shields.io/badge/Amazon_AWS-FF9900?style=for-the-badge&logo=amazonaws&logoColor=white)
![EC2](https://img.shields.io/badge/Amazon_EC2-FF9900?style=for-the-badge&logo=amazonec2&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white)
![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white)
![Linux](https://img.shields.io/badge/Linux-FCC624?style=for-the-badge&logo=linux&logoColor=black)
![Nginx](https://img.shields.io/badge/NGINX-009639?style=for-the-badge&logo=nginx&logoColor=white)

**A centralized hub for production-ready AWS architectures, Infrastructure as Code (IaC), and real-world cloud solutions — engineered for high availability, fault tolerance, and infrastructure automation.**

[![Repo Status](https://img.shields.io/badge/Status-Active-brightgreen?style=flat-square)](https://github.com/danish-ali)
[![Projects](https://img.shields.io/badge/Projects-4-blue?style=flat-square)](#-repository-structure)
[![Notes](https://img.shields.io/badge/Notes-10-orange?style=flat-square)](./Notes)

</div>

---

## 📌 About Amazon Web Services (AWS)

Amazon Web Services (AWS) is the world's most comprehensive and broadly adopted cloud platform, offering over 200 fully featured services from data centers globally. It forms the backbone of modern DevOps pipelines — enabling teams to build, deploy, and scale applications at any speed without managing physical infrastructure.

Key pillars that make AWS the industry standard:

- **🌍 Global Infrastructure** — 33+ geographic regions, 105+ Availability Zones, and a worldwide edge network for ultra-low latency delivery.
- **⚡ Elasticity & Scalability** — On-demand resource provisioning lets infrastructure grow and shrink in real time with traffic patterns, eliminating over-provisioning.
- **🔒 Security by Design** — A shared responsibility model with granular IAM policies, VPC isolation, encryption at rest and in transit, and compliance across 140+ security standards.
- **🛡️ High Availability & Fault Tolerance** — Multi-AZ deployments, automated failover, and self-healing architectures ensure systems stay online even when individual components fail.
- **🔧 DevOps-Native Toolchain** — Services like CodePipeline, CodeBuild, CodeDeploy, and ECR integrate seamlessly into CI/CD workflows for fully automated software delivery.

---

## 🗂️ Core AWS Services & Architectures Explored

| Category | Services |
|:---|:---|
| **⚙️ Compute** | EC2, Lambda, Auto Scaling Groups (ASG), Launch Templates |
| **🌐 Networking** | VPC, Public & Private Subnets, Route Tables, Internet Gateway (IGW), NAT Gateway, Egress-Only IGW (EIGW) |
| **🔀 Traffic Management** | Application Load Balancer (ALB), Target Groups, Health Checks, Route 53 |
| **🗄️ Storage** | S3 (Static Assets, IAM Role-based access), EBS Volumes |
| **🐳 Containers & Registry** | Docker, Amazon ECR, CodeBuild Docker Integration |
| **🚀 CI/CD & Deployment** | AWS CodePipeline, CodeBuild, CodeDeploy, `buildspec.yml`, `appspec.yml` |
| **🔐 Identity & Security** | IAM Roles, IAM Policies, Security Groups, Least-Privilege Access, SSM Parameter Store |
| **📊 Observability** | CloudWatch Dashboards, Metrics, Alarms, Log Groups |
| **🛠️ Systems Management** | AWS Systems Manager (SSM), Session Manager (Bastion-free access) |

---

## 🏗️ Hands-on Cloud Implementations

Every project in this repository demonstrates a concrete, production-aligned architecture solving a real-world infrastructure challenge.

---

### 🔵 Project 1 — High-Availability Two-Tier Web Architecture

> **Objective:** Eliminate single points of failure by deploying a fault-tolerant, load-balanced web infrastructure across multiple Availability Zones.

- Designed a **custom VPC** with 4 subnets — 2 public (ALB tier) and 2 private (compute tier) — across `eu-north-1a` and `eu-north-1b`.
- Deployed an **Application Load Balancer (ALB)** with a Target Group and active health checks to distribute traffic via Round Robin.
- Configured an **Auto Scaling Group (ASG)** maintaining a minimum of 2 healthy EC2 instances at all times for self-healing behavior.
- Implemented **Security Group nesting**: web servers accept traffic *only* from the ALB Security Group ID, enforcing a zero-exposure private tier.
- Automated Nginx web server installation and dynamic HTML generation via EC2 **User Data scripts**.

📁 [`project-1/`](./project-1) | 📐 Architecture: `ALB → Target Group → Private EC2s (Multi-AZ)`

---

### 🟣 Project 2 — Dual-Stack Hybrid Networking (IPv4 + IPv6)

> **Objective:** Build a secure, egress-controlled network topology supporting both IPv4 and IPv6 without exposing private instances to inbound internet traffic.

- Architected a **Dual-Stack VPC** (`10.0.0.0/16` + Amazon-provided IPv6) with public and private subnet segregation.
- Configured a **NAT Gateway** for private-subnet IPv4 outbound traffic and an **Egress-Only Internet Gateway (EIGW)** for IPv6 — ensuring instances remain fully unreachable from the public internet.
- Eliminated SSH/port-22 exposure by configuring **AWS Systems Manager (SSM)** with IAM role-bound `AmazonSSMManagedInstanceCore` policy for secure terminal access.
- Verified dual-stack connectivity end-to-end: `ping6` to Google DNS (`2001:4860:4860::8888`) returned 0% packet loss via EIGW; `apt update` successfully routed through NAT Gateway.

📁 [`project-2/`](./project-2) | 📐 Architecture: `Private Subnet → NAT / EIGW → Internet (Outbound Only)`

---

### 🟢 Project 3 — Production-Grade Multi-AZ Scalable Infrastructure

> **Objective:** Deploy a resilient two-tier architecture with real-time CloudWatch observability and cross-AZ Auto Scaling for a zero-downtime production environment.

- Built `project-3-vpc` with a `/16` CIDR block, segmenting public subnets (ALB + Bastion Host) from private subnets (ASG instances).
- Configured **IAM Roles** on compute instances granting `AmazonS3ReadOnlyAccess` — assets fetched securely from S3 without storing any credentials on disk.
- Set up an **Auto Scaling Group** with a Launch Template, proving multi-AZ instance distribution with all 4 targets reporting **Healthy** in the ALB Target Group.
- Built a **CloudWatch Dashboard** to monitor real-time CPU, memory, and storage metrics across the fleet, validating the observability layer.

📁 [`project-3/`](./project-3) | 📐 Architecture: `ALB → ASG (Multi-AZ) → Private EC2s + CloudWatch`

---

### 🟠 Project 5 — Containerized CI/CD Pipeline (CodeBuild + CodeDeploy + Docker)

> **Objective:** Automate the full software delivery lifecycle — from source code to a running Docker container on EC2 — using a fully managed AWS CI/CD pipeline.

- Containerized a **Flask (Python 3.8)** web application using a multi-stage `Dockerfile`, exposing port `5000`.
- Wrote a **`buildspec.yml`** for AWS CodeBuild that installs dependencies, runs tests, authenticates to Docker Hub using secrets securely stored in **SSM Parameter Store**, builds the Docker image, and pushes it to the registry — all in a fully automated pipeline.
- Authored an **`appspec.yml`** for AWS CodeDeploy defining lifecycle event hooks (`ApplicationStop` → `AfterInstall`) that gracefully stop the old container and launch the new one on the target EC2 instance.
- Credentials are managed entirely via **SSM Parameter Store** (`/myapp/docker-credentials/*`) — zero hardcoded secrets in source code.

📁 [`project-5/`](./project-5) | 📐 Pipeline: `GitHub → CodePipeline → CodeBuild → Docker Hub → CodeDeploy → EC2`

---

## 📁 Repository Structure

```
Amazone_Web_Services/
│
├── 📂 Notes/                              # Structured learning notes & study references
│   ├── 📄 Introduction to cloud & aws.pdf
│   ├── 📄 EC2 Instance.pdf
│   ├── 📄 Identity and Access Management.pdf
│   ├── 📄 Virtual Private Cloud.pdf
│   ├── 📄 S3.pdf
│   ├── 📄 Route53_Domain_Management.pdf
│   ├── 📄 AWS Lambda.pdf
│   ├── 📄 AWS CloudWatch.pdf
│   ├── 📄 AWS CloudFormation Template.pdf
│   └── 📄 AWS CLI.pdf
│
├── 📂 project-1/                          # High-Availability Two-Tier Web Architecture
│   ├── 📄 README.md
│   ├── 🖼️  architecture-diagram.png
│   ├── 🖼️  server1.png
│   └── 🖼️  server2.png
│
├── 📂 project-2/                          # Dual-Stack Hybrid Networking (IPv4 + IPv6)
│   ├── 📄 README.md
│   ├── 🖼️  architecture-diagram.png
│   ├── 🖼️  vpc.png
│   └── 🖼️  ipv6tst-through-eigw.png
│
├── 📂 project-3/                          # Production-Grade Multi-AZ Scalable Infrastructure
│   ├── 📄 README.md
│   ├── 🖼️  architecture-diagram.png
│   ├── 🖼️  autoscalinggroup.png
│   ├── 🖼️  targetgroup.png
│   ├── 🖼️  cloudwatch.png
│   └── 🖼️  output.png
│
├── 📂 project-5/                          # Containerized CI/CD Pipeline (CodeBuild + Docker)
│   ├── 🐳 Dockerfile
│   ├── 🐍 app.py
│   ├── 📄 buildspec.yml
│   ├── 📄 appspec.yml
│   ├── 📄 requirements.txt
│   ├── 🔧 start_container.sh
│   └── 🔧 stop_container.sh
│
└── 📄 README.md                           # ← You are here
```

---

## 🔭 Future Enhancements

This repository evolves continuously. Upcoming additions planned on the roadmap:

- [ ] **🏗️ Terraform IaC** — Re-provision all existing architectures as reusable Terraform modules, enabling one-command environment creation and true infrastructure-as-code portability.
- [ ] **🔐 Advanced IAM Fine-Tuning** — Replace broad managed policies with custom least-privilege IAM policies scoped to exact resource ARNs, following the principle of minimal blast radius.
- [ ] **🌍 Multi-Region Disaster Recovery** — Implement active-passive DR across two AWS regions using Route 53 failover routing policies, S3 cross-region replication, and RDS read replicas.
- [ ] **📦 Amazon ECS / EKS** — Migrate the containerized Flask application from EC2-based CodeDeploy to a fully managed container orchestration platform using ECS Fargate or EKS.
- [ ] **🔍 Centralized Logging & Alerting** — Integrate CloudWatch Log Groups, metric filters, and SNS-based alerting for proactive anomaly detection and incident response.
- [ ] **🔒 AWS WAF & Shield** — Layer Web Application Firewall rules and DDoS protection on top of load balancers to harden public-facing infrastructure.
- [ ] **⚙️ AWS CloudFormation Templates** — Author CloudFormation stacks for repeatable, version-controlled environment provisioning alongside Terraform modules.

---

## 🤝 Connect With Me

I'm an aspiring Cloud & DevOps Engineer passionate about building secure, scalable, and automated cloud infrastructure. Open to collaborations, feedback, and new opportunities.

<div align="center">

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Danish_Ali-0077B5?style=for-the-badge&logo=linkedin&logoColor=white)](https://linkedin.com/in/your-linkedin-profile)
[![GitHub](https://img.shields.io/badge/GitHub-danish--ali-181717?style=for-the-badge&logo=github&logoColor=white)](https://github.com/your-github-username)

</div>

---

<div align="center">

*"Infrastructure is not just servers and cables — it's the backbone of every digital experience."*

⭐ **If you find this repository valuable, consider giving it a star!**

</div>
