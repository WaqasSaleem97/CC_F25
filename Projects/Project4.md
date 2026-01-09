# 🛡️ Project 4 – Secure Multi-Tier Network Architecture

**Duration:** 8–10 hours  
**Total Marks:** 100  
**Submission Format:** GitHub Repository + PDF Report  

---

## 📋 Project Overview

Build a **secure three-tier network architecture** on AWS using **Terraform** and **Ansible**, using only the tools and concepts covered in this course (**Linux administration + Git/GitHub + AWS IAM/VPC/EC2 + Terraform + Ansible + Nginx**).

You will:

- Design and provision a **VPC** with:
  - **Public subnets** for a web tier (**Nginx reverse proxy**).
  - **Private subnets** for an application tier.
  - A **management subnet** for a **bastion/jump host** (single SSH entry point).
- Implement **strict Security Groups** using the **least privilege** principle (Security Groups only; no NACL requirements).
- Use **Ansible** to **harden Linux servers** (course-scope hardening):
  - Disable root login.
  - Disable SSH password authentication (key-only SSH).
  - Set correct permissions on SSH configuration files.
  - Create a non-root admin user (e.g., `deployer`) and configure SSH keys.
  - Install common packages and ensure services are enabled.
- Deploy **Nginx reverse proxy** in the web tier that forwards requests to app servers in private subnets.
- Implement **basic centralized logging using Linux log files + Ansible collection**:
  - Collect key logs (`/var/log/auth.log` or `/var/log/secure`, Nginx access/error logs) from all servers using Ansible and store them in the repository under `docs/logs/` for evidence.
  - (This replaces rsyslog/journald log forwarding, which is not in course outline.)
- Create a **bastion host** pattern to securely SSH into private instances.
- Document the complete **security architecture** and create a **security compliance checklist** based on what you implemented.

**Key Concepts (Course Scope):** Network segmentation (subnets + SGs), least privilege SG rules, bastion host access, SSH security, Nginx reverse proxy, Linux users/groups/permissions, Ansible roles, Terraform modules, Git workflows



---

## 🎯 Learning Objectives

By completing this project, you will demonstrate:

- Designing a **three-tier network** with clear separation: Web, Application, and Management tiers.
- Using **Terraform** to provision:
  - VPC, subnets, route tables, and internet gateway.
  - EC2 instances for web/app/bastion roles.
  - Security Groups with least privilege.
- Using **Ansible** to:
  - Harden Linux servers (SSH hardening + users + permissions).
  - Configure Nginx as a reverse proxy.
  - Collect logs from servers for verification evidence.
- Implementing **bastion host** patterns for secure SSH access.
- Writing **security documentation** and a **security compliance checklist**.

---

## 🧱 Architecture Overview

### High-Level Architecture (Text)

```markdown
┌───────────────────────────────────────────────────────────────┐
│                       External Clients                        │
└───────────────────────────────┬───────────────────────────────┘
                                │
                                │ HTTP (80) → Nginx Reverse Proxy
                                ▼
 ┌───────────────────────────────────────────────────────────────┐
 │                         AWS VPC                               │
 │                 (Secure 3-Tier Architecture)                  │
 │                                                               │
 │  Public Subnets (Web Tier)                                    │
 │  ┌───────────────────────────────┐                            │
 │  │  Web Server (Nginx)           │◄─────────── Internet       │
 │  │  - Reverse proxy              │                            │
 │  │  - Only HTTP open publicly    │                            │
 │  └───────────────┬──────────────┘                             │
 │                  │ Proxy HTTP to App Tier                     │
 │                  ▼                                            │
 │  Private Subnets (Application Tier)                           │
 │  ┌───────────────────────────────┐                            │
 │  │  Application Servers          │                            │
 │  │  - App on port 8080           │                            │
 │  │  - No public IPs              │                            │
 │  └───────────────┬───────────────┘                            │
 │                  │                                            │
 │                  │ SSH from Bastion only                      │
 │                  ▼                                            │
 │  Management Subnet (Mgmt Tier)                                │
 │  ┌───────────────────────────────┐                            │
 │  │  Bastion Host (Jump Box)      │                            │
 │  │  - SSH from Admin IPs only    │                            │
 │  │  - SSH into web/app servers   │                            │
 │  └───────────────────────────────┘                            │
 │                                                               │
 │  Security Groups:                                             │
 │  - Strict rules per tier (web/app/mgmt)                       │
 │  - Deny by default, allow minimal required traffic            │
 └───────────────────────────────────────────────────────────────┘
```

