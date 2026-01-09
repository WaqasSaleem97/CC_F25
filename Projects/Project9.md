# 📡 Project 9 – Infrastructure Monitoring and Log Collection System (Within Course Scope)

**Duration:** 8–10 hours  
**Total Marks:** 100  
**Submission Format:** GitHub Repository + PDF Report  

---

## 📋 Project Overview

Build a lightweight **infrastructure monitoring and log collection system** using **Terraform**, **Ansible**, and **custom Bash scripts** on Linux—strictly within the topics taught in this course:

- Linux administration + **bash scripting** (cron, services, logs)
- Git/GitHub repository practices
- AWS: **IAM, VPC, EC2**
- Terraform: modules, variables, outputs, state, environments
- Ansible: playbooks, roles, inventories, handlers, dynamic inventory
- Nginx: reverse proxy and serving simple pages (also produces useful logs)

> **Important scope rule:** This project does **not** use rsyslog forwarding, logrotate configuration, email/webhook alerting, GitHub Actions, or Python.  
> Instead, it uses **Bash + Ansible** to collect logs/metrics and publish a simple **static dashboard** via Nginx.

You will:

- Use **Terraform** to provision:
  - A **monitoring server** (Nginx dashboard + monitoring scripts).
  - Multiple **application servers** to be monitored.
- Use **Ansible** to:
  - Install and configure Nginx (to generate access/error logs and to serve a static dashboard).
  - Deploy **monitoring scripts** to the monitoring server.
  - Schedule monitoring jobs via cron.
  - Collect selected logs from application servers and store them on the monitoring server (using Ansible `fetch` / `copy` patterns).
- Develop **custom Bash monitoring scripts** to:
  - Collect system metrics: CPU, memory, disk, and basic network checks.
  - Check service status: Nginx (and any app service you deploy).
  - Perform basic HTTP health checks (curl).
  - Produce **daily/weekly reports** saved to files.
- Build a simple **static dashboard** served by Nginx on the monitoring server:
  - Shows latest status and links to latest reports/log snapshots.

**Key Concepts (Course Scope):** Monitoring with bash + cron, log collection with Ansible, Nginx logging, Terraform provisioning, documentation and runbooks

<img width="1756" height="2368" alt="diagram-export-12-29-2025-3_01_55-AM" src="https://github.com/user-attachments/assets/b97be9da-9e69-4d7a-ba13-f3f04767359e" />

---

## 🎯 Learning Objectives

By completing this project, you will:

- Build custom **monitoring tooling** using bash scripts and cron.
- Use **Terraform** to provision monitoring infrastructure (EC2 + VPC + SGs).
- Use **Ansible** to configure:
  - Nginx on servers.
  - Monitoring scripts and scheduled checks.
  - Log/metrics collection using Ansible automation.
- Serve a basic dashboard using **Nginx + static HTML**.
- Document a practical **monitoring architecture** and basic incident procedures.

---

## 🧱 Architecture Overview

### High-Level Architecture (Text)

```markdown
┌───────────────────────────────────────────────────────────────┐
│                     Administrators / Ops                      │
└───────────────────────────────┬───────────────────────────────┘
                                │
                                │ Git + Terraform + Ansible
                                ▼
                   ┌──────────────────────────────┐
                   │     Git Repository           │
                   │ - Terraform infra            │
                   │ - Ansible playbooks/roles    │
                   │ - Bash monitoring scripts    │
                   └───────────┬──────────────────┘
                               │
                               │ terraform apply / ansible-playbook
                               ▼
 ┌───────────────────────────────────────────────────────────────┐
 │                            AWS VPC                            │
 │                                                               │
 │  ┌───────────────────────────────┐                            │
 │  │ Monitoring Server (EC2)       │                            │
 │  │ - Nginx serves dashboard      │                            │
 │  │ - Cron runs checks            │                            │
 │  │ - Stores reports/log snapshots│                            │
 │  └───────────────┬──────────────┘                            │
 │                  │                                           │
 │                  │ SSH + HTTP checks + Ansible fetch         │
 │                  ▼                                           │
 │        ┌─────────────────────────────────────────┐          │
 │        │ Application Servers (N)                  │          │
 │        │ - Nginx + sample app                     │          │
 │        │ - local logs (/var/log/nginx/*)          │          │
 │        │ - health endpoint (/health)              │          │
 │        └─────────────────────────────────────────┘          │
 │                                                               │
 └───────────────────────────────────────────────────────────────┘
```

