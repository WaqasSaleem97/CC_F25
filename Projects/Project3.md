# 🚀 Project 3 – High Availability Web Application with Nginx Load Balancing

**Duration:** 8–10 hours  
**Total Marks:** 100  
**Submission Format:** GitHub Repository + PDF Report  

---

## 📋 Project Overview

Deploy a **highly available web application infrastructure** on AWS using **Terraform** and **Ansible**, using only the services and tools covered in this course (**AWS IAM, VPC, EC2 + Terraform + Ansible + Nginx + Bash scripting**).

You will:

- Use **Terraform** to provision:
  - A **VPC** with multiple subnets across **at least two Availability Zones**.
  - **1 Nginx Load Balancer / Reverse Proxy** EC2 instance in a public subnet (Internet-facing).
  - **2 Backend web servers** (Nginx) EC2 instances deployed across **two AZs** (web-1, web-2).
  - **1 Backup web server** (Nginx) EC2 instance (web-3) configured as **backup** (only used when primary servers fail).
  - Proper **security groups** so:
    - Only the load balancer is publicly accessible.
    - Backends accept traffic only from the load balancer.
- Use **Ansible** to:
  - Install and configure **Nginx** on all instances.
  - Configure **Nginx load balancing** with **backup server** support.
  - Configure **health-check endpoints** (e.g., `/health`) and custom health-check pages.
  - Deploy a **custom web application** (static HTML/CSS/JS) to all backend servers.
  - Perform **rolling application updates** safely (one backend at a time).
  - Install and configure a **monitoring script** on the load balancer to check backend health and log results.
- Implement **high availability testing** by stopping Nginx on backends and verifying:
  - The load balancer continues serving traffic using remaining healthy servers.
  - The backup server is used only when both primary servers fail.
- Implement **SSL termination** on the Nginx load balancer using **self-signed certificates**.
- Implement **content caching** at the Nginx load balancer for performance optimization (as covered in Week 14).

**Key Concepts (Course Scope):** Multi-AZ design, EC2 provisioning, Terraform modules, Ansible roles, Nginx reverse proxy & load balancing, backup server, SSL termination (self-signed), caching, health checks, rolling updates, bash monitoring script

---

## 🎯 Learning Objectives

By completing this project, you will demonstrate:

- Designing a **high availability** web architecture using AWS building blocks (**VPC, EC2, IAM**) and **multi-AZ** subnet layout.
- Using **Terraform** to provision:
  - VPC, subnets, route tables, internet gateway, security groups.
  - EC2 instances for a load balancer and multiple backend servers.
  - Reusable Terraform modules to keep code organized.
- Using **Ansible** to:
  - Install and configure Nginx for load balancing (reverse proxy) and backend web serving.
  - Configure SSL/TLS with **self-signed certificates** on the Nginx load balancer.
  - Configure Nginx caching (proxy cache) for performance.
  - Deploy and update a custom web application across multiple backend servers.
- Using **bash scripting** to implement basic monitoring (health checks + logging).
- Implementing **health checks** and testing **failover** (primary → backup server).
- Structuring a GitHub repository with Terraform, Ansible, app code, and documentation.

---

## 🧱 Architecture Overview

### High-Level Architecture (Text)

```markdown
┌───────────────────────────────────────────────────────────────┐
│                        End Users (Clients)                    │
└───────────────────────────────┬───────────────────────────────┘
                                │
                                │ HTTPS (443) / HTTP (80 → 443)
                                ▼
                    ┌──────────────────────────┐
                    │   Nginx Load Balancer    │
                    │   (EC2 in Public Subnet) │
                    │ - SSL Termination        │
                    │ - Reverse Proxy          │
                    │ - Caching                │
                    │ - Monitoring Script      │
                    └────────────┬─────────────┘
                                 │
                                 │ HTTP (80) - Private Traffic
                                 ▼
            ┌──────────────────────────────────────────────┐
            │                Backend Tier (EC2)            │
            │  web-1 (AZ-a)  web-2 (AZ-b)   web-3 (backup) │
            │  - Nginx serving app content                 │
            │  - /health endpoint                          │
            └──────────────────────────────────────────────┘
```

