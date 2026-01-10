# 🚀 Project 2 – Automated Server Provisioning and Configuration System

**Duration:** 8–10 hours  
**Total Marks:** 100  
**Submission Format:** GitHub Repository + PDF Report  

---

## 📋 Project Overview

Build an **automated server provisioning and configuration management system** on AWS using **Terraform** and **Ansible**.

You will:

- Use **Terraform** to provision multiple **EC2 instances** with different roles (web servers, application servers).
- Use **Terraform workspaces** to manage **different server configurations** (e.g., dev, staging, production).
- Use **Ansible** playbooks and roles to:
  - Install required packages.
  - Configure host-based firewalls.
  - Set up user accounts and SSH keys.
  - Apply SSH hardening (disable root login, password authentication, etc.).
  - Deploy sample web and application code.
- Use **Ansible dynamic inventory** to automatically discover and configure newly created EC2 instances.
- Create **Ansible roles** for:
  - Web server setup (**Nginx**).
  - Application deployment.
  - System hardening.
- Implement **automated configuration drift detection and remediation** with Ansible.
- Version control all infrastructure code and playbooks in **GitHub**, with clear documentation.
- Test the **end-to-end provisioning process** by creating and configuring infrastructure from scratch.

**Key Concepts:** Terraform workspaces, EC2 provisioning, Ansible roles, dynamic inventory, server hardening, drift detection and remediation, Git-based workflows  

---

## 🎯 Learning Objectives

By completing this project, you will demonstrate:

- Structuring a GitHub repository for **infrastructure-as-code (IaC)** and **configuration management**.
- Using **Terraform** to:
  - Provision multiple EC2 instances.
  - Tag instances by **role** and **environment**.
  - Use **workspaces** for dev, staging, and production configurations.
- Using **Ansible** to:
  - Discover instances dynamically via **aws_ec2 inventory plugin**.
  - Configure servers based on role (web/app).
  - Harden SSH and the OS.
  - Deploy a sample web app (Nginx) and application services.
- Implementing **configuration drift detection** (finding deviations) and **remediation** (return to desired state).
- Integrating Terraform and Ansible into a **repeatable provisioning pipeline**.

---

## 🧱 Architecture Overview

### High-Level Architecture (Text)

```markdown
┌───────────────────────────────────────────────────────────────┐
│                        Developers                             │
└───────────────────────────────┬───────────────────────────────┘
                                │
                                │ Git push (dev / staging / main)
                                ▼
                   ┌──────────────────────────────┐
                   │       GitHub Repo            │
                   │ Terraform + Ansible + Docs   │
                   └───────────┬──────────────────┘
                               │
                               │ (Local CLI or GitHub Actions)
                               ▼
 ┌───────────────────────────────────────────────────────────────┐
 │                         AWS Account                           │
 │                                                               │
 │      ┌──────────────────────────────┐                         │
 │      │          Terraform           │                         │
 │      │ - Workspaces: dev/stg/prod   │                         │
 │      │ - EC2: Role=web / Role=app   │                         │
 │      └─────────────┬────────────────┘                         │
 │                    │                                           │
 │                    ▼                                           │
 │        ┌──────────────────────────────┐                        │
 │        │         EC2 Instances        │                        │
 │        │ - Web servers (Nginx)        │                        │
 │        │ - App servers (API/app)      │                        │
 │        │ - Tagged by Role/Env         │                        │
 │        └──────────────┬───────────────┘                        │
 │                       │                                        │
 │                       ▼                                        │
 │            ┌─────────────────────────┐                         │
 │            │        Ansible          │                         │
 │            │ - aws_ec2 Inventory     │                         │
 │            │ - Roles: hardening, web,│                         │
 │            │          app, users     │                         │
 │            │ - Drift detection & fix │                         │
 │            └─────────────────────────┘                         │
 │                                                               │
 └───────────────────────────────────────────────────────────────┘
```

---

## 📝 Requirements

### Part 1: Git Repository Setup (15 marks)

#### 1.1 Repository Structure (5 marks)

Create a well-organized Git repository following a pattern similar to Project 1, adapted for EC2 + Ansible:

