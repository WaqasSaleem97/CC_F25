# ⚡ Project 6 – High-Performance Web Hosting Platform

**Duration:** 8–10 hours  
**Total Marks:** 100  
**Submission Format:** GitHub Repository + PDF Report  

---

## 📋 Project Overview

Build a **production-ready, high-performance web hosting platform** on AWS using **Terraform** and **Ansible**, using only tools and topics covered in this course (**Linux + Git/GitHub + AWS IAM/VPC/EC2 + Terraform + Ansible + Nginx + PHP-FPM**) and allowed borderline items (**NAT Gateway** where needed).

You will:

- Use **Terraform** to provision:
  - VPC and networking components (public/private subnets across 2 AZs).
  - A **high-availability web tier** using **Nginx load balancing on EC2** (instead of AWS ALB).
  - Multiple web servers (EC2) running **Nginx + PHP-FPM**.
  - A **shared-content approach** that does **not** require NFS:
    - Website content is deployed using Ansible to each web server (kept consistent across instances).
- Use **Ansible** to:
  - Install and configure a **web stack**:
    - **Nginx** (web server + load balancer).
    - **PHP-FPM** (PHP runtime).
  - Configure **Nginx virtual hosts (server blocks)** to host **multiple customer websites**.
  - Apply **performance tuning** for Nginx and PHP-FPM (workers, buffers, keepalive, connection limits).
  - Configure **SSL/TLS** using **self-signed certificates** for all hosted sites.
  - Provide **deployment automation** for adding new websites automatically.
  - Implement **monitoring scripts** (bash) to check website uptime and basic response time.

You will document:

- Platform architecture.
- Customer onboarding procedure for adding new websites.

**Key Concepts (Course Scope):** Multi-tenancy (via server blocks), Nginx virtual hosts, Nginx load balancing, backup server, SSL termination, caching, PHP-FPM, bash scripting, Git workflow, Terraform modules, Ansible roles

---

## 🎯 Learning Objectives

By completing this project, you will:

- Design a **multi-tenant web hosting platform** using AWS primitives (IAM/VPC/EC2) and Nginx features.
- Use **Terraform** to define and provision:
  - VPC, subnets, Internet Gateway, route tables (and NAT Gateway if needed).
  - EC2 instances for:
    - Nginx load balancer tier (2 instances across AZs recommended)
    - Web server tier (2+ instances across AZs)
- Use **Ansible** to:
  - Install and configure Nginx and PHP-FPM.
  - Host multiple websites using **Nginx server blocks**.
  - Apply performance tuning for Nginx and PHP-FPM.
  - Configure SSL/TLS with self-signed certificates.
  - Automate onboarding for new customer sites.
  - Implement monitoring scripts for uptime/latency checks.

---

## 🧱 Architecture Overview

### High-Level Architecture (Text)

```markdown
┌───────────────────────────────────────────────────────────────┐
│                        Customers / Users                      │
└───────────────────────────────┬───────────────────────────────┘
                                │
                                │ HTTP / HTTPS requests
                                ▼
 ┌───────────────────────────────────────────────────────────────┐
 │                         AWS VPC                               │
 │                                                               │
 │  Public Subnets (Edge Tier)                                  │
 │  ┌───────────────────────────────┐                            │
 │  │ Nginx Load Balancers (EC2)    │                            │
 │  │ - Reverse proxy + LB          │                            │
 │  │ - SSL termination             │                            │
 │  │ - Caching                     │                            │
 │  └───────────────┬──────────────┘                            │
 │                  │                                            │
 │                  ▼                                            │
 │  Private Subnets (Web Tier)                                   │
 │  ┌───────────────────────────────┐                            │
 │  │ Web Servers (EC2)             │                            │
 │  │ - Nginx + PHP-FPM             │                            │
 │  │ - Multiple server blocks      │                            │
 │  │   for customer sites          │                            │
 │  └───────────────────────────────┘                            │
 │                                                               │
 │  Content Deployment Strategy                                  │
 │  - Ansible deploys websites to all web servers                │
 │  - Same content on each web server (no NFS)                   │
 │                                                               │
 └───────────────────────────────────────────────────────────────┘
```

---

## 📝 Requirements

### Part 1: Git Repository Setup (15 marks)

#### 1.1 Repository Structure (5 marks)

Use Project1 as a template and adapt for this hosting platform:

```text
Project6/
├── README.md
├── .gitignore
├── terraform/
│   ├── main.tf
│   ├── variables.tf
│   ├── outputs.tf
│   ├── backend.tf
│   ├── terraform.tfvars.example
│   ├── environments/
│   │   ├── test.tfvars
│   │   ├── staging.tfvars
│   │   └── production.tfvars
│   └── modules/
│       ├── network/
│       │   ├── main.tf
│       │   ├── variables.tf
│       │   └── outputs.tf
│       ├── lb-ec2/
│       │   ├── main.tf
│       │   ├── variables.tf
│       │   └── outputs.tf
│       └── web-ec2/
│           ├── main.tf
│           ├── variables.tf
│           └── outputs.tf
├── ansible/
│   ├── ansible.cfg
│   ├── inventory/
│   │   ├── test_aws_ec2.yml
│   │   ├── staging_aws_ec2.yml
│   │   └── production_aws_ec2.yml
│   ├── playbooks/
│   │   ├── provision-web-stack.yml
│   │   ├── deploy-websites.yml
│   │   ├── add-website.yml
│   │   └── monitoring-checks.yml
│   └── roles/
│       ├── web-stack/
│       │   ├── tasks/main.yml
│       │   ├── templates/nginx.conf.j2
│       │   ├── templates/php-fpm.conf.j2
│       │   └── vars/main.yml
│       ├── lb-nginx/
│       │   ├── tasks/main.yml
│       │   └── templates/lb-nginx.conf.j2
│       ├── websites/
│       │   └── tasks/main.yml
│       └── monitoring/
│           ├── tasks/main.yml
│           └── files/check-site.sh
├── websites/
│   ├── customer1/
│   │   └── index.php
│   ├── customer2/
│   │   └── index.php
│   └── default/
│       └── index.php
└── docs/
    ├── architecture.md
    ├── performance-tuning.md
    ├── ssl-strategy.md
    ├── monitoring.md
    └── customer-onboarding.md
```

**Tasks:**
- [ ] Create all directories and files.  
- [ ] Initialize Git and create the initial commit.  

**Deliverables:**
- Screenshot: `project6_part1_repository_structure.png`  
- Screenshot: `project6_part1_initial_commit.png`  

---

#### 1.2 Git Branching Strategy (5 marks)

Follow a similar branching model to Project1:

```text
main (Production platform)
├── staging (Pre-prod)
│   └── test (Internal testing)
└── feature/* (New features / improvements)
```

**Rules:**
- `feature/*` → merge into `test` via PR and review.  
- `test` → deploys to **test environment**.  
- `staging` → deploys to **staging environment** after successful testing.  
- `main` → deploys to **production** after approvals.  

**Tasks:**
- [ ] Create `test` and `staging` branches from `main`.  
- [ ] Document branching strategy in `README.md`.  

**Deliverables:**
- Screenshot: `project6_part1_git_branches.png`  

---

#### 1.3 `.gitignore` Configuration (5 marks)

Use a similar `.gitignore` as Project1:

```gitignore
# Terraform
**/.terraform/*
*.tfstate
*.tfstate.*
*.tfvars
!*.tfvars.example
crash.log
crash.*.log
.terraformrc
terraform.rc

# Ansible
*.retry
*.secret

# AWS credentials
.aws/
*.pem
*.key

# IDE
.vscode/
.idea/
*.swp
*.swo
*~

# OS
.DS_Store
Thumbs.db

# Logs
*.log
logs/

# Environment files
.env
.env.local

# Temp
tmp/
temp/
```

**Tasks:**
- [ ] Add `.gitignore`.  
- [ ] Confirm no state, secrets, or credentials are tracked.  

**Deliverables:**
- Screenshot: `project6_part1_gitignore_content.png`  
- Screenshot: `project6_part1_git_status_clean.png`  

---

### Part 2: Terraform Infrastructure (35 marks)

#### 2.1 Network Module – VPC & Subnets (12 marks)

**Module:** `terraform/modules/network/`

Requirements:

- VPC CIDR (e.g., `10.0.0.0/16`).  
- Public subnets (for LB EC2 instances) across 2 AZs.  
- Private subnets (for web EC2 instances) across 2 AZs.  
- Internet Gateway + public route table associations.  
- **NAT Gateway (borderline allowed)** if you need package updates for private instances.

**Outputs:**
- VPC ID  
- Public subnet IDs  
- Private subnet IDs  

**Deliverables:**
- Screenshot: `project6_part2_network_main.png`  
- Screenshot: `project6_part2_network_outputs.png`  

---

#### 2.2 LB EC2 Module – Nginx Load Balancers (11 marks)