You will refine this diagram and include it in `docs/architecture.md` (e.g., from app.eraser.io).

---

## 📝 Requirements

### Part 1: Git Repository Setup (15 marks)

#### 1.1 Repository Structure (5 marks)

Create a well-organized Git repository adapted for secure multi-tier infrastructure:

```text
Project4/
├── README.md
├── .gitignore
├── terraform/
│   ├── main.tf
│   ├── variables.tf
│   ├── outputs.tf
│   ├── backend.tf
│   ├── terraform.tfvars.example
│   ├── environments/
│   │   ├── dev.tfvars
│   │   ├── staging.tfvars
│   │   └── production.tfvars
│   └── modules/
│       ├── network/           # VPC, subnets, route tables, IGW
│       │   ├── main.tf
│       │   ├── variables.tf
│       │   └── outputs.tf
│       ├── security/          # Security Groups (least privilege)
│       │   ├── main.tf
│       │   ├── variables.tf
│       │   └── outputs.tf
│       └── compute/           # Web/App/Bastion EC2
│           ├── main.tf
│           ├── variables.tf
│           └── outputs.tf
├── ansible/
│   ├── ansible.cfg
│   ├── inventory/
│   │   ├── dev_aws_ec2.yml
│   │   ├── staging_aws_ec2.yml
│   │   └── production_aws_ec2.yml
│   ├── playbooks/
│   │   ├── harden-servers.yml
│   │   ├── configure-nginx-proxy.yml
│   │   ├── collect-logs.yml
│   │   └── verify-security.yml
│   ├── roles/
│   │   ├── hardening/
│   │   │   ├── tasks/main.yml
│   │   │   └── templates/sshd_config.j2
│   │   ├── nginx-reverse-proxy/
│   │   │   ├── tasks/main.yml
│   │   │   └── templates/nginx.conf.j2
│   │   └── app/
│   │       └── tasks/main.yml
│   └── group_vars/
│       └── all.yml
├── app/
│   └── api/                  # simple app on port 8080 (Python or Node)
└── docs/
    ├── architecture.md
    ├── security-architecture.md
    ├── security-checklist.md
    ├── troubleshooting.md
    └── logs/                  # collected logs evidence (from Ansible)
```

**Tasks:**
- [ ] Create all necessary files and directories.  
- [ ] Document the structure in `README.md`.  
- [ ] Initialize Git repository and create initial commit.  

**Deliverables:**
- Screenshot: `project4_part1_repository_structure.png`  
- Screenshot: `project4_part1_gitignore.png`  
- Screenshot: `project4_part1_initial_commit.png`  

---

#### 1.2 Git Branching Strategy (5 marks)

Use the same branching strategy taught in GitHub collaboration:

**Branch Structure:**
```text
main (production)
├── staging
│   └── dev
└── feature/* (for new features)
```

**Branch Rules:**
- `dev` → Deploys to **Dev** environment (used for experimentation).  
- `staging` → Deploys to **Staging** environment.  
- `main` → Deploys to **Production** environment.  

**Tasks:**
- [ ] Create `dev` and `staging` branches from `main`.  
- [ ] Document branching strategy in `README.md`.  
- [ ] Create a sample feature branch (e.g., `feature/ssh-hardening`).  

**Deliverables:**
- Screenshot: `project4_part1_git_branches.png`  
- Screenshot: `project4_part1_branching_diagram.png`  

---

#### 1.3 `.gitignore` Configuration (5 marks)

Use the same comprehensive `.gitignore` as previous projects:

```gitignore
# Terraform files
**/.terraform/*
*.tfstate
*.tfstate.*
*.tfvars
!*.tfvars.example
crash.log
crash.*.log
.terraformrc
terraform.rc

# Ansible files
*.retry
*.secret

# AWS credentials
.aws/
*.pem
*.key

# IDE files
.vscode/
.idea/
*.swp
*.swo
*~

# OS files
.DS_Store
Thumbs.db

# Logs
*.log
logs/

# Environment files
.env
.env.local

# Temporary files
tmp/
temp/
```

**Tasks:**
- [ ] Create `.gitignore` with all required exclusions.  
- [ ] Verify no sensitive files (`.pem`, keys, `tfstate`) are tracked.  

**Deliverables:**
- Screenshot: `project4_part1_gitignore_content.png`  
- Screenshot: `project4_part1_git_status_clean.png`  

---

### Part 2: Terraform – VPC, Security, Compute (35 marks)

#### 2.1 VPC & Subnet Module (12 marks)

**Module:** `terraform/modules/network/`

Requirements:

- VPC CIDR (e.g., `10.0.0.0/16`).  
- **Public Subnets** (Web Tier) in at least 2 AZs.  
- **Private Subnets** (Application Tier) in at least 2 AZs.  
- **Management Subnet** (Management Tier) in at least 1 AZ.  
- Internet Gateway attached to VPC.  
- Route tables:
  - Public route table → default route to IGW.
  - Private route table → no default route to internet required for this project (document limitation).  

**Outputs:**
- VPC ID  
- Public subnet IDs  
- Private subnet IDs  
- Management subnet ID(s)

**Tasks:**
- [ ] Implement VPC topology in Terraform.  
- [ ] Tag subnets by tier: `Tier=web`, `Tier=app`, `Tier=management`.  

**Deliverables:**
- Screenshot: `project4_part2_network_main.png`  
- Screenshot: `project4_part2_network_outputs.png`  

---

#### 2.2 Security Module – Security Groups (Least Privilege) (10 marks)

**Module:** `terraform/modules/security/`

Requirements:

Create **Security Groups** (no NACL requirement):

- **Web SG**
  - Inbound:
    - HTTP (80) from allowed CIDRs (can be `0.0.0.0/0` in dev)
    - SSH (22) only from **Bastion SG**
  - Outbound:
    - To App SG on app port (e.g., 8080)

- **App SG**
  - Inbound:
    - App port (8080) only from **Web SG**
    - SSH (22) only from **Bastion SG**
  - Outbound:
    - Allow all (or restrict to necessary as you document)

- **Bastion SG**
  - Inbound:
    - SSH (22) only from Admin IP(s)
  - Outbound:
    - SSH (22) to Web SG and App SG

**Tasks:**
- [ ] Implement SGs with least privilege and SG-to-SG referencing.  
- [ ] Variables for admin IP CIDR and app port.

**Deliverables:**
- Screenshot: `project4_part2_security_main.png`  
- Screenshot: `project4_part2_security_rules_summary.png` (table or diagram acceptable)

---

#### 2.3 Compute Module – Web, App, Bastion (13 marks)

**Module:** `terraform/modules/compute/`

Requirements:

- **EC2 Instances:**
  - Web Tier:
    - Nginx reverse proxy host(s) in public subnets.
    - Associate with Web SG.
  - App Tier:
    - App server(s) in private subnets.
    - Associate with App SG.
    - **No public IPs**.
  - Management Tier:
    - Bastion host in management subnet.
    - Associate with Bastion SG.

- Tag instances:
  - `Project`, `Environment`, `Tier`, `Role`, `Name`

**Outputs:**
- Web instance IDs and public IPs.  
- App instance IDs and private IPs.  
- Bastion host public IP.  

**Tasks:**
- [ ] Define AMI, instance type, key pair as variables.  
- [ ] Confirm app instances have no public IPs.

**Deliverables:**
- Screenshot: `project4_part2_compute_main.png`  
- Screenshot: `project4_part2_compute_outputs.png`  

---