---

## 📝 Requirements

### Part 1: Git Repository & Structure (15 marks)

#### 1.1 Repository Structure (5 marks)

Use Project1 as a template and adapt for monitoring + log collection:

```text
Project9/
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
│       ├── network/              # VPC, subnets, SGs
│       │   ├── main.tf
│       │   ├── variables.tf
│       │   └── outputs.tf
│       ├── monitoring-server/    # Monitoring EC2
│       │   ├── main.tf
│       │   ├── variables.tf
│       │   └── outputs.tf
│       └── app-servers/          # N app servers to monitor
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
│   │   ├── configure-app-servers.yml
│   │   ├── configure-monitoring-server.yml
│   │   ├── collect-logs.yml
│   │   └── generate-report.yml
│   └── roles/
│       ├── nginx-app/            # Nginx + /health on app servers
│       │   ├── tasks/main.yml
│       │   └── templates/nginx.conf.j2
│       ├── monitoring-tools/     # Bash scripts + cron on monitoring server
│       │   ├── tasks/main.yml
│       │   └── files/
│       │       ├── collect-metrics.sh
│       │       ├── check-services.sh
│       │       ├── http-health-check.sh
│       │       ├── build-dashboard.sh
│       │       └── generate-report.sh
│       └── dashboard/            # Static HTML served by Nginx
│           ├── tasks/main.yml
│           └── templates/index.html.j2
├── scripts/
│   ├── run-all-health-checks.sh
│   ├── run-daily-report.sh
│   └── run-weekly-report.sh
└── web-ui/
    ├── index.html                # generated/updated by scripts
    └── assets/
        ├── css/
        └── js/
```

**Tasks:**
- [ ] Create directory structure and placeholder files.
- [ ] Initialize Git and create the initial commit.

**Deliverables:**
- Screenshot: `project9_part1_repository_structure.png`  
- Screenshot: `project9_part1_initial_commit.png`  

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

# Credentials
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
- [ ] Confirm no state, secrets, or keys are tracked.

**Deliverables:**
- Screenshot: `project9_part1_gitignore_content.png`  
- Screenshot: `project9_part1_git_status_clean.png`  

---

#### 1.3 Branching Strategy (5 marks)

Simple model:

```text
main    (stable monitoring setup)
└── dev (active changes)
    └── feature/* (experimental)
```

**Tasks:**
- [ ] Create `dev` branch from `main`.
- [ ] Use PRs for `feature/*` → `dev` → `main`.

**Deliverables:**
- Screenshot: `project9_part1_git_branches.png`

---

### Part 2: Terraform Infrastructure (30 marks)

#### 2.1 Network Module – VPC & Subnets (10 marks)

**Module:** `terraform/modules/network/`

Requirements:

- VPC with CIDR (e.g., `10.0.0.0/16`).
- Public subnet for monitoring server (to access dashboard via browser).
- Private subnets for app servers.
- Internet Gateway + route tables.
- Security Groups:
  - Monitoring SG: allow HTTP(80) from your IP (or 0.0.0.0/0 for dev), SSH from your IP.
  - App SG: allow HTTP(80) from monitoring SG only; SSH from your IP only.

**Outputs:**
- VPC ID, subnet IDs, SG IDs.

**Deliverables:**
- Screenshot: `project9_part2_network_main.png`  
- Screenshot: `project9_part2_network_outputs.png`  

---

#### 2.2 Monitoring Server Module (10 marks)

**Module:** `terraform/modules/monitoring-server/`

Requirements:

- One EC2 instance:
  - `Role=monitoring`
  - Public IP enabled
- Output:
  - Monitoring public IP/DNS

**Tasks:**
- [ ] Implement monitoring server provisioning and tagging.

**Deliverables:**
- Screenshot: `project9_part2_monitoring_server_main.png`  
- Screenshot: `project9_part2_monitoring_server_outputs.png`  

---

#### 2.3 Application Servers Module (10 marks)

**Module:** `terraform/modules/app-servers/`

Requirements:

- N EC2 app servers (configurable count).
- Tags:
  - `Role=app`
  - `Environment=dev/staging/production`