```text
Project2/
├── README.md
├── .gitignore
├── .github/
│   └── workflows/
│       ├── provision-dev.yml
│       ├── provision-staging.yml
│       └── provision-production.yml
├── terraform/
│   ├── main.tf
│   ├── variables.tf
│   ├── outputs.tf
│   ├── backend.tf
│   ├── terraform.tfvars.example
│   └── modules/
│       └── ec2-servers/
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
│   │   ├── configure-all.yml
│   │   ├── detect-drift.yml
│   │   └── remediate-drift.yml
│   ├── roles/
│   │   ├── hardening/
│   │   │   ├── tasks/main.yml
│   │   │   ├── templates/sshd_config.j2
│   │   │   └── vars/main.yml
│   │   ├── web/
│   │   │   ├── tasks/main.yml
│   │   │   └── templates/nginx.conf.j2
│   │   ├── app/
│   │   │   ├── tasks/main.yml
│   │   │   └── templates/app-config.j2
│   │   └── users/
│   │       └── tasks/main.yml
│   └── group_vars/
│       └── all.yml
├── app/
│   ├── web/        # static content for Nginx
│   └── api/        # sample application code (Python/Node/etc.)
└── docs/
    ├── architecture.md
    ├── provisioning-guide.md
    ├── drift-management.md
    └── troubleshooting.md
```

**Tasks:**
- [ ] Create all directories and placeholder files.  
- [ ] Implement `.gitignore` for Terraform/Ansible/credentials (see 1.3).  
- [ ] Initialize Git repository and create an initial commit.  

**Deliverables:**
- Screenshot: `project2_part1_repository_structure.png`  
- Screenshot: `project2_part1_gitignore.png`  
- Screenshot: `project2_part1_initial_commit.png`  

---

#### 1.2 Git Branching Strategy (5 marks)

Use a multi-environment branching model:

**Branch Structure:**
```text
main (production)
├── staging
│   └── dev
└── feature/* (for new features & experiments)
```

**Branch Rules:**
- `dev` → used to test new provisioning and configuration changes.  
- `staging` → for pre-production validation of full automation.  
- `main` → stable production configuration.  

**Tasks:**
- [ ] Create `dev` and `staging` branches from `main`.  
- [ ] Configure branch protection rules on `main` and `staging`.  
- [ ] Document branching strategy in `README.md`.  
- [ ] Create a sample feature branch (e.g., `feature/add-ssh-hardening`).  

**Deliverables:**
- Screenshot: `project2_part1_git_branches.png`  
- Screenshot: `project2_part1_branch_protection.png`  
- Screenshot: `project2_part1_branching_diagram.png`  

---

#### 1.3 `.gitignore` Configuration (5 marks)

Use a comprehensive `.gitignore` similar to Project 1:

```gitignore
# Terraform files
**/.terraform/*
*.tfstate
*.tfstate.*
*.tfvars
!*.tfvars.example
crash.log
crash.*.log
override.tf
override.tf.json
*_override.tf
*_override.tf.json
.terraformrc
terraform.rc

# Ansible files
*.retry
.vault_pass
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

# Backup files
*.backup
*.bak

# Temporary files
tmp/
temp/
```

**Tasks:**
- [ ] Add `.gitignore` with the rules above.  
- [ ] Verify no sensitive files or state are committed.  
- [ ] Document key ignore rules in `README.md`.  

**Deliverables:**
- Screenshot: `project2_part1_gitignore_content.png`  
- Screenshot: `project2_part1_git_status_clean.png`  

---

### Part 2: Terraform Infrastructure (30 marks)

#### 2.1 EC2 Servers Module (15 marks)

Create a Terraform module to provision EC2 instances for both **web** and **application** roles.

**Module Location:** `terraform/modules/ec2-servers/`

**Example `variables.tf`:**
```hcl
variable "environment" {
  description = "Environment name (dev, staging, production)"
  type        = string
}

variable "project_name" {
  description = "Project name for tagging"
  type        = string
  default     = "project2-server-provisioning"
}

variable "web_instance_count" {
  description = "Number of web servers"
  type        = number
  default     = 1
}

variable "app_instance_count" {
  description = "Number of application servers"
  type        = number
  default     = 1
}

variable "instance_type" {
  description = "EC2 instance type"
  type        = string
  default     = "t3.micro"
}

variable "ami_id" {
  description = "Base AMI ID for instances"
  type        = string
}

variable "key_name" {
  description = "SSH key pair name"
  type        = string
}

variable "subnet_id" {
  description = "Subnet ID for instances (use private or public as needed)"
  type        = string
}

variable "security_group_ids" {
  description = "List of security group IDs to attach"
  type        = list(string)
  default     = []
}

variable "tags" {
  description = "Additional tags"
  type        = map(string)
  default     = {}
}
```

