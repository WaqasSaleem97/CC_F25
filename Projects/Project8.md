# 🔵🟢 Project 8 – Blue-Green Deployment Infrastructure (Within Course Scope)

**Duration:** 8–10 hours  
**Total Marks:** 100  
**Submission Format:** GitHub Repository + PDF Report  

---

## 📋 Project Overview

Implement a **blue-green deployment infrastructure** for **zero-downtime application updates** using **Terraform** and **Ansible**, staying strictly within the tools and services taught in the course:

- Linux administration + bash scripting
- Git & GitHub workflow (branches/PRs)
- AWS: **IAM, VPC, EC2** (and Security Groups)
- Terraform: modules, variables, outputs, state
- Ansible: roles, playbooks, inventories, automation
- Nginx: reverse proxy, load balancing, backup, SSL termination, caching (where relevant)

> **Important scope rule:** This project uses **Nginx-based traffic switching** (not AWS ALB/ASG/Target Groups).

You will:

- Use **Terraform** to provision:
  - Two **identical environments**: **Blue** and **Green** (each is a group of EC2 web servers).
  - A **single Nginx Load Balancer** EC2 instance that can route traffic to either Blue or Green.
  - A multi-AZ VPC with public/private subnets.
- Use **Ansible** to:
  - Deploy application versions to the **inactive** environment (e.g., Green while Blue is live).
  - Run **smoke tests** and validation on the new deployment.
  - Keep Nginx configuration **synchronized** across environments.
- Implement **automated health checks** to verify the new version before switching traffic.
- Implement a **traffic switching mechanism**:
  - Switch the LB upstream from Blue → Green (and back) by updating an Nginx config value and reloading Nginx.
- Implement **rollback procedures**:
  - Quickly switch traffic back if problems are detected.
- Create **deployment orchestration scripts**:
  - Automate the full workflow:
    - Deploy → Test → Switch → Monitor → Rollback (if needed)
- Test the workflow by deploying **multiple application versions**.
- Document:
  - Deployment procedures.
  - Rollback decision tree.

**Key Concepts (Course Scope):** Blue-green deployment strategy, zero-downtime updates using Nginx upstream switching, automated smoke tests, rollback procedures, Git-based change workflow, Terraform+Ansible integration

<img width="1480" height="2206" alt="diagram-export-12-29-2025-2_54_17-AM" src="https://github.com/user-attachments/assets/6196db7f-eb62-410e-bf19-bb41995c319e" />

---

## 🎯 Learning Objectives

By completing this project, you will:

- Design and implement **blue-green infrastructure** on AWS using **EC2 + Nginx** (no ALB/ASG).
- Use **Terraform** to:
  - Provision VPC, subnets, security groups.
  - Provision one **Nginx load balancer** EC2 instance (public).
  - Provision two **backend pools**:
    - Blue: 2 EC2 instances (private)
    - Green: 2 EC2 instances (private)
- Use **Ansible** to:
  - Deploy application versions to a selected environment (Blue or Green).
  - Run automated smoke tests and health checks.
  - Keep Nginx config consistent.
- Implement a safe workflow:
  - Deploy to inactive color → validate → switch traffic → rollback if needed.
- Write bash scripts to orchestrate the deployment and switching.

---

## 🧱 Architecture Overview

### High-Level Architecture (Text)

```markdown
┌───────────────────────────────────────────────────────────────┐
│                        End Users                              │
└───────────────────────────────┬───────────────────────────────┘
                                │
                                │ HTTP / HTTPS
                                ▼
 ┌───────────────────────────────────────────────────────────────┐
 │               Nginx Load Balancer (EC2 - Public)              │
 │              ┌──────────────────┴───────────────────┐         │
 │              │                                      │         │
 │     Upstream Pool (Blue)                    Upstream Pool (Green)
 │     - Blue instances                         - Green instances
 │     - Active at a time                        - Idle at a time
 └───────────────────────────────────────────────────────────────┘
                 │                                   │
                 ▼                                   ▼
 ┌────────────────────────────┐         ┌────────────────────────┐
 │   Blue Environment         │         │   Green Environment    │
 │   - 2 EC2 + Nginx + App v1 │         │   - 2 EC2 + Nginx + App v2│
 └────────────────────────────┘         └────────────────────────┘

Deployment Flow:
1. Blue live, Green idle → deploy new version to Green.
2. Run smoke tests on Green (directly via private IP from LB or via Ansible).
3. If healthy, switch LB upstream to Green (reload Nginx).
4. Blue becomes idle (rollback target).
5. If issues, switch LB back to Blue.
```