You will document this architecture in `docs/architecture.md` and include failover + caching + monitoring test results.

---

## 📝 Requirements

### Part 1: Git Repository Setup (15 marks)

#### 1.1 Repository Structure (5 marks)

Create a well-organized Git repository adapted for this HA Nginx architecture:

```text
Project3/
├── README.md
├── .gitignore
├── terraform/
│   ├── main.tf
│   ├── variables.tf
│   ├── outputs.tf
│   ├── locals.tf
│   ├── terraform.tfvars.example
│   └── modules/
│       ├── network/
│       │   ├── main.tf
│       │   ├── variables.tf
│       │   └── outputs.tf
│       ├── security/
│       │   ├── main.tf
│       │   ├── variables.tf
│       │   └── outputs.tf
│       └── ec2/
│           ├── main.tf
│           ├── variables.tf
│           └── outputs.tf
├── ansible/
│   ├── ansible.cfg
│   ├── inventory/
│   │   └── hosts.ini              # Static inventory (EC2 public IPs)
│   ├── group_vars/
│   │   └── all.yml
│   ├── playbooks/
│   │   ├── configure-backends.yml
│   │   ├── configure-lb.yml
│   │   ├── deploy-app.yml
│   │   ├── update-app-rolling.yml
│   │   └── verify-health.yml
│   └── roles/
│       ├── backend_nginx/
│       │   ├── tasks/main.yml
│       │   └── templates/index.html.j2
│       ├── lb_nginx/
│       │   ├── tasks/main.yml
│       │   └── templates/nginx.conf.j2
│       ├── monitoring/
│       │   ├── tasks/main.yml
│       │   └── templates/monitor_backends.sh.j2
│       └── common/
│           └── tasks/main.yml
├── app/
│   └── static/
│       ├── index.html
│       └── assets/
└── docs/
    ├── architecture.md
    ├── ha-testing-guide.md
    ├── ssl-configuration.md
    ├── caching-testing.md
    ├── monitoring.md
    └── troubleshooting.md
```

**Tasks:**
- [ ] Create all necessary directories and placeholder files.  
- [ ] Document the structure in `README.md`.  
- [ ] Initialize Git repository and create the initial commit.  

**Deliverables:**
- Screenshot: `project3_part1_repository_structure.png`  
- Screenshot: `project3_part1_gitignore.png`  
- Screenshot: `project3_part1_initial_commit.png`  

---

#### 1.2 Git Branching Strategy (5 marks)

Use the same branching model taught in GitHub collaboration:

**Branch Structure:**
```text
main
├── staging
│   └── dev
└── feature/* (for new features / experiments)
```

**Branch Rules:**
- `dev` → used for testing changes (Terraform/Ansible/app).  
- `staging` → integration testing before final submission.  
- `main` → stable version for submission.  

**Tasks:**
- [ ] Create `dev` and `staging` branches from `main`.  
- [ ] Document branching strategy in `README.md`.  
- [ ] Create a sample feature branch (e.g., `feature/add-monitoring-script`).  

**Deliverables:**
- Screenshot: `project3_part1_git_branches.png`  
- Screenshot: `project3_part1_branching_diagram.png`  

---

#### 1.3 `.gitignore` Configuration (5 marks)

Use a `.gitignore` that excludes Terraform state, sensitive data, and temporary files:

```gitignore
# Terraform files
**/.terraform/*
*.tfstate
*.tfstate.*
*.tfvars
!*.tfvars.example
crash.log
crash.*.log

# Ansible files
*.retry
*.secret

# AWS credentials / keys
.aws/
*.pem
*.key

# IDE / OS
.vscode/
.idea/
*.swp
*.swo
*~
.DS_Store
Thumbs.db

# Logs
*.log
logs/

# Environment files
.env
.env.local
```

**Tasks:**
- [ ] Add `.gitignore` to the repo.  
- [ ] Verify no sensitive files or state files are tracked.  

**Deliverables:**
- Screenshot: `project3_part1_gitignore_content.png`  
- Screenshot: `project3_part1_git_status_clean.png`  