### Part 3: Root Terraform & Environments (15 marks)

#### 3.1 Root Terraform Configuration (8 marks)

**Files:** `terraform/main.tf`, `variables.tf`, `outputs.tf`, `backend.tf`

Requirements:

- Configure AWS provider and Terraform backend.  
- Instantiate `network`, `security`, and `compute` modules:
  - Wire VPC and subnet outputs into security/compute modules.
  - Wire SG IDs into compute module.  
- Export:
  - Bastion public IP (for SSH).  
  - Web public endpoint(s).  
  - App private IP(s).

**Tasks:**
- [ ] Implement `main.tf` wiring all modules.  
- [ ] Add helpful outputs for Ansible inventory and documentation.

**Deliverables:**
- Screenshot: `project4_part3_main_tf.png`  
- Screenshot: `project4_part3_outputs_tf.png`  

---

#### 3.2 Environment-Specific Variables (7 marks)

**Files:**
- `terraform/environments/dev.tfvars`  
- `terraform/environments/staging.tfvars`  
- `terraform/environments/production.tfvars`  
- `terraform/terraform.tfvars.example`  

Per environment, configure:

- CIDRs (admin IP, allowed HTTP ranges).
- Instance sizes and counts (Dev smaller, Prod more robust).
- Whether to deploy multiple web/app instances.

**Tasks:**
- [ ] Create environment `.tfvars` for dev/staging/production.  
- [ ] Use a consistent region (e.g., `me-central-1`).  

**Deliverables:**
- Screenshot: `project4_part3_dev_tfvars.png`  
- Screenshot: `project4_part3_staging_tfvars.png`  
- Screenshot: `project4_part3_production_tfvars.png`  
- Screenshot: `project4_part3_tfvars_example.png`  

---

### Part 4: Ansible – Hardening, Nginx Reverse Proxy, Log Collection (20 marks)

#### 4.1 Ansible Inventory & Global Config (5 marks)

Use **aws_ec2** dynamic inventory to target instances by tags (e.g., `Tier=web/app/management`).

Example `ansible/inventory/dev_aws_ec2.yml`:

```yaml
plugin: aws_ec2
regions:
  - me-central-1
filters:
  tag:Project: "Project4-Secure-Network"
  tag:Environment: "dev"
keyed_groups:
  - key: tags.Tier
    prefix: tier
hostnames:
  - private-ip-address
compose:
  ansible_host: private_ip_address
```

**ansible/ansible.cfg:**

```ini
[defaults]
inventory = inventory/dev_aws_ec2.yml
host_key_checking = False
retry_files_enabled = False
gathering = smart
stdout_callback = yaml
roles_path = roles

[privilege_escalation]
become = True
become_method = sudo

[ssh_connection]
pipelining = True
```

**group_vars/all.yml:**
- SSH hardening settings (permit root login, password auth).
- Nginx upstream app port.
- App listen port (8080).

**Deliverables:**
- Screenshot: `project4_part4_ansible_inventory_dev.png`  
- Screenshot: `project4_part4_ansible_cfg.png`  
- Screenshot: `project4_part4_group_vars_all.png`  

---

#### 4.2 Server Hardening Role (10 marks)

**Role:** `roles/hardening`

Required tasks:

- Create a non-root admin user (e.g., `deployer`) and add to sudo.
- Configure SSH authorized keys for `deployer`.
- Disable root SSH login.
- Disable SSH password authentication (key-only).
- Ensure correct permissions:
  - `/etc/ssh/sshd_config` owned by root, mode `0600`.
- Reload/restart SSH service via handler.
- Install common packages (`vim`, `curl`, `git`).

Playbook `ansible/playbooks/harden-servers.yml`:

```yaml
---
- name: Harden all Linux servers
  hosts: all
  gather_facts: yes
  roles:
    - hardening
```

**Deliverables:**
- Screenshot: `project4_part4_hardening_role_main.png`  
- Screenshot: `project4_part4_harden_execution.png`  

---

#### 4.3 Nginx Reverse Proxy & App Tier (5 marks)