---

## 📝 Requirements

### Part 1: Git Repository & Structure (15 marks)

#### 1.1 Repository Structure (5 marks)

Use Project1 as a template; adapt for blue-green infrastructure and orchestration:

```text
Project8/
├── README.md
├── .gitignore
├── scripts/
│   ├── blue-green-deploy.sh      # Orchestrate full deploy (inactive color)
│   ���── blue-green-switch.sh      # Switch traffic between blue/green
│   ├── blue-green-rollback.sh    # Roll traffic back to previous color
│   └── smoke-tests.sh            # Run smoke tests against inactive env
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
│       ├── network/              # VPC, subnets, routes, IGW
│       │   ├── main.tf
│       │   ├── variables.tf
│       │   └── outputs.tf
│       ├── lb-ec2/               # Single Nginx LB instance
│       │   ├── main.tf
│       │   ├── variables.tf
│       │   └── outputs.tf
│       └── bluegreen-ec2/         # Parameterized EC2 pool module
│           ├── main.tf
│           ├── variables.tf
│           └── outputs.tf
├── ansible/
│   ├── ansible.cfg
│   ├── inventory/
│   │   ├── blue_aws_ec2.yml
│   │   ├── green_aws_ec2.yml
│   │   └── lb_aws_ec2.yml
│   ├── playbooks/
│   │   ├── configure-lb.yml
│   │   ├── configure-web.yml
│   │   ├── deploy-app-blue.yml
│   │   ├── deploy-app-green.yml
│   │   ├── smoke-test-blue.yml
│   │   ├── smoke-test-green.yml
│   │   └── sync-nginx-config.yml
│   └── roles/
│       ├── nginx-lb/             # LB Nginx reverse proxy config (switchable)
│       │   ├── tasks/main.yml
│       │   └── templates/lb.conf.j2
│       ├── nginx-web/            # Web Nginx serving the app
│       │   ├── tasks/main.yml
│       │   └── templates/web.conf.j2
│       └── app-deploy/           # Deploy application versions
│           ├── tasks/main.yml
│           └── vars/
│               ├── blue.yml
│               └── green.yml
└── docs/
    ├── architecture.md
    ├── deployment-procedures.md
    ├── rollback-decision-tree.md
    └── testing-strategy.md
```

**Tasks:**
- [ ] Create directories and placeholder files.  
- [ ] Initialize Git and create initial commit.

**Deliverables:**
- Screenshot: `project8_part1_repository_structure.png`  
- Screenshot: `project8_part1_initial_commit.png`  

---

#### 1.2 .gitignore (5 marks)

Use a `.gitignore` similar to Project1:

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
- Screenshot: `project8_part1_gitignore_content.png`  
- Screenshot: `project8_part1_git_status_clean.png`  

---

#### 1.3 Branching & Simple Workflow (5 marks)

Use a simple pattern:

```text
main    (stable infra + deployment scripts)
└── dev (active development of infra & playbooks)
    └── feature/* (short-lived)
```

**Tasks:**
- [ ] Create `dev` branch from `main`.
- [ ] Use PRs from `feature/*` → `dev` → `main`.
- [ ] Document workflow in `README.md`.

**Deliverables:**
- Screenshot: `project8_part1_git_branches.png`  

---

### Part 2: Terraform Infrastructure – Blue & Green (35 marks)

#### 2.1 Network Module – VPC, Subnets, SGs (12 marks)

**Module:** `terraform/modules/network/`

Requirements:

- VPC with CIDR (e.g., `10.0.0.0/16`).
- Public subnets:
  - For the LB instance (multi-AZ recommended).
- Private subnets:
  - For Blue and Green web servers (multi-AZ).
- Internet Gateway + route tables.
- Security Groups:
  - LB SG: allow HTTP/HTTPS from internet; SSH from admin IP.
  - Web SG: allow HTTP from LB SG only; SSH from admin IP (or via LB if documented).

**Outputs:**
- VPC ID, subnet IDs, SG IDs.

**Deliverables:**
- Screenshot: `project8_part2_network_main.png`  
- Screenshot: `project8_part2_network_outputs.png`  

---

#### 2.2 LB EC2 Module – Nginx Traffic Router (10 marks)

**Module:** `terraform/modules/lb-ec2/`