---

### Part 2: Terraform – Network, Security, EC2 (35 marks)

#### 2.1 Network Module – VPC & Multi-AZ Subnets (12 marks)

**Module:** `terraform/modules/network/`

Requirements:

- Create a VPC (e.g., `10.0.0.0/16`).  
- Create **at least two public subnets** and **two private subnets** across **two AZs**.  
- Attach an Internet Gateway.  
- Create:
  - Public route table → default route to IGW → associate with public subnets.
  - Private route table → no internet route required (keep internal) OR document limitations (backend updates via Ansible happen through public SSH).  
- Outputs:
  - VPC ID  
  - Public subnet IDs  
  - Private subnet IDs  
  - AZs used

**Tasks:**
- [ ] Implement `main.tf`, `variables.tf`, `outputs.tf` for network module.  
- [ ] Use tags: `Project`, `Environment`, `Tier=public/private`.  

**Deliverables:**
- Screenshot: `project3_part2_network_main.png`  
- Screenshot: `project3_part2_network_outputs.png`  

---

#### 2.2 Security Module – Security Groups (10 marks)

**Module:** `terraform/modules/security/`

Requirements:

Create **two security groups**:

1) **Load Balancer SG** (for the public Nginx LB EC2):
- Ingress:
  - 22 (SSH) from your IP only
  - 80 (HTTP) from anywhere
  - 443 (HTTPS) from anywhere
- Egress:
  - All

2) **Backend SG** (for backend servers):
- Ingress:
  - 22 (SSH) from your IP only
  - 80 (HTTP) **from Load Balancer SG only**
- Egress:
  - All

**Tasks:**
- [ ] Implement both security groups and rules.
- [ ] Use SG-to-SG rule for backend HTTP ingress (not CIDR).
- [ ] Tag resources.

**Deliverables:**
- Screenshot: `project3_part2_security_main.png`  
- Screenshot: `project3_part2_security_outputs.png`  

---

#### 2.3 EC2 Module – Load Balancer + Backends (13 marks)

**Module:** `terraform/modules/ec2/`

Provision **4 EC2 instances**:

- `lb-1` (public subnet, Load Balancer SG)
- `web-1` (private subnet AZ-a, Backend SG)
- `web-2` (private subnet AZ-b, Backend SG)
- `web-3` (private subnet, Backend SG) **backup server**

Requirements:

- Use variables for AMI ID, instance type, key name, subnet IDs, SG IDs.
- Tag instances with:
  - `Project`
  - `Environment`
  - `Role = lb / web`
  - `Name`

Outputs:
- Public IP of LB
- Private IPs of backends
- Public IPs of backends (only if you enable them; otherwise document SSH path)

**Tasks:**
- [ ] Implement resources for all four instances.
- [ ] Ensure instances are spread across AZs using subnet selection.

**Deliverables:**
- Screenshot: `project3_part2_ec2_main.png`  
- Screenshot: `project3_part2_ec2_outputs.png`  

---

### Part 3: Root Terraform & Variables (15 marks)

#### 3.1 Root Terraform Configuration (8 marks)

**Files:** `terraform/main.tf`, `variables.tf`, `outputs.tf`, `locals.tf`

Requirements:

- Configure AWS provider.
- Instantiate `network`, `security`, and `ec2` modules.
- Export:
  - Load balancer public IP
  - Backend private IPs
  - Helpful SSH commands (optional)

**Tasks:**
- [ ] Wire modules correctly (pass VPC/subnets → SGs → EC2).
- [ ] Add helpful outputs including:
  - `https://<lb-public-ip>` URL
  - backend IP list for Nginx upstream config

**Deliverables:**
- Screenshot: `project3_part3_main_tf.png`  
- Screenshot: `project3_part3_outputs_tf.png`  

---

#### 3.2 Variable Configuration (7 marks)

**Files:**  
- `terraform/variables.tf`  
- `terraform/terraform.tfvars.example`

Required variables (root):