**Role:** `roles/nginx-reverse-proxy`

- Install Nginx on **web tier** (hosts in `tier_web` group).
- Configure `nginx.conf`:
  - Listens on 80.
  - Forwards traffic to app tier servers (e.g., `upstream app_backend` on port 8080).
  - Provide a `/health` endpoint on web tier.

**Role:** `roles/app`

- Deploy a simple app that listens on port 8080 (Python/Node).
- Provide a basic route and `/health` route.

Playbook `ansible/playbooks/configure-nginx-proxy.yml`:

```yaml
---
- name: Configure application servers
  hosts: tier_app
  gather_facts: yes
  roles:
    - app

- name: Configure Nginx reverse proxy on web tier
  hosts: tier_web
  gather_facts: yes
  roles:
    - nginx-reverse-proxy
```

**Deliverables:**
- Screenshot: `project4_part4_nginx_conf_template.png`  
- Screenshot: `project4_part4_configure_nginx_execution.png`  

---

#### 4.4 Log Collection for Evidence (5 marks)

**Playbook:** `ansible/playbooks/collect-logs.yml`

Goal: collect logs using Ansible and store them locally in the repo under `docs/logs/`.

Collect at least:

- SSH log: `/var/log/auth.log` (Ubuntu) or `/var/log/secure` (Amazon Linux)
- Nginx logs on web tier:
  - `/var/log/nginx/access.log`
  - `/var/log/nginx/error.log`

Example outline:

```yaml
---
- name: Collect logs for evidence
  hosts: all
  gather_facts: yes
  tasks:
    - name: Fetch SSH log file
      ansible.builtin.fetch:
        src: "{{ ssh_log_path }}"
        dest: "../docs/logs/{{ inventory_hostname }}/"
        flat: no
      ignore_errors: yes
```

**Tasks:**
- [ ] Implement `collect-logs.yml`.
- [ ] Document in README which logs you collected and why.

**Deliverables:**
- Screenshot: `project4_part4_collect_logs_playbook.png`  
- Screenshot: `project4_part4_logs_collected_tree.png`  

---

### Part 5: Security Testing & Documentation (15 marks)

#### 5.1 Security Architecture & Checklist (10 marks)

In `docs/security-architecture.md`, document:

- Network segmentation:
  - Which traffic is allowed between web/app/management tiers.
- Security Groups rules (least privilege).
- SSH access flow via bastion.
- How Ansible hardening enforces secure SSH access.

In `docs/security-checklist.md`, create a checklist such as:

- [ ] Root login disabled on all servers.
- [ ] SSH password authentication disabled.
- [ ] Only SSH keys allowed for `deployer`.
- [ ] App servers have no public IPs.
- [ ] Web servers expose only HTTP (80) publicly.
- [ ] SSH to web/app is only possible through bastion.
- [ ] Nginx reverse proxy forwards correctly to app tier.
- [ ] Logs collected and stored in `docs/logs/`.

**Deliverables:**
- Screenshot: `project4_part5_security_architecture_doc.png`  
- Screenshot: `project4_part5_security_checklist_doc.png`  

---

#### 5.2 Verification & Testing (5 marks)

Perform and document tests:

- Attempt SSH to app server **directly** from your local machine → should FAIL (no public IP / SG restriction).
- SSH to bastion from your admin IP → should SUCCEED.
- SSH from bastion to app server private IP → should SUCCEED.
- Access the web tier public URL:
  - Confirm Nginx reverse proxy forwards to app tier.
- Show evidence of secure SSH config:
  - `PermitRootLogin no`
  - `PasswordAuthentication no`

**Deliverables:**
- Screenshot: `project4_part5_bastion_ssh_flow.png`  
- Screenshot: `project4_part5_nginx_proxy_working.png`  
- Screenshot: `project4_part5_sshd_config_evidence.png`  

---

## 📦 Submission Guidelines

### 1. GitHub Repository

Example name:

```text
CC_<YourName>_<YourRollNumber>/Project-4-Secure-MultiTier-Network
```

Do **not** commit:

- `terraform.tfstate`, `.terraform/`, real `terraform.tfvars`.  
- Any credential files (`.aws/`, `*.pem`, `*.key`, `.env`, etc.).  

### 2. PDF Report

Name:

```text
Project4_<YourName>_<YourRollNumber>.pdf
```

Suggested structure:

1. Cover Page  
2. Table of Contents  
3. Executive Summary  
4. Architecture Design (network & security diagrams)  
5. Terraform Implementation (network, security groups, compute)  
6. Ansible Implementation (hardening, Nginx reverse proxy, log collection)  
7. Security Testing & Compliance Checklist  
8. Challenges & Solutions  
9. Conclusion & Future Improvements  
10. Appendices (snippets, logs, screenshots)  

---

## 📊 Grading Criteria (High Level)

### Excellent (90–100%)
- Fully implemented 3-tier architecture with strict Security Groups.
- Bastion host pattern correctly implemented.
- SSH hardening completed and verified (root disabled, key-only).
- Nginx reverse proxy works correctly and app tier is private.
- Logs collected as evidence + strong documentation and checklist.

### Good (80–89%)
- Most features implemented; minor gaps in testing or documentation.

### Satisfactory (70–79%)
- Core tiers working; some security rules or evidence missing.

### Needs Improvement (60–69%)
- Partial implementation; security posture not clearly tested.

### Unsatisfactory (<60%)
- Incomplete architecture; major components missing or insecure.

---

## 💡 Tips for Success

- Start with network + compute, then implement SG restrictions.
- Apply SSH hardening gradually and test SSH after each change.
- Use tags to simplify Ansible dynamic inventory grouping.
- Keep Ansible roles idempotent and rerunnable.
- Use bastion as the single SSH entry point.

---

## 🐞 Common Issues & Solutions

**Issue:** Can't SSH into app servers  
- Confirm app servers have no public IP and SG restricts SSH to bastion only.  
- Verify you can SSH to bastion first.

**Issue:** Nginx returns 502/504  
- Check app is running and listening on port 8080.
- Verify upstream config uses correct private IPs.

**Issue:** Logs not collected  
- Ensure correct log file path (`/var/log/auth.log` vs `/var/log/secure`).
- Ensure Ansible user has privilege escalation enabled.

---

## 📚 Resources

- [AWS VPC Documentation](https://docs.aws.amazon.com/vpc/latest/userguide/)  
- [AWS Security Groups](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html)  
- [Terraform AWS Provider](https://registry.terraform.io/providers/hashicorp/aws/latest/docs)  
- [Ansible Roles](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_reuse_roles.html)  
- [Nginx Reverse Proxy Guide](https://docs.nginx.com/nginx/admin-guide/web-server/reverse-proxy/)  

---

## ❓ FAQ

**Q: Can I use NACLs / CloudWatch / SSM / NAT Gateway / fail2ban?**  
A: No. This project must stay within course scope (IAM/VPC/EC2 + Terraform + Ansible + Nginx).

**Q: Do I need SSL on Nginx?**  
A: Not required for this project; focus is on segmentation + SSH hardening + reverse proxy.

---

## 📜 Academic Integrity

- Group project (max 3 persons).  
- Do not copy code from others without citation.  
- Cite any external references used.

---

## ⏰ Deadline

**Due Date:** 26-01-2026  
**Time:** 09:00 AM  

Late submissions follow your course policy.

---

## ✅ Final Checklist

### Terraform
- [ ] VPC with public, private, and management subnets.
- [ ] Security Groups implemented with least privilege.
- [ ] EC2 instances for web, app, and bastion created and tagged.
- [ ] App tier has no public IPs.

### Ansible
- [ ] Hardening role applied to all servers.
- [ ] Nginx reverse proxy configured on web tier.
- [ ] App tier reachable only via web tier and bastion.
- [ ] Logs collected into `docs/logs/`.

### Documentation
- [ ] Architecture & security docs complete.
- [ ] Security checklist completed.
- [ ] PDF report includes screenshots and verification results.