Requirements:

- One EC2 instance (public) running Nginx load balancer.
- Tags:
  - `Role=lb`
- Outputs:
  - LB public IP/DNS

**Tasks:**
- [ ] Implement LB instance + SG + tagging.

**Deliverables:**
- Screenshot: `project8_part2_lb_main.png`  
- Screenshot: `project8_part2_lb_outputs.png`  

---

#### 2.3 Blue/Green EC2 Pools (13 marks)

**Module:** `terraform/modules/bluegreen-ec2/`

A parameterized module instantiated twice:

- `blue_pool` (environment = "blue")
- `green_pool` (environment = "green")

Requirements:

- 2 EC2 instances in private subnets for each pool.
- Tagging:
  - `Environment=blue|green`
  - `Role=web`
- Outputs:
  - Blue private IPs
  - Green private IPs

**Root module wiring:**
- Instantiate module twice and export both IP lists for Ansible.

**Deliverables:**
- Screenshot: `project8_part2_bluegreen_module_main.png`  
- Screenshot: `project8_part2_blue_green_outputs.png`  

---

### Part 3: Ansible Configuration – Nginx & Applications (25 marks)

#### 3.1 Inventories & Common Config (5 marks)

Use **aws_ec2** dynamic inventory:

- `blue_aws_ec2.yml` filters by `Environment=blue` and `Role=web`.
- `green_aws_ec2.yml` filters by `Environment=green` and `Role=web`.
- `lb_aws_ec2.yml` filters by `Role=lb`.

**Tasks:**
- [ ] Implement inventories for Blue, Green, and LB.
- [ ] Prove host discovery with `ansible-inventory --graph`.

**Deliverables:**
- Screenshot: `project8_part3_ansible_inventory_blue.png`  
- Screenshot: `project8_part3_ansible_inventory_green.png`  
- Screenshot: `project8_part3_ansible_inventory_lb.png`  

---

#### 3.2 Nginx Roles – Consistent Configs (10 marks)

**Role:** `roles/nginx-web`

Requirements:
- Install Nginx on Blue and Green web servers.
- Deploy identical web config:
  - `/health` endpoint returns 200.
  - Serve app from `/var/www/app`.

**Role:** `roles/nginx-lb`

Requirements:
- Install Nginx + OpenSSL on LB.
- Configure:
  - HTTP → HTTPS redirect
  - SSL termination (self-signed)
  - An upstream that can be switched between:
    - `blue_upstream` (blue private IPs)
    - `green_upstream` (green private IPs)
  - Use a single variable `active_color` to choose which upstream is live.

Playbook `sync-nginx-config.yml` should ensure repeatability.

**Deliverables:**
- Screenshot: `project8_part3_nginx_web_role_main.png`  
- Screenshot: `project8_part3_nginx_lb_conf_template.png`  

---

#### 3.3 App Deploy & Smoke Test Playbooks (10 marks)

**Role:** `roles/app-deploy`

- Deploy application versions (simple HTML or static site):
  - Blue and Green use different `app_version` values.

Playbooks:
- `deploy-app-blue.yml`
- `deploy-app-green.yml`

Smoke tests:
- Must verify:
  - HTTP 200 from inactive environment
  - response contains correct version string
  - `/health` returns 200

**Deliverables:**
- Screenshot: `project8_part3_app_deploy_role_main.png`  
- Screenshot: `project8_part3_smoke_tests_execution.png`  

---

### Part 4: Traffic Switching & Rollback (20 marks)

#### 4.1 Traffic Switching Mechanism (10 marks)

Implement switching **without AWS ALB**, using Nginx config reload:

Two allowed approaches (choose one):

1) **Ansible-driven switch (recommended):**
   - Playbook updates LB config variable `active_color=blue|green`
   - Reload Nginx (`nginx -t` then `systemctl reload nginx`)

2) **Script-driven switch:**
   - `scripts/blue-green-switch.sh` calls Ansible playbook with `-e active_color=...`

**Requirements:**
- Switch Blue → Green and Green → Blue
- No downtime (Nginx reload, not restart)

**Deliverables:**
- Screenshot: `project8_part4_switch_script.png`  
- Screenshot: `project8_part4_switch_run_output.png`  

---

#### 4.2 Orchestrated Deployment & Rollback Scripts (10 marks)

**Script:** `scripts/blue-green-deploy.sh`