**Example `main.tf`:**
```hcl
# Web servers
resource "aws_instance" "web" {
  count         = var.web_instance_count
  ami           = var.ami_id
  instance_type = var.instance_type
  subnet_id     = var.subnet_id
  key_name      = var.key_name

  vpc_security_group_ids = var.security_group_ids

  tags = merge(
    {
      Name        = "${var.project_name}-${var.environment}-web-${count.index}"
      Project     = var.project_name
      Environment = var.environment
      Role        = "web"
      ManagedBy   = "Terraform"
    },
    var.tags
  )
}

# Application servers
resource "aws_instance" "app" {
  count         = var.app_instance_count
  ami           = var.ami_id
  instance_type = var.instance_type
  subnet_id     = var.subnet_id
  key_name      = var.key_name

  vpc_security_group_ids = var.security_group_ids

  tags = merge(
    {
      Name        = "${var.project_name}-${var.environment}-app-${count.index}"
      Project     = var.project_name
      Environment = var.environment
      Role        = "app"
      ManagedBy   = "Terraform"
    },
    var.tags
  )
}
```

**Example `outputs.tf`:**
```hcl
output "web_instance_ids" {
  description = "IDs of web instances"
  value       = aws_instance.web[*].id
}

output "web_private_ips" {
  description = "Private IPs of web instances"
  value       = aws_instance.web[*].private_ip
}

output "app_instance_ids" {
  description = "IDs of application instances"
  value       = aws_instance.app[*].id
}

output "app_private_ips" {
  description = "Private IPs of application instances"
  value       = aws_instance.app[*].private_ip
}
```

**Tasks:**
- [ ] Implement web and app EC2 resources.  
- [ ] Tag instances with `Project`, `Environment`, and `Role`.  
- [ ] Output instance IDs and IPs.  

**Deliverables:**
- Screenshot: `project2_part2_ec2_module_variables.png`  
- Screenshot: `project2_part2_ec2_module_main.png`  
- Screenshot: `project2_part2_ec2_module_outputs.png`  

---

#### 2.2 Root Terraform & Workspaces (15 marks)

Use **Terraform workspaces** to manage different configurations (dev, staging, production).

**terraform/main.tf** (outline):

```hcl
terraform {
  required_version = ">= 1.0"

  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
}

provider "aws" {
  region = var.aws_region

  default_tags {
    tags = {
      Project     = var.project_name
      Environment = terraform.workspace
      ManagedBy   = "Terraform"
    }
  }
}

locals {
  workspace = terraform.workspace

  env_config = lookup(
    {
      dev = {
        environment        = "dev"
        web_instance_count = 1
        app_instance_count = 1
        instance_type      = "t3.micro"
      }
      staging = {
        environment        = "staging"
        web_instance_count = 2
        app_instance_count = 2
        instance_type      = "t3.micro"
      }
      production = {
        environment        = "production"
        web_instance_count = 3
        app_instance_count = 3
        instance_type      = "t3.small"
      }
    },
    local.workspace
  )
}

module "ec2_servers" {
  source = "./modules/ec2-servers"

  environment        = local.env_config.environment
  project_name       = var.project_name
  web_instance_count = local.env_config.web_instance_count
  app_instance_count = local.env_config.app_instance_count
  instance_type      = local.env_config.instance_type

  ami_id             = var.ami_id
  key_name           = var.key_name
  subnet_id          = var.subnet_id
  security_group_ids = var.security_group_ids
}
```

**terraform/variables.tf** (key vars):  

- `aws_region`, `project_name`, `ami_id`, `key_name`, `subnet_id`, `security_group_ids`.

**Tasks:**
- [ ] Configure Terraform with AWS provider and default tags.  
- [ ] Implement workspace-dependent configuration via `local.env_config`.  
- [ ] Output summary information (counts, IPs).  

**Deliverables:**
- Screenshot: `project2_part2_main_tf.png`  
- Screenshot: `project2_part2_variables_tf.png`  
- Screenshot: `project2_part2_terraform_output.png`  

---

### Part 3: Ansible Configuration & Dynamic Inventory (25 marks)

