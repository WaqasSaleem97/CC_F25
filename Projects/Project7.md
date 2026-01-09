# 🧪 Project 7 – Automated Development and Testing Environment (Within Course Scope)

**Duration:** 8–10 hours  
**Total Marks:** 100  
**Submission Format:** GitHub Repository + PDF Report  

---

## 📋 Project Overview

Deploy an **automated development and testing infrastructure** that can **provision complete environments on-demand** using **Terraform** and **Ansible**, using only tools and topics covered in this course:

- Linux administration + **bash scripting**
- Git and GitHub collaboration
- AWS: **IAM, VPC, EC2**
- Terraform: modules, state, outputs, workspaces
- Ansible: inventories, playbooks, roles, handlers, automation
- Web servers: **Nginx** (and **Apache/HTTPD** where required)
- **PHP-FPM** (as taught)

> **Borderline allowed concepts (only if needed and documented):**
> - **NAT Gateway** for private subnet outbound internet access (package installs/updates).
> - (Optional) **S3 remote backend** for Terraform state, if you taught it and want shared state for teams.

You will:

- Use **Terraform** to build reusable modules for dev/test environments:
  - EC2 instances
  - Networking (VPC, subnets, routing)
  - Security groups (least privilege)
- Use **Ansible** to configure development stacks:
  - Web servers:
    - **LEMP** (Linux, Nginx, MySQL is not used; instead PHP-FPM + Nginx for PHP site)
    - **LAMP** (Linux, Apache/HTTPD + PHP)
  - Development tools (Git, curl, editors, build tools)
  - Nginx reverse proxy and development-friendly features (autoindex)
- Implement **environment provisioning automation** using scripts:
  - Bash scripts to create and destroy environments on demand.
- Implement **automated testing environments** that “clone production-like configuration”:
  - Same OS, package versions, and web server configs as production, but isolated per environment.
- Use **Terraform workspaces** or separate state files for isolation:
  - `dev`, `qa`, `uat`
- Implement teardown automation for cost optimization.
- Implement environment health checks (scripts or Ansible tasks).
- Document environment request procedures and self-service scripts.

**Key Concepts (Course Scope):** Environment automation, Terraform modules/workspaces/state, Ansible roles, Nginx reverse proxy, Apache/HTTPD, PHP/PHP-FPM, Git workflows, cost optimization via teardown, health checks

<img width="1591" height="3343" alt="diagram-export-12-29-2025-2_47_45-AM" src="https://github.com/user-attachments/assets/8eee27f2-a611-442f-9bb2-8637bdae6df3" />

---

## 🎯 Learning Objectives

By completing this project, you will:

- Design and implement **on-demand development and testing environments**.
- Use **Terraform modules** to build reusable infrastructure patterns.
- Use **Terraform workspaces** (or isolated state) so environments do not conflict.
- Use **Ansible roles** to configure multiple web stacks:
  - LAMP (Linux + Apache/HTTPD + PHP)
  - Nginx + PHP-FPM stack
- Configure **Nginx** for developer-friendly features:
  - Directory listing (autoindex)
  - Reverse proxy to backend dev services (simple HTTP apps)
- Automate provisioning & teardown with bash scripts.
- Implement basic health checks to validate the environment.

---

## 🧱 Architecture Overview

### High-Level Architecture (Text)

```markdown
┌───────────────────────────────────────────────────────────────┐
│                Developers / QA Engineers / Testers            │
└───────────────────────────────┬───────────────────────────────┘
                                │
                                │ Self-service scripts / CLI
                                ▼
                   ┌──────────────────────────────┐
                   │   Git Repo + Scripts         │
                   │   - Terraform modules        │
                   │   - Ansible roles            │
                   │   - env-provision.sh         │
                   │   - env-destroy.sh           │
                   │   - env-health-check.sh      │
                   └───────────┬──────────────────┘
                               │
                               │ terraform apply / destroy
                               ▼
 ┌───────────────────────────────────────────────────────────────┐
 │                         AWS Account                           │
 │                (Dev / QA / UAT Environments)                  │
 │                                                               │
 │  VPC (per workspace/environment)                              │
 │  - Public subnet(s): Web entry + optional proxy               │
 │  - Private subnet(s): App instances                           │
 │  - SGs: least privilege                                       │
 │                                                               │
 │  EC2 Instances (tagged by Role/Stack)                         │
 │  - role_web_apache   (LAMP-like: Apache + PHP)                │
 │  - role_web_nginx    (Nginx + PHP-FPM)                        │
 │  - role_proxy_nginx  (Nginx reverse proxy + autoindex)        │
 │                                                               │
 │  Ansible                                                      │
 │  - installs web stacks + tools                                │
 │  - configures “prod-like” settings                            │
 │  - runs health checks                                         │
 │                                                               │
 └───────────────────────────────────────────────────────────────┘
```