**Module:** `terraform/modules/lb-ec2/`

Requirements:

- Provision **2 EC2 instances** as load balancers:
  - `lb-1` in public subnet AZ-a
  - `lb-2` in public subnet AZ-b
- Security Group:
  - Allow 80/443 from the internet
  - Allow SSH from your admin IP only
- Tags:
  - `Role=lb`, `Environment`, `Project`

**Outputs:**
- LB public IPs / DNS  
- LB private IPs (for internal troubleshooting)

**Deliverables:**
- Screenshot: `project6_part2_lb_ec2_main.png`  
- Screenshot: `project6_part2_lb_ec2_outputs.png`  

---

#### 2.3 Web EC2 Module – Web Servers (12 marks)

**Module:** `terraform/modules/web-ec2/`

Requirements:

- Provision **2+ EC2 instances** as web servers in private subnets across AZs:
  - `web-1` in private subnet AZ-a
  - `web-2` in private subnet AZ-b
- Security Group:
  - Allow 80 from LB SG only
  - Allow SSH from your admin IP only (or from LB if you choose and document)
- Tags:
  - `Role=web`, `Environment`, `Project`

**Outputs:**
- Web instance IDs  
- Web private IPs  

**Deliverables:**
- Screenshot: `project6_part2_web_ec2_main.png`  
- Screenshot: `project6_part2_web_ec2_outputs.png`  

---

### Part 3: Environment Configuration (15 marks)

#### 3.1 Root Terraform Configuration (8 marks)

**Files:** `terraform/main.tf`, `variables.tf`, `outputs.tf`, `backend.tf`

Requirements:

- Configure AWS provider.  
- Instantiate modules:
  - `network`, `lb-ec2`, `web-ec2`.  
- Export:
  - LB public IPs
  - Web private IPs (for Nginx upstream)
  - SSH helper commands (optional)

**Deliverables:**
- Screenshot: `project6_part3_main_tf.png`  
- Screenshot: `project6_part3_outputs_tf.png`  

---

#### 3.2 Environment-Specific Variables (7 marks)

**Files:**
- `terraform/environments/test.tfvars`  
- `terraform/environments/staging.tfvars`  
- `terraform/environments/production.tfvars`  
- `terraform/terraform.tfvars.example`  

Each environment config should define:

- `environment` (test, staging, production)
- instance counts and sizes
- allowed admin IP CIDR

**Deliverables:**
- Screenshot: `project6_part3_test_tfvars.png`  
- Screenshot: `project6_part3_staging_tfvars.png`  
- Screenshot: `project6_part3_production_tfvars.png`  
- Screenshot: `project6_part3_tfvars_example.png`  

---

### Part 4: Ansible Web Stack & Websites (25 marks)

#### 4.1 Inventory & Configuration (5 marks)

Use **aws_ec2** dynamic inventory filtered by tags:

- `Role=lb` for load balancers
- `Role=web` for web servers

**Deliverables:**
- Screenshot: `project6_part4_ansible_inventory_graph.png`  
- Screenshot: `project6_part4_ansible_cfg.png`  

---

#### 4.2 Web Stack Role (Nginx + PHP-FPM + Performance Tuning) (10 marks)

**Role:** `roles/web-stack`

Tasks:

- Install Nginx + PHP-FPM.
- Configure Nginx performance settings:
  - `worker_processes auto;`
  - `worker_connections`
  - `keepalive_timeout`
  - sensible gzip settings (optional)
- Configure PHP-FPM tuning:
  - `pm` settings and limits
- Configure Nginx **server blocks** for:
  - `customer1.local`
  - `customer2.local`
  - `default.local`

**Deliverables:**
- Screenshot: `project6_part4_web_stack_role_main.png`  
- Screenshot: `project6_part4_nginx_server_blocks.png`  

---

#### 4.3 Load Balancer Nginx Role (Reverse Proxy + SSL + Cache + Backup) (5 marks)

**Role:** `roles/lb-nginx`

Tasks:

- Install Nginx and OpenSSL.
- Generate self-signed certificate.
- Configure:
  - HTTP → HTTPS redirect
  - upstream pool for web servers
  - add a **backup server** behavior:
    - `web-2` as backup (or add third web node as backup in prod)
  - enable proxy cache and add `X-Cache-Status` header

**Deliverables:**
- Screenshot: `project6_part4_lb_nginx_conf.png`  
- Screenshot: `project6_part4_lb_ssl_certs.png`  

---