#### 3.1 Ansible Configuration and Dynamic Inventory (10 marks)

Use the **aws_ec2** dynamic inventory plugin.

Example `ansible/inventory/dev_aws_ec2.yml`:

```yaml
plugin: aws_ec2
regions:
  - me-central-1
filters:
  tag:Project: "project2-server-provisioning"
  tag:Environment: "dev"
keyed_groups:
  - key: tags.Role
    prefix: role
hostnames:
  - private-ip-address
compose:
  ansible_host: private_ip_address
```

Create equivalent `staging_aws_ec2.yml` and `production_aws_ec2.yml` with `tag:Environment` changed accordingly.

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

**group_vars/all.yml** (outline):

```yaml
---
common_packages:
  - vim
  - git
  - curl

default_users:
  - name: deployer
    groups: sudo
    shell: /bin/bash

ssh_hardening:
  permit_root_login: "no"
  password_auth: "no"

nginx_listen_port: 80
app_service_name: "sample-app"
```

**Tasks:**
- [ ] Create dynamic inventory files for dev, staging, production.  
- [ ] Configure `ansible.cfg`.  
- [ ] Create `group_vars/all.yml` with reasonable defaults.  

**Deliverables:**
- Screenshot: `project2_part3_ansible_inventory_dev.png`  
- Screenshot: `project2_part3_ansible_cfg.png`  
- Screenshot: `project2_part3_group_vars_all.png`  

---

#### 3.2 Ansible Roles for Hardening, Web, App, Users (15 marks)

Create roles:

1. **`roles/hardening`** – **System & SSH Hardening (EXPLICIT REQUIREMENTS)**  
   This role must implement your baseline security controls on all servers.

   **Required behavior:**
   - Install base/common packages defined in `common_packages` (e.g., `vim`, `git`, `curl`).
   - Manage `/etc/ssh/sshd_config` from the template `roles/hardening/templates/sshd_config.j2`, using `ssh_hardening` variables:
     - `PermitRootLogin` set according to `ssh_hardening.permit_root_login` (typically `"no"`).
     - `PasswordAuthentication` set according to `ssh_hardening.password_auth` (typically `"no"`).
     - Enforce SSH protocol 2 (if applicable on your chosen AMI).
   - Ensure secure permissions on `/etc/ssh/sshd_config`:
     - Owner: `root`
     - Group: `root`
     - Mode: `0600`
   - Use a handler to **restart/reload SSH** when `sshd_config` changes:
     - Define `handlers/main.yml` with a `restart sshd` (or `restart ssh`) task.
   - The role must be **idempotent**:
     - Re-running the role should not report changes if nothing has drifted.

2. **`roles/users`**  
   - Create `deployer` user (and others as needed) from `default_users`.
   - Set correct shell, groups (e.g., `sudo`), and home directory.
   - Configure SSH authorized keys so that you can log in via key-based auth.
   - No direct root login should be required or used after hardening.

3. **`roles/web`**  
   - Install Nginx.
   - Configure Nginx using `templates/nginx.conf.j2`:
     - Listen on `nginx_listen_port` (default 80).
     - Serve static content (e.g., from `/var/www/html` populated from `app/web`).
   - Enable and start the Nginx service.

4. **`roles/app`**  
   - Install required runtime (Python/Node/etc.).
   - Deploy sample app from `app/api` to `/opt/app` or similar.
   - Configure a systemd service (via `templates/app-config.j2` or similar) that runs the app.
   - Enable and start the app service (`app_service_name`).

**Playbook: `ansible/playbooks/configure-all.yml`**

```yaml
---
- name: Configure all servers according to role
  hosts: all
  gather_facts: yes

  roles:
    - role: hardening
    - role: users

- name: Configure web servers
  hosts: role_web
  gather_facts: no

  roles:
    - role: web

- name: Configure application servers
  hosts: role_app
  gather_facts: no

  roles:
    - role: app
```

**Tasks:**
- [ ] Implement roles: `hardening`, `users`, `web`, `app`.  
- [ ] Make roles idempotent (re-runs should not fail or re-change unnecessarily).  
- [ ] Verify that web servers and app servers are correctly configured by role and hardened.  

**Deliverables:**
- Screenshot: `project2_part3_roles_structure.png`  
- Screenshot: `project2_part3_hardening_role_main.png`  
- Screenshot: `project2_part3_web_role_main.png`  
- Screenshot: `project2_part3_app_role_main.png`  
- Screenshot: `project2_part3_configure_all_execution.png`  