Flow:
1. Identify current active color (store in a file `scripts/active_color.txt` OR read from Ansible var file).
2. Set inactive color.
3. Deploy to inactive color (Ansible playbook).
4. Smoke test inactive color.
5. If pass → switch LB to inactive color.
6. If fail → do not switch, exit non-zero.

**Script:** `scripts/blue-green-rollback.sh`

Flow:
- Switch LB back to previous color.
- Re-run smoke test to confirm rollback environment is healthy.

**Deliverables:**
- Screenshot: `project8_part4_blue_green_deploy_script.png`  
- Screenshot: `project8_part4_blue_green_rollback_script.png`  
- Screenshot: `project8_part4_full_workflow_run.png`  

---

## 📦 Submission Guidelines

### 1. GitHub Repository

Example:

```text
CC_<YourName>_<YourRollNumber>/Project-8-Blue-Green-Deployment
```

- ✅ Include: Terraform modules, Ansible roles/playbooks, scripts, docs, screenshots.
- ❌ Exclude: state files, `.terraform/`, real tfvars, secrets, private keys.

---

### 2. PDF Report

Name:

```text
Project8_<YourName>_<YourRollNumber>.pdf
```

Suggested structure:

1. Cover Page  
2. Table of Contents  
3. Executive Summary  
4. Architecture Design  
5. Terraform Implementation (network, LB EC2, blue/green pools)  
6. Ansible Implementation (Nginx LB/Web, app deploy, smoke tests)  
7. Blue-Green Workflow (step-by-step logs)  
8. Traffic Switching & Health Checks  
9. Rollback Decision Tree  
10. Test Results for Multiple Versions  
11. Challenges & Lessons Learned  
12. Conclusion & Future Improvements  
13. Appendices (scripts, configs, logs)  

---

## 📊 Grading Criteria (High Level)

### Excellent (90–100%)
- Blue/Green pools fully working with Nginx LB switching.
- Deploy-to-inactive + smoke tests gate switching.
- Rollback works quickly and reliably.
- Scripts are robust (clear output, exit codes).
- Documentation and decision trees are clear and complete.

### Good (80–89%)
- Core blue-green works; minor gaps in automation or documentation.

### Satisfactory (70–79%)
- Blue-green infra works but switching or testing is partially manual.

### Needs Improvement (60–69%)
- Partial infra; unstable switching or missing rollback.

### Unsatisfactory (<60%)
- No working zero-downtime deployment path.

---

## 💡 Tips for Success

- Start with Blue only, verify Nginx + app works.
- Clone config for Green using the same module + tags.
- Keep Nginx config identical; change only app version content.
- Use `nginx -t` before reload to prevent breaking production traffic.

---

## 🐞 Common Issues & Solutions

**Issue:** Health checks fail after switching  
- Ensure `/health` exists on both Blue and Green.
- Confirm SG rules allow LB → web traffic.

**Issue:** Deploy script switches even if tests fail  
- Check exit codes and stop on error (`set -e`).

**Issue:** Config drift between Blue and Green  
- Use one Ansible role and variable-driven differences only (app version).

---

## ❓ FAQ

**Q: Can I use AWS ALB/ASG instead of Nginx switching?**  
A: No. This project must stay within the course scope. Use **Nginx-based switching** on EC2.

**Q: How do I achieve “zero downtime” without ALB?**  
A: Use **Nginx reload** (`systemctl reload nginx`) after validating config (`nginx -t`). Reload swaps configs without dropping existing connections.

**Q: What should my “application” be?**  
A: A simple static page or minimal site that prints the version (v1/v2) is enough to prove deployment, switching, and rollback.

**Q: Where do I store which color is active?**  
A: Use a simple text file (`scripts/active_color.txt`) or an Ansible vars file and update it as part of the switch process.

---

## ✅ Final Checklist

### Infrastructure
- [ ] VPC, subnets, SGs created.
- [ ] LB EC2 created (public).
- [ ] Blue pool + Green pool created (private).

### Configuration & Apps
- [ ] Nginx installed/configured on LB and all web servers.
- [ ] App versions deployed to Blue and Green separately.
- [ ] `/health` works everywhere.

### Deployment Workflow
- [ ] Deploy to inactive environment works.
- [ ] Smoke tests gate switching.
- [ ] Switch and rollback tested both ways.

### Documentation
- [ ] Deployment procedures and rollback decision tree completed.
- [ ] PDF report includes evidence of multiple versions and switching.