#### 4.4 Website Deployment & Automated Onboarding (5 marks)

Playbooks:

- `deploy-websites.yml` deploy initial websites to all web servers.
- `add-website.yml` adds a new website given variables:
  - `site_name`
  - `server_name`
  - `doc_root`
  - `enable_ssl` (true/false)

**Deliverables:**
- Screenshot: `project6_part4_add_website_playbook.png`  
- Screenshot: `project6_part4_new_site_added.png`  

---

### Part 5: Monitoring & Customer Onboarding Documentation (10 marks)

#### 5.1 Monitoring Scripts (5 marks)

**Role:** `roles/monitoring`

Deploy `check-site.sh`:

- Checks HTTP status code for each site.
- Measures simple response time using `curl -w`.
- Logs results to `/var/log/site_checks.log`.
- Runs via cron every 5 minutes.

**Deliverables:**
- Screenshot: `project6_part5_monitoring_script.png`  
- Screenshot: `project6_part5_monitoring_log_output.png`  

---

#### 5.2 Customer Onboarding Procedures (5 marks)

In `docs/customer-onboarding.md`, document:

- Inputs needed to add a site.
- Running `ansible-playbook add-website.yml -e "server_name=..."`.
- How to verify via LB.
- Directory and SSL naming conventions.

**Deliverables:**
- Screenshot: `project6_part5_customer_onboarding_doc.png`  

---

## 📦 Submission Guidelines

### 1. GitHub Repository

Example:

```text
CC_<YourName>_<YourRollNumber>/Project-6-High-Perf-Web-Hosting
```

- ✅ Commit Terraform + Ansible + scripts + docs.
- ❌ Do not commit state files, secrets, credentials, `.pem` keys.

---

### 2. PDF Report

Name:

```text
Project6_<YourName>_<YourRollNumber>.pdf
```

Include:

- Architecture diagram
- Terraform module explanations
- Nginx + PHP-FPM config highlights
- Performance tuning notes
- Monitoring output evidence
- Onboarding steps

---

## 📊 Grading Criteria (High Level)

### Excellent (90–100%)
- Multi-AZ deployment, working Nginx LB layer, stable multi-site hosting.
- SSL + caching implemented correctly on LB.
- PHP-FPM configured and working for PHP sites.
- Automated onboarding and monitoring scripts work.
- Strong documentation and testing evidence.

### Good (80–89%)
- Core platform works; minor missing tuning or documentation gaps.

### Satisfactory (70–79%)
- Platform works but limited automation/tuning.

### Needs Improvement (60–69%)
- Partial implementation or incomplete end-to-end flow.

### Unsatisfactory (<60%)
- Major components missing; unstable or not deployable.

---

## 💡 Tips for Success

- Start with 1 LB + 1 web server first, then scale out.
- Keep server blocks templated and data-driven.
- Test SSL and caching early.
- Make roles idempotent and rerunnable.

---

## ❓ FAQ

**Q1: Can I use AWS ALB/ASG instead of Nginx load balancer on EC2?**  
A: No. This project must remain within the course scope. Use **Nginx load balancing on EC2** (Week 14).

**Q2: Do I need NFS/shared storage for websites?**  
A: No. NFS is not required. This project uses **Ansible deployment** to keep website content consistent across web servers.

**Q3: Do I have to configure HTTPS for every customer site?**  
A: Yes. Use **self-signed certificates** (SSL termination on Nginx) and document how to test/accept them.

**Q4: How do I test that caching is working?**  
A: Add a response header like `X-Cache-Status` and show `HIT/MISS` behavior with repeated `curl` requests.

**Q5: Can I use MySQL as part of this project?**  
A: No. Database administration is not part of the course outline for this project. Keep the platform focused on **Nginx + PHP-FPM** web hosting.

**Q6: Can I add NAT Gateway?**  
A: Yes (borderline). If your private web servers need outbound access for package installation/updates, you may include a NAT Gateway and document why.

**Q7: How many websites must I host?**  
A: At minimum, host **two customer sites** (`customer1`, `customer2`) plus a default site, each implemented as an Nginx server block.

---

## ✅ Final Checklist

- [ ] Terraform provisions VPC + EC2 tiers.
- [ ] LB Nginx reverse proxy works.
- [ ] Web servers run Nginx + PHP-FPM and serve multiple sites.
- [ ] SSL self-signed certs configured.
- [ ] Cache header shows HIT/MISS.
- [ ] Monitoring script runs via cron and logs results.
- [ ] Customer onboarding doc is complete.