---

### Part 4: Configuration Drift Detection & Remediation (20 marks)

#### 4.1 Drift Detection (10 marks)

Create `ansible/playbooks/detect-drift.yml`:

- Runs **in check mode** (`--check`) or with `check_mode: yes` on critical tasks.  
- Detects if:
  - `sshd_config` differs from template.  
  - Required packages are missing.  
  - Nginx/app service is disabled/stopped.  
- Use `changed_when` and `failed_when` to clearly show drift.

**Example (per host group):**
```yaml
---
- name: Detect configuration drift on all servers
  hosts: all
  gather_facts: yes

  tasks:
    - name: Check if sshd_config matches template (hash)
      command: sha256sum /etc/ssh/sshd_config
      register: sshd_hash
      changed_when: false

    # (You can compare against expected hash or simply rely on template + diff in --check runs)
```

**Tasks:**
- [ ] Implement `detect-drift.yml` that surfaces deviations clearly.  
- [ ] Demonstrate a run where there is **no drift** and a run where you have manually changed something.  

**Deliverables:**
- Screenshot: `project2_part4_detect_drift_playbook.png`  
- Screenshot: `project2_part4_detect_drift_execution.png`  

---

#### 4.2 Drift Remediation (10 marks)

Create `ansible/playbooks/remediate-drift.yml`:

- Essentially re-runs the same roles as `configure-all.yml` to **restore desired state**.  
- Demonstrate that:
  - After manual changes (e.g., altering SSH config or uninstalling Nginx),  
  - `remediate-drift.yml` brings the system back to the desired configuration.

**Tasks:**
- [ ] Implement `remediate-drift.yml`.  
- [ ] Show before/after evidence in screenshots/logs.  

**Deliverables:**
- Screenshot: `project2_part4_remediate_drift_playbook.png`  
- Screenshot: `project2_part4_remediate_drift_execution.png`  

---

### Part 5: CI/CD and End-to-End Provisioning (10 marks)

#### 5.1 Optional GitHub Actions Workflows (5 marks)

If using GitHub Actions, create:

- `.github/workflows/provision-dev.yml`  
- `.github/workflows/provision-staging.yml`  
- `.github/workflows/provision-production.yml`  

Each should:

- Checkout repository.  
- Configure AWS credentials.  
- Set Terraform workspace (`dev`, `staging`, `production`).  
- Run `terraform fmt -check`, `validate`, `plan`, `apply`.  
- Run Ansible `configure-all.yml`.  
- Optionally run `detect-drift.yml` as a post-check.

**Deliverables:**
- Screenshot: `project2_part5_dev_workflow_run.png`  
- (If not using Actions, describe your manual pipeline in the report.)  

---

#### 5.2 End-to-End Provisioning Test (5 marks)

Manually (or via CI) perform a full test in `dev`:

1. Ensure no leftover EC2 instances from previous runs.  
2. Run:
   - `terraform workspace select dev`  
   - `terraform apply`  
   - `ansible-playbook -i inventory/dev_aws_ec2.yml playbooks/configure-all.yml`  
3. Validate:
   - Nginx serves a test page on web nodes.  
   - Application service is running and reachable on app nodes.  
   - SSH hardening is in place (no root login, key-only).  

**Deliverables:**
- Screenshot: `project2_part5_end_to_end_terraform_apply.png`  
- Screenshot: `project2_part5_end_to_end_ansible_run.png`  
- Screenshot: `project2_part5_web_and_app_validation.png`  

---

## 🧹 Clean-Up Strategy (Cost & Resource Management)

Destroy infrastructure when not needed:

```bash
cd terraform/

terraform workspace select dev
terraform destroy

terraform workspace select staging
terraform destroy

terraform workspace select production
terraform destroy
```

- Confirm all EC2 instances tagged with `Project=project2-server-provisioning` are terminated.  
- Clean local Ansible artifacts/logs as needed.  

---

## 📦 Submission Guidelines

### 1. GitHub Repository

Example naming:

```text
CC_<YourName>_<YourRollNumber>/Project-2-Automated-Servers
```

- Include all Terraform code, Ansible code, `app/`, `docs/`, screenshots.  
- Do **not** commit sensitive data or Terraform state.  