- `aws_region`
- `project_name`
- `environment`
- `vpc_cidr_block`
- `public_subnet_cidr_blocks` (list)
- `private_subnet_cidr_blocks` (list)
- `availability_zones` (list of 2)
- `instance_type`
- `ami_id`
- `key_name`
- `my_ip`

**Tasks:**
- [ ] Add descriptions and validations where appropriate.
- [ ] Provide `terraform.tfvars.example` without secrets.

**Deliverables:**
- Screenshot: `project3_part3_variables_tf.png`  
- Screenshot: `project3_part3_tfvars_example.png`  

---

### Part 4: Ansible – Nginx LB, Backends, SSL, Caching, Health, Monitoring (20 marks)

#### 4.1 Ansible Inventory & Configuration (5 marks)

Use **static inventory** (`hosts.ini`) with Terraform outputs:

Example `ansible/inventory/hosts.ini`:

```ini
[lb]
lb-1 ansible_host=<lb-public-ip>

[web]
web-1 ansible_host=<web1-public-ip-or-ssh-path>
web-2 ansible_host=<web2-public-ip-or-ssh-path>
web-3 ansible_host=<web3-public-ip-or-ssh-path>

[all:vars]
ansible_user=ec2-user
```

**ansible/ansible.cfg:**
```ini
[defaults]
inventory = inventory/hosts.ini
host_key_checking = False
retry_files_enabled = False
stdout_callback = yaml
roles_path = roles

[privilege_escalation]
become = True
become_method = sudo
```

**group_vars/all.yml** must include:
- `health_path: "/health"`
- `web_root: "/usr/share/nginx/html"`
- `lb_cache_path: "/var/cache/nginx"`
- `monitor_log_path: "/var/log/backend_health.log"`
- backend private IPs list (from Terraform output) OR set them via `--extra-vars`

**Deliverables:**
- Screenshot: `project3_part4_hosts_ini.png`  
- Screenshot: `project3_part4_ansible_cfg.png`  
- Screenshot: `project3_part4_group_vars_all.png`  

---

#### 4.2 Backend Nginx Role + App Deployment (7 marks)

Roles:

1) **`roles/backend_nginx`**
- Install Nginx
- Enable and start Nginx
- Deploy a custom `index.html` template showing:
  - hostname
  - private IP
  - timestamp
  - server name (web-1/web-2/web-3)
- Implement `/health` endpoint returning HTTP 200

2) **`roles/common`**
- Install common packages (`vim`, `curl`, `git`)

**Playbooks:**
- `ansible/playbooks/configure-backends.yml`
- `ansible/playbooks/deploy-app.yml`

**Deliverables:**
- Screenshot: `project3_part4_backend_role_main.png`  
- Screenshot: `project3_part4_deploy_app_execution.png`  

---

#### 4.3 Load Balancer Nginx Role (SSL + LB + Backup + Cache) (6 marks)

Role: **`roles/lb_nginx`**

Requirements on `lb-1`:

- Install Nginx and OpenSSL.
- Generate a **self-signed certificate** on the LB server.
- Configure Nginx:
  - HTTP (80) → redirect to HTTPS (443)
  - HTTPS (443) terminates SSL
  - `upstream` block with:
    - `web-1` and `web-2` as primary
    - `web-3` as `backup`
  - Enable **proxy caching**:
    - `proxy_cache_path`
    - `proxy_cache` in location block
    - add response header `X-Cache-Status`

**Deliverables:**
- Screenshot: `project3_part4_lb_nginx_conf_template.png`  
- Screenshot: `project3_part4_configure_lb_execution.png`  

---

#### 4.4 Monitoring Role (LB Monitoring Script) (2 marks)

Role: **`roles/monitoring`**

Requirements:

- Deploy a monitoring script on the **load balancer**:
  - Script path: `/usr/local/bin/monitor_backends.sh`
  - Must check `/health` on each backend (private IP).
  - Must log results with timestamp to: `{{ monitor_log_path }}` (default `/var/log/backend_health.log`)
  - Must be executable (`chmod +x`).
- Schedule it using **cron** to run every 1 minute:
  - e.g., `* * * * * /usr/local/bin/monitor_backends.sh`