You will refine this architecture in `docs/architecture.md` with more detail (subnets, SGs, tags, and traffic flow).

---

## 📝 Requirements

### Part 1: Git Repository & Structure (15 marks)

#### 1.1 Repository Structure (5 marks)

Use Project1 as inspiration and adapt for multi-stack dev/test environments:

```text
Project7/
├── README.md
├── .gitignore
├── scripts/
│   ├── env-provision.sh         # Provision new environment
│   ├── env-destroy.sh           # Destroy environment
│   └── env-health-check.sh      # Run basic health checks
├── terraform/
│   ├── main.tf
│   ├── variables.tf
│   ├── outputs.tf
│   ├── backend.tf               # local backend by default
│   ├── terraform.tfvars.example
│   ├── environments/
│   │   ├── dev.tfvars
│   │   ├── qa.tfvars
│   │   └── uat.tfvars
│   └── modules/
│       ├── network/             # VPC, subnets, IGW, routing, (optional NAT)
│       │   ├── main.tf
│       │   ├── variables.tf
│       │   └── outputs.tf
│       ├── security/            # Security Groups only (least privilege)
│       │   ├── main.tf
│       │   ├── variables.tf
│       │   └── outputs.tf
│       └── compute/             # EC2 instances per stack
│           ├── main.tf
│           ├── variables.tf
│           └── outputs.tf
├── ansible/
│   ├── ansible.cfg
│   ├── inventory/
│   │   ├── dev_aws_ec2.yml
│   │   ├── qa_aws_ec2.yml
│   │   └── uat_aws_ec2.yml
│   ├── playbooks/
│   │   ├── provision-environment.yml
│   │   ├── deploy-sites.yml
│   │   ├── health-check.yml
│   │   └── teardown-notes.md
│   └── roles/
│       ├── base-tools/          # Git, curl, editors, build tools
│       │   └── tasks/main.yml
│       ├── apache-web/          # Apache/HTTPD + PHP
│       │   ├── tasks/main.yml
│       │   └── templates/httpd.conf.j2
│       ├── nginx-phpfpm-web/    # Nginx + PHP-FPM
│       │   ├── tasks/main.yml
│       │   ├── templates/nginx.conf.j2
│       │   └── templates/php-fpm.conf.j2
│       ├── nginx-proxy-dev/     # Nginx reverse proxy + autoindex
│       │   ├── tasks/main.yml
│       │   └── templates/nginx-dev.conf.j2
│       └── sample-sites/        # Deploy simple sample sites
│           ├── tasks/main.yml
│           └── files/
│               ├── apache-index.php
│               └── nginx-index.php
└── docs/
    ├── architecture.md
    ├── environment-requests.md
    ├── environment-teardown.md
    └── testing-strategy.md
```

**Tasks:**
- [ ] Create folder structure and placeholder files.  
- [ ] Initialize Git and create the initial commit.

**Deliverables:**
- Screenshot: `project7_part1_repository_structure.png`  
- Screenshot: `project7_part1_initial_commit.png`  

---

#### 1.2 .gitignore & Basic Hygiene (5 marks)

Use the same `.gitignore` baseline as Project1:

```gitignore
# Terraform
**/.terraform/*
*.tfstate
*.tfstate.*
*.tfvars
!*.tfvars.example

# Ansible
*.retry
.vault_pass
*.secret

# AWS credentials
.aws/
*.pem
*.key

# IDE / OS / Logs / Temp
.vscode/
.idea/
*.swp
*.swo
*~
.DS_Store
Thumbs.db
*.log
logs/
.env
.env.local
*.backup
*.bak
tmp/
temp/
```

**Tasks:**
- [ ] Add `.gitignore`.
- [ ] Confirm no state or secrets are tracked.

**Deliverables:**
- Screenshot: `project7_part1_gitignore_content.png`  
- Screenshot: `project7_part1_git_status_clean.png`  

---

#### 1.3 Branching & Simple Workflow (5 marks)

A simple but effective branching strategy:

```text
main    (stable IaC for environments)
└── dev (active development of modules and roles)
    └── feature/* (short-lived)
```

**Tasks:**
- [ ] Create `dev` branch from `main`.
- [ ] Use PRs from `feature/*` → `dev` → `main`.
- [ ] Document workflow briefly in `README.md`.

**Deliverables:**
- Screenshot: `project7_part1_git_branches.png`  

---

### Part 2: Terraform Infrastructure Modules (30 marks)

#### 2.1 Network Module – VPC, Subnets, Routing (10 marks)

**Module:** `terraform/modules/network/`

Requirements:

- VPC CIDR (e.g., `10.0.0.0/16`).
- Public subnet(s) for web/proxy access.
- Private subnet(s) for app hosts.
- Internet Gateway and public route table.
- **Borderline (optional): NAT Gateway** if private hosts must install packages/updates.

**Outputs:**
- VPC ID, subnet IDs.

**Deliverables:**
- Screenshot: `project7_part2_network_main.png`  
- Screenshot: `project7_part2_network_outputs.png`  

---

#### 2.2 Security Module – Security Groups (10 marks)

**Module:** `terraform/modules/security/`

Requirements:

- Security groups for each role:
  - `sg_proxy`: allow 80/443 from allowed CIDR; SSH from your IP.
  - `sg_web`: allow 80 only from `sg_proxy`; SSH from your IP (or via proxy if you document).
- Use tags:
  - `Project`, `Environment`, `Role`, `Stack`

**Outputs:**
- SG IDs for compute module.

**Deliverables:**
- Screenshot: `project7_part2_security_main.png`  
- Screenshot: `project7_part2_security_outputs.png`  

---

#### 2.3 Compute Module – Environment Instances (10 marks)

**Module:** `terraform/modules/compute/`

Requirements:

Provision EC2 instances (counts can be variables):

- 1x **Nginx Proxy/Dev Gateway** (public subnet) tagged `Role=proxy`
- 1x **Apache Web** instance tagged `Role=web` and `Stack=apache`
- 1x **Nginx+PHP-FPM Web** instance tagged `Role=web` and `Stack=nginx-phpfpm`

All instances must be tagged with:
- `Project=Project7-Automated-DevTest`
- `Environment=dev|qa|uat`
- `Role=proxy|web`
- `Stack=apache|nginx-phpfpm|proxy`

**Outputs:**
- Public IP/DNS of proxy
- Private IPs of web instances

**Deliverables:**
- Screenshot: `project7_part2_compute_main.png`  
- Screenshot: `project7_part2_compute_outputs.png`  

---

### Part 3: State Isolation & Environments (15 marks)

#### 3.1 State Isolation (8 marks)

Choose one:

**Option A: Terraform workspaces**
- `terraform workspace new dev`
- `terraform workspace new qa`
- `terraform workspace new uat`

**Option B: Separate state files**
- Store state per environment and document how.

**Requirements:**
- Environments must not conflict.
- Document exact commands in `docs/environment-requests.md`.

**Deliverables:**
- Screenshot: `project7_part3_workspaces_list.png` (or equivalent)  
- Screenshot: `project7_part3_env_isolation_notes.png`  

---

#### 3.2 Environment-Specific Variables (7 marks)

**Files:**
- `terraform/environments/dev.tfvars`
- `terraform/environments/qa.tfvars`
- `terraform/environments/uat.tfvars`
- `terraform/terraform.tfvars.example`

Each should set:
- instance types/counts
- allowed admin IP
- environment name

**Deliverables:**
- Screenshot: `project7_part3_dev_tfvars.png`
- Screenshot: `project7_part3_qa_tfvars.png`
- Screenshot: `project7_part3_uat_tfvars.png`

---

### Part 4: Ansible Configuration – Web Stacks & Dev Proxy (30 marks)

#### 4.1 Base Tools & Inventory (6 marks)

**Role:** `roles/base-tools`
- Install: `git`, `curl`, `vim`, plus any needed utilities.

**Inventory:** Use `aws_ec2` plugin and filters by tags:
- `Environment=dev|qa|uat`
- `Role=proxy|web`
- `Stack=apache|nginx-phpfpm`

**Deliverables:**
- Screenshot: `project7_part4_ansible_cfg.png`
- Screenshot: `project7_part4_inventory_graph.png`

---

#### 4.2 Apache Web Stack (HTTPD + PHP) (8 marks)

**Role:** `roles/apache-web`

Requirements:
- Install **Apache/HTTPD** and PHP.
- Deploy a sample site (`apache-index.php`) that prints:
  - hostname
  - environment
  - timestamp
- Enable and start services.

**Deliverables:**
- Screenshot: `project7_part4_apache_role_main.png`
- Screenshot: `project7_part4_apache_site_working.png`

---

#### 4.3 Nginx + PHP-FPM Web Stack (8 marks)

**Role:** `roles/nginx-phpfpm-web`

Requirements:
- Install **Nginx** + **PHP-FPM**.
- Configure Nginx to serve PHP via PHP-FPM.
- Deploy sample site (`nginx-index.php`) that prints:
  - hostname
  - environment
  - timestamp
- Enable and start services.

**Deliverables:**
- Screenshot: `project7_part4_nginx_phpfpm_role_main.png`
- Screenshot: `project7_part4_nginx_php_site_working.png`