- Output:
  - list of app private IPs

**Tasks:**
- [ ] Implement module and root integration.

**Deliverables:**
- Screenshot: `project9_part2_app_servers_main.png`  
- Screenshot: `project9_part2_app_servers_outputs.png`  

---

### Part 3: Ansible – Nginx, Monitoring Scripts, Log Collection (30 marks)

#### 3.1 Configure App Servers (10 marks)

**Role:** `roles/nginx-app`

Requirements:

- Install Nginx.
- Configure:
  - `/health` endpoint returning HTTP 200.
  - A simple home page identifying the server.
- Ensure Nginx access/error logs exist at:
  - `/var/log/nginx/access.log`
  - `/var/log/nginx/error.log`

Playbook: `configure-app-servers.yml`

**Tasks:**
- [ ] Apply role to all app servers.
- [ ] Verify `/health` works on each app server.

**Deliverables:**
- Screenshot: `project9_part3_nginx_app_role.png`  
- Screenshot: `project9_part3_health_check_on_apps.png`  

---

#### 3.2 Configure Monitoring Server (10 marks)

**Role:** `roles/monitoring-tools` + `roles/dashboard`

Requirements:

- Install Nginx (to serve dashboard).
- Deploy monitoring scripts to `/usr/local/bin/`:
  - `collect-metrics.sh`: CPU, memory, disk.
  - `check-services.sh`: `systemctl is-active nginx` and basic process checks.
  - `http-health-check.sh`: curl each app `/health`.
  - `generate-report.sh`: build a report file with current status and metrics.
  - `build-dashboard.sh`: generate/update `web-ui/index.html` using latest report and results.
- Schedule cron jobs:
  - Every 5 minutes: metrics + health checks + dashboard update
  - Daily: generate daily report
  - Weekly: generate weekly report

Playbook: `configure-monitoring-server.yml`

**Deliverables:**
- Screenshot: `project9_part3_monitoring_scripts.png`  
- Screenshot: `project9_part3_cron_entries.png`  

---

#### 3.3 Log Collection Using Ansible (10 marks)

**Playbook:** `ansible/playbooks/collect-logs.yml`

Approach (course-scope):

- Use Ansible to **fetch** logs from app servers and store them on the monitoring server (or store them in repo for evidence).
- Collect at minimum:
  - `/var/log/nginx/access.log`
  - `/var/log/nginx/error.log`

**Tasks:**
- [ ] Implement `collect-logs.yml` using `ansible.builtin.fetch`.
- [ ] Organize collected logs by host and date.

**Deliverables:**
- Screenshot: `project9_part3_collect_logs_playbook.png`  
- Screenshot: `project9_part3_logs_collected_tree.png`  

---

### Part 4: Dashboard & Reporting (15 marks)

#### 4.1 Simple Web Dashboard (8 marks)

Serve `web-ui/` via Nginx on the monitoring server.

Dashboard must show:
- List of app servers and their last known status (UP/DOWN)
- Last metrics snapshot (CPU/mem/disk) from monitoring server
- Links to latest collected logs and reports

**Deliverables:**
- Screenshot: `project9_part4_dashboard_ui.png`  
- Screenshot: `project9_part4_dashboard_links_working.png`  

---

#### 4.2 Automated Reporting (7 marks)

Instead of sending emails/webhooks (out of scope), store reports as files:

- Daily report saved to:
  - `/var/www/html/reports/daily-YYYY-MM-DD.txt`
- Weekly report saved to:
  - `/var/www/html/reports/weekly-YYYY-WW.txt`

**Tasks:**
- [ ] Implement daily and weekly report scripts.
- [ ] Confirm cron runs and files appear.

**Deliverables:**
- Screenshot: `project9_part4_daily_report_sample.png`  
- Screenshot: `project9_part4_weekly_report_sample.png`  

---

### Part 5: Documentation & Basic Runbooks (10 marks)

#### 5.1 Documentation (5 marks)

In `README.md` and/or `docs/` (optional folder), document:

- Architecture diagram and components
- How to provision infra (Terraform)
- How to configure servers (Ansible)
- How to view dashboard and reports

**Deliverables:**
- Screenshot: `project9_part5_readme_sections.png`  

---