**Template:** `roles/monitoring/templates/monitor_backends.sh.j2`

**Minimum script requirements:**
- For each backend:
  - run `curl` to `http://<backend-ip>/health`
  - capture HTTP code
  - log to file: timestamp + backend + status
- Exit code:
  - exit 0 if at least one backend returns 200
  - exit 1 if all backends are down

**Deliverables:**
- Screenshot: `project3_part4_monitor_script_template.png`  
- Screenshot: `project3_part4_monitoring_role_main.png`  
- Screenshot: `project3_part4_cron_entry.png`  

---

#### 4.5 Rolling App Updates (serial) (Optional but graded) (0 marks extra inside Part 4, counted in Part 5 evidence)

Create `ansible/playbooks/update-app-rolling.yml`:

- Use `serial: 1` to update one backend at a time.
- Deploy updated app content.
- Restart Nginx if needed.
- Verify `/health` returns 200 before moving to next server.

**Deliverables:**
- Screenshot: `project3_part4_update_app_playbook.png`  
- Screenshot: `project3_part4_update_app_execution.png`  

---

### Part 5: High Availability, Caching, SSL & Monitoring Testing (15 marks)

#### 5.1 Failover Test (Backup Server) (6 marks)

Perform HA tests:

1. Confirm normal traffic alternates between `web-1` and `web-2`.
2. Stop Nginx on `web-1`:
   - Verify service still works via `web-2`.
3. Stop Nginx on `web-2`:
   - Verify LB now uses `web-3` (backup).
4. Restart Nginx on web-1 and web-2:
   - Verify traffic returns to web-1/web-2 and web-3 stops serving (backup only).

**Deliverables:**
- Screenshot: `project3_part5_web1_stopped.png`  
- Screenshot: `project3_part5_web2_stopped.png`  
- Screenshot: `project3_part5_backup_activated.png`  
- Screenshot: `project3_part5_services_restored.png`  

---

#### 5.2 Caching Test (4 marks)

Verify LB caching works:

- First request: header `X-Cache-Status: MISS`
- Second request: header `X-Cache-Status: HIT`
- Confirm cache directory on LB has files.

**Deliverables:**
- Screenshot: `project3_part5_cache_miss.png`  
- Screenshot: `project3_part5_cache_hit.png`  
- Screenshot: `project3_part5_cache_directory.png`  

---

#### 5.3 SSL Test (3 marks)

Verify SSL termination works on the Nginx LB:

- Confirm HTTPS works and HTTP redirects to HTTPS.
- Show certificate details (self-signed).

**Deliverables:**
- Screenshot: `project3_part5_http_redirect.png`  
- Screenshot: `project3_part5_ssl_certificate.png`  

---

#### 5.4 Monitoring Validation (2 marks)

Validate the monitoring script is working:

**Tasks:**
- [ ] Show the log file exists and contains entries.
- [ ] Stop Nginx on a backend and confirm the log shows failures for that backend.
- [ ] Bring it back up and confirm monitoring shows 200 again.

**Deliverables:**
- Screenshot: `project3_part5_monitor_log.png`  
- Screenshot: `project3_part5_monitor_log_failure.png`  
- Screenshot: `project3_part5_monitor_log_recovery.png`  

---

## 📦 Submission Guidelines

### 1. GitHub Repository

Example name:

```text
CC_<YourName>_<YourRollNumber>/Project-3-HA-WebApp
```

**Content:**
- Terraform code (`terraform/` + modules).
- Ansible code (`ansible/` + roles + playbooks).
- App code (`app/`).
- Documentation (`docs/`).
- Screenshots (`screenshots/` or `docs/screenshots/`).

Do **not** commit:
- `terraform.tfstate`, `.terraform/`, real `terraform.tfvars`.
- Credentials (`.aws/`, `*.pem`, `*.key`, `.env`, secrets).

### 2. PDF Report

Name:

```text
Project3_<YourName>_<YourRollNumber>.pdf
```

Suggested structure:

1. Cover Page  
2. Table of Contents  
3. Executive Summary  
4. Architecture Design (diagrams, components, data flow).  
5. Terraform Implementation (network, security, EC2 roles).  
6. Ansible Implementation (LB config, backend config, SSL, caching, monitoring).  
7. HA + caching + SSL + monitoring tests (method + results).  
8. Challenges & Solutions.  
9. Conclusion & Future Improvements.  
10. Appendices (snippets, logs, screenshots).  

---

## 📊 Grading Criteria (High Level)

### Excellent (90–100%)
- Multi-AZ VPC and correct SG isolation.
- Terraform modules clean and reusable.
- Nginx LB configured with SSL termination + caching + backup server.
- Monitoring script installed, scheduled, and logging correctly.
- Backend servers serve correct app pages and health checks.
- HA + caching + SSL + monitoring tests documented with clear evidence.

### Good (80–89%)
- Core architecture works; minor issues in tests or documentation.

### Satisfactory (70–79%)
- Basic LB + backends work; limited HA/caching/monitoring evidence.

### Needs Improvement (60–69%)
- Partial implementation; missing key HA behaviors.

### Unsatisfactory (<60%)
- Major components missing; no end-to-end success.

---

## 💡 Tips for Success

- Start with one backend and LB first, then add multi-AZ and backup.
- Use Terraform outputs to populate Ansible inventory easily.
- Make roles idempotent so reruns are safe.
- Test failover early so you can debug SG and Nginx upstream rules.

---

## 🐞 Common Issues & Solutions

**Issue:** LB shows 502 Bad Gateway  
- Backend IP wrong in upstream block
- Backend SG not allowing HTTP from LB SG
- Backend Nginx not running

**Issue:** Cache always MISS  
- Cache directory permissions wrong
- Cache not enabled in location block
- Restart LB Nginx after config changes

**Issue:** Monitoring script log not updating  
- Cron not installed/running
- Script not executable
- Wrong backend IPs in script template
- LB cannot reach backend private IPs (routing/SG)

**Issue:** HTTPS not working  
- Certificate/key paths wrong
- Nginx config syntax error → run `nginx -t`

---

## 📚 Resources (Course-Relevant)

- [Terraform AWS Provider](https://registry.terraform.io/providers/hashicorp/aws/latest/docs)
- [Terraform Modules](https://developer.hashicorp.com/terraform/language/modules)
- [Ansible Roles](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_reuse_roles.html)
- [Nginx Reverse Proxy](https://docs.nginx.com/nginx/admin-guide/web-server/reverse-proxy/)
- [Nginx Load Balancing](https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/)
- [Nginx Caching](https://docs.nginx.com/nginx/admin-guide/content-cache/content-caching/)

---

## ❓ FAQ

**Q: Can I use AWS ALB/ASG instead of Nginx LB?**  
A: No. This project must stay within the course scope and use Nginx load balancing on EC2.

**Q: Can I use only one AZ?**  
A: No. You must deploy across at least two Availability Zones.

**Q: Do I need a database?**  
A: No. Focus is on HA web-tier with Nginx LB + backend servers.

---

## 📜 Academic Integrity

- Group project (max 3 persons).  
- Do not copy other students’ work.  
- Cite any external references used.

---

## ⏰ Deadline

**Due Date:** 26-01-2026  
**Time:** 09:00 AM  

Late submissions follow your course policy.

---

## ✅ Final Checklist

### Terraform
- [ ] VPC + multi-AZ public/private subnets created.
- [ ] Security groups configured (LB public, backend restricted).
- [ ] 1 LB instance + 3 backend instances created and tagged.

### Ansible
- [ ] Backend Nginx role works with `/health`.
- [ ] LB Nginx role works with HTTPS redirect, SSL termination, caching, backup server.
- [ ] Monitoring role installs script + cron and writes logs.
- [ ] Rolling update playbook works (serial).

### Testing & Docs
- [ ] Failover tests completed with screenshots.
- [ ] Cache HIT/MISS verified.
- [ ] SSL redirect and certificate shown.
- [ ] Monitoring logs show healthy + failure + recovery.
- [ ] README + docs complete.