### 2. PDF Report

Name:

```text
Project2_<YourName>_<YourRollNumber>.pdf
```

Suggested sections:

1. Cover Page  
2. Table of Contents  
3. Executive Summary  
4. Architecture & Design (Terraform, workspaces, Ansible roles, inventory)  
5. Implementation Details  
6. Drift Detection & Remediation  
7. End-to-End Provisioning Test  
8. Challenges & Solutions  
9. Conclusion  
10. Appendices (config snippets, logs, extra screenshots)  

---

## 📊 Detailed Grading Criteria

### Excellent (90–100%)

- Terraform workspaces + EC2 provisioning fully implemented and documented.  
- Ansible roles (hardening, web, app, users) complete, idempotent, and well-structured.  
- Dynamic inventory and role-based configuration works across roles.  
- Drift detection and remediation clearly demonstrated.  
- Clear PDF report with strong screenshots and explanations.  

### Good (80–89%)

- Most features working; minor gaps or rough edges.  
- Some drift detection/remediation implemented.  
- Documentation mostly complete.  

### Satisfactory (70–79%)

- Core provisioning + configuration works for at least one environment.  
- Drift detection/remediation partially implemented.  
- Documentation present but missing detail.  

### Needs Improvement (60–69%)

- Partial infra or Ansible roles; manual steps still required.  
- Limited or no drift handling.  
- Thin or incomplete documentation.  

### Unsatisfactory (<60%)

- Major parts missing (no working provisioning, roles, or drift handling).  
- Poor or missing report.  

---

## 💡 Tips for Success

- Start with **one workspace (dev)** until infrastructure + Ansible are stable.  
- Keep tasks **idempotent** so they can fix drift reliably.  
- Tag servers clearly (`Project`, `Environment`, `Role`) to make dynamic inventory easy.  
- Test roles in isolation (e.g., run `hardening` first, then `web`, then `app`).  

---

## 🐞 Common Issues & Solutions

**Issue:** Dynamic inventory finds no hosts  
- Check region and tag filters.  
- Ensure instances are running and correctly tagged.  

**Issue:** SSH fails with “permission denied”  
- Validate key pair, security groups, and SSH hardening settings.  

**Issue:** Ansible reports changes every run (not idempotent)  
- Review `changed_when` and `creates`/`removes` usage; ensure desired state is properly declared.  

---

## 📚 Resources

- [Terraform AWS Provider](https://registry.terraform.io/providers/hashicorp/aws/latest/docs)  
- [Terraform Workspaces](https://developer.hashicorp.com/terraform/language/state/workspaces)  
- [Ansible aws_ec2 Inventory Plugin](https://docs.ansible.com/ansible/latest/collections/amazon/aws/aws_ec2_inventory.html)  
- [Ansible Roles](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_reuse_roles.html)  

---

## ❓ FAQ

**Q: Do I have to use Nginx specifically?**  
A: Yes, for this project use Nginx as the web server to meet the requirement.

**Q: Can I use a different cloud provider?**  
A: No, this project is based on AWS EC2 and the aws_ec2 dynamic inventory plugin.

**Q: Is CI/CD mandatory?**  
A: At minimum, scripts/workflows should exist; you may run them manually or via GitHub Actions.

---

## 📜 Academic Integrity

- This is an **Group** project. (Max 3 persons)  
- Do not copy code from other students or public repos without proper citation.  
- You may reuse patterns from official docs but must adapt and understand them.    

---

## ⏰ Deadline

**Due Date:** 26-01-2026  
**Time:** 09:00 AM  

Late submissions follow your course’s standard policy.

---

## ✅ Final Checklist

### Terraform

- [ ] EC2 module implemented for web and app roles.  
- [ ] Workspaces: dev, staging, production created and tested.  
- [ ] Outputs list instance IDs and IPs.  

### Ansible

- [ ] Dynamic inventory for dev/staging/prod configured.  
- [ ] Roles for `hardening`, `users`, `web`, `app` implemented.  
- [ ] `configure-all.yml` successfully configures all servers.  

### Drift

- [ ] `detect-drift.yml` shows drift when present.  
- [ ] `remediate-drift.yml` restores desired configuration.  

### Docs & Report

- [ ] `README.md` explains setup and usage.  
- [ ] `docs/*.md` updated.  
- [ ] PDF report complete with screenshots and explanations.  