#### 5.2 Basic Incident Procedures (5 marks)

Create a short `docs/incident-procedures.md` (or include in README) with steps for:

- App server down (Nginx stopped)
- Disk space high on monitoring server
- `/health` check failing

Keep it practical and command-based:
- `systemctl status nginx`
- `df -h`
- `tail -n 50 /var/log/nginx/error.log`

**Deliverables:**
- Screenshot: `project9_part5_incident_procedures_doc.png`  

---

## 📦 Submission Guidelines

### 1. GitHub Repository

Example:

```text
CC_<YourName>_<YourRollNumber>/Project-9-Monitoring-Log-Collection
```

- ✅ Include: Terraform `.tf`, Ansible roles/playbooks, scripts, web-ui, docs, screenshots.
- ❌ Exclude: state files, `.terraform/`, real tfvars, secrets, keys.

---

### 2. PDF Report

Name:

```text
Project9_<YourName>_<YourRollNumber>.pdf
```

Suggested structure:

1. Cover Page  
2. Table of Contents  
3. Executive Summary  
4. Architecture Design (network + monitoring flow)  
5. Terraform Implementation  
6. Ansible Implementation  
7. Monitoring Scripts & Cron  
8. Log Collection Evidence  
9. Dashboard & Reports Evidence  
10. Incident Procedures  
11. Challenges & Solutions  
12. Conclusion  
13. Appendices (scripts, configs, screenshots)

---

## 📊 Grading Criteria (High Level)

### Excellent (90–100%)
- Monitoring and app servers provisioned correctly.
- Health checks + metrics collection automated via cron.
- Logs collected reliably using Ansible.
- Dashboard shows correct and updated status.
- Reports generated daily/weekly with evidence.
- Documentation and incident procedures are clear.

### Good (80–89%)
- Core features work; minor gaps in dashboard/reporting polish.

### Satisfactory (70–79%)
- Basic monitoring works; limited automation or evidence.

### Needs Improvement (60–69%)
- Partial system; logs or monitoring not consistent.

### Unsatisfactory (<60%)
- No working monitoring/log collection flow end-to-end.

---

## 💡 Tips for Success

- Start with 1 app server and prove:
  - `/health` works
  - logs exist
  - monitoring server can curl the app
- Then scale to multiple app servers.
- Keep scripts simple and use absolute paths in cron.

---

## 🐞 Common Issues & Solutions

**Issue:** Monitoring server cannot reach app servers  
- Check SG: app allows HTTP from monitoring SG.
- Verify correct private IPs in health check script.

**Issue:** Cron runs but output files not updated  
- Use absolute paths.
- Redirect output to a log file for debugging.

**Issue:** Logs not collected  
- Ensure Ansible has SSH access.
- Confirm paths for Nginx logs.

---

## 📚 Resources

- [Nginx Logging](https://nginx.org/en/docs/http/ngx_http_log_module.html)  
- [Ansible fetch module](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/fetch_module.html)  
- [Terraform AWS Provider](https://registry.terraform.io/providers/hashicorp/aws/latest/docs)  
- [Ansible Documentation](https://docs.ansible.com/)  

---

## ❓ FAQ

**Q1: Why are we not using rsyslog or logrotate?**  
A: They are not part of the course outline. We achieve log collection using **Ansible fetch** and keep a simple, auditable workflow.

**Q2: Why no email/Slack alerts?**  
A: External alert integrations are out of scope. Instead, we generate reports and show status on a dashboard served by Nginx.

**Q3: How do I prove the system works?**  
A: Show screenshots of:
- `/health` OK for each app server
- collected logs on monitoring server
- dashboard showing UP/DOWN
- daily/weekly report files generated by cron

---

## ✅ Final Checklist

### Infrastructure
- [ ] VPC + subnets + SGs created via Terraform.
- [ ] Monitoring server + N app servers provisioned.

### Configuration
- [ ] Nginx configured on app servers with `/health`.
- [ ] Monitoring scripts installed on monitoring server.
- [ ] Cron jobs run and update dashboard/reports.

### Log Collection
- [ ] Ansible collects logs from app servers regularly.
- [ ] Logs are organized per-host and per-date.

### Dashboard & Docs
- [ ] Nginx serves the dashboard and reports.
- [ ] Documentation and incident procedures completed.