---

#### 4.4 Nginx Dev Gateway (Reverse Proxy + Autoindex) (8 marks)

**Role:** `roles/nginx-proxy-dev`

Requirements:
- Install Nginx on proxy host.
- Enable **autoindex** for a safe directory (e.g., `/var/www/dev-files/`) for development convenience.
- Configure reverse proxy routes:
  - `/apache/` → Apache web private IP
  - `/phpfpm/` → Nginx+PHP-FPM web private IP
- Add a `/health` endpoint on the proxy returning 200.

**Deliverables:**
- Screenshot: `project7_part4_nginx_dev_conf.png`
- Screenshot: `project7_part4_proxy_routes_working.png`

---

### Part 5: Automation Scripts, Health Checks & Teardown (10 marks)

#### 5.1 Provision Script (4 marks)

**Script:** `scripts/env-provision.sh`

Must:
- take environment name (dev/qa/uat)
- select/create workspace
- run `terraform init`, `plan`, `apply`
- run Ansible `provision-environment.yml`

**Deliverables:**
- Screenshot: `project7_part5_env_provision_script.png`
- Screenshot: `project7_part5_env_provision_run.png`

---

#### 5.2 Teardown Script (3 marks)

**Script:** `scripts/env-destroy.sh`

Must:
- select workspace
- destroy environment with `terraform destroy`

**Deliverables:**
- Screenshot: `project7_part5_env_destroy_script.png`
- Screenshot: `project7_part5_env_destroy_run.png`

---

#### 5.3 Health Checks (3 marks)

**Script:** `scripts/env-health-check.sh` OR `ansible/playbooks/health-check.yml`

Must validate:
- proxy `/health` returns 200
- proxy routes `/apache/` and `/phpfpm/` return content

**Deliverables:**
- Screenshot: `project7_part5_health_check_script.png`
- Screenshot: `project7_part5_health_check_output.png`

---

## 📦 Submission Guidelines

### 1. GitHub Repository

Example:

```text
CC_<YourName>_<YourRollNumber>/Project-7-Automated-Dev-Test-Env
```

- ✅ Include: Terraform code, Ansible roles/playbooks, scripts, docs, screenshots.
- ❌ Exclude: terraform state, `.terraform/`, real tfvars, credentials, `.pem` keys.

---

### 2. PDF Report

Name:

```text
Project7_<YourName>_<YourRollNumber>.pdf
```

Suggested structure:

1. Cover Page  
2. Table of Contents  
3. Executive Summary  
4. Architecture Design  
5. Terraform Modules & Workspaces  
6. Ansible Roles (Apache + Nginx + PHP-FPM + Proxy)  
7. Automation Scripts  
8. Health Checks & Testing Strategy  
9. Teardown / Cost Optimization  
10. Challenges & Solutions  
11. Conclusion  
12. Appendices  

---

## 📊 Grading Criteria (High Level)

### Excellent (90–100%)
- End-to-end provisioning works using scripts.
- Workspaces isolate environments correctly.
- Apache and Nginx+PHP-FPM stacks work and are reachable via proxy routes.
- Autoindex is configured safely and documented.
- Health checks and teardown are tested and documented well.

### Good (80–89%)
- Core automation works; minor gaps in docs or one stack.

### Satisfactory (70–79%)
- One environment works; limited automation and testing evidence.

### Needs Improvement (60–69%)
- Partial infrastructure; scripts not reliable.

### Unsatisfactory (<60%)
- No working on-demand environment lifecycle.

---

## ❓ FAQ

**Q1: Can I use MySQL/PostgreSQL/Redis/Memcached?**  
A: No. These are not part of the course outline for this project. Keep stacks limited to **Apache/Nginx + PHP/PHP-FPM**.

**Q2: Can I use Node.js or Python application stacks?**  
A: No. Use only the web stacks covered in the course.

**Q3: Can I use GitHub Actions for provisioning?**  
A: No. Use **bash scripts + manual CLI** workflow, which is within course scope.

**Q4: Do I need NAT Gateway?**  
A: Only if your private instances must install packages/updates. If you use it, document the reason and cost impact.

**Q5: How do I verify environment isolation?**  
A: Show separate Terraform workspaces (`dev`, `qa`, `uat`) and prove resources are tagged by environment and do not overlap.

---

## ✅ Final Checklist

- [ ] Terraform modules created (network, security, compute).
- [ ] Workspaces/state isolation implemented.
- [ ] Ansible roles run successfully (base-tools, apache-web, nginx-phpfpm-web, nginx-proxy-dev).
- [ ] env-provision and env-destroy scripts tested.
- [ ] Health checks pass and produce clear output.
- [ ] Docs completed with screenshots and step-by-step procedures.
