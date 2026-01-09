# 🧩 Project 5 – Infrastructure as Code with Git-Based Workflow (Within Course Scope)

**Duration:** 8–10 hours  
**Total Marks:** 100  
**Submission Format:** GitHub Repository + PDF Report  

---

## 📋 Project Overview

Implement a complete **Infrastructure as Code (IaC) workflow** using **Git**, **Terraform**, and **Ansible**, with an emphasis on **Git-based workflows, code reviews, environment promotion, state management, and change management** — using only topics covered in this course.

You will:

- Use **Terraform** to define and provision a **web application infrastructure** on AWS:
  - VPC, subnets, route tables, Internet Gateway.
  - Security Groups (least privilege).
  - EC2 instances (web/app roles).
  - **NAT Gateway (borderline but allowed)** to enable private subnet outbound access for updates.
- Use **Git/GitHub** to drive workflow:
  - A **protected main branch**.
  - Infrastructure changes go through **Pull Requests** and **code reviews** before applying.
  - Use **Git tags** to version infrastructure releases.
- Manage **Terraform state**:
  - Start with local state during initial development.
  - Then **migrate to an S3 remote backend** (taught / allowed).
- Use **manual quality gates** (course-scope) before merging PRs:
  - `terraform fmt -recursive`
  - `terraform validate`
  - `terraform plan`
  - (No pre-commit framework required; run these commands as part of your PR checklist.)
- Use **Ansible** to deploy a sample application **after infrastructure provisioning**.
- Implement a **test → staging → production** flow:
  - Infrastructure changes are tested in a **test environment** before promotion.
- Create **runbooks** for:
  - Provisioning.
  - Updates.
  - Rollbacks / disaster recovery.
- Document a **change management process** with approvals and release tagging.
- Test **disaster recovery** by destroying and fully recreating the infrastructure from code.

**Key Concepts (Course Scope + Allowed Borderline):** Infrastructure as Code, Git workflows, PR reviews, Terraform modules/state/outputs, S3 remote backend, VPC/EC2/IAM, NAT Gateway (borderline), Ansible deployment, Nginx, documentation/runbooks

<img width="1797" height="1312" alt="diagram-export-12-29-2025-2_32_59-AM" src="https://github.com/user-attachments/assets/58bacfe7-b3ac-40ad-aa5b-b9c4b6c97589" />

---

## 🎯 Learning Objectives

By completing this project, you will demonstrate:

- Designing a **Git-driven IaC workflow** for Terraform and Ansible.
- Implementing:
  - **Branch protection** and code review policies for infrastructure changes.
  - Manual PR quality gates: `fmt`, `validate`, `plan`.
  - **Terraform state management** including **migration to S3 remote backend**.
  - **Environment promotion** (test → staging → prod) using branches and tags.
- Writing **Ansible playbooks/roles** that deploy applications only after infra is ready.
- Creating **runbooks** and **change management documentation**.
- Testing **disaster recovery** by rebuilding from source code.

---

## 🧱 Architecture Overview

### High-Level Architecture (Text)

```markdown
┌───────────────────────────────────────────────────────────────┐
│                        Developers / DevOps                    │
└───────────────────────────────┬───────────────────────────────┘
                                │
                                │ Git push (feature → dev → staging → main)
                                ▼
                   ┌─────────────────────────��────┐
                   │       GitHub Repo            │
                   │ - main / staging / dev       │
                   │ - PR reviews + approvals     │
                   │ - tags/releases              │
                   └───────────┬──────────────────┘
                               │
                               │ Manual pipeline (CLI):
                               │ terraform fmt/validate/plan/apply
                               │ then ansible-playbook deploy
                               ▼
 ┌───────────────────────────────────────────────────────────────┐
 │                         Terraform State                       │
 │                                                               │
 │   ┌─────────────────────┐   ┌──────────────────────────────┐  │
 │   │ Local State         │   │ Remote Backend (S3)          │  │
 │   │ (initial)           │   │ - Shared state for team      │  │
 │   └─────────────────────┘   └──────────────────────────────┘  │
 └───────────────────────────────────────────────────────────────┘
                               │
                               ▼
 ┌───────────────────────────────────────────────────────────────┐
 │                         AWS Account                           │
 │                                                               │
 │  ┌─────────────────────────────┐                              │
 │  │  VPC                        │                              │
 │  │  - Public subnets (web)     │                              │
 │  │  - Private subnets (app)    │                              │
 │  │  - NAT Gateway (optional)   │                              │
 │  │  - Security Groups          │                              │
 │  └─────────────┬──────────────┘                              │
 │                │                                             │
 │        ┌───────▼────────┐      ┌──────────────────────────┐ │
 │        │ Nginx (Web)    │      │ App Instances            │ │
 │        │ Reverse Proxy  │─────▶│ (private subnets)        │ │
 │        └────────────────┘      └──────────────────────────┘ │
 │                                                               │
 └───────────────────────────────────────────────────────────────┘
```

---

## 📝 Requirements

### Part 1: Git Repository & Workflow Setup (20 marks)

#### 1.1 Repository Structure (5 marks)

Create a Git repository adapted for IaC workflow:

```text
Project5/
├── README.md
├── .gitignore
├── terraform/
│   ├── main.tf
│   ├── variables.tf
│   ├── outputs.tf
│   ├── backend-local.tf        # initial local state
│   ├── backend-s3.tf           # S3 remote backend (enabled later)
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
│       ├── compute/
│       │   ├── main.tf         # EC2 + SGs
│       │   ├── variables.tf
│       │   └── outputs.tf
│       └── web/
│           ├── main.tf         # Nginx reverse proxy + app SG wiring
│           ├── variables.tf
│           └── outputs.tf
├── ansible/
│   ├── ansible.cfg
│   ├── inventory/
│   │   ├── test_aws_ec2.yml
│   │   ├── staging_aws_ec2.yml
│   │   └── production_aws_ec2.yml
│   ├── playbooks/
│   │   ├── configure-web.yml
│   │   ├── deploy-app.yml
│   │   ├── update-app.yml
│   │   └── verify-app.yml
│   └── roles/
│       ├── web/
│       │   ├── tasks/main.yml
│       │   └── templates/nginx.conf.j2
│       └── app/
│           ├── tasks/main.yml
│           └── templates/index.html.j2
└── docs/
    ├── architecture.md
    ├── runbook-provisioning.md
    ├── runbook-update.md
    ├── runbook-rollback-dr.md
    └── change-management.md
```

**Tasks:**
- [ ] Create the directory structure and placeholder files.  
- [ ] Initialize Git.  
- [ ] Create an initial commit.  

**Deliverables:**
- Screenshot: `project5_part1_repository_structure.png`  
- Screenshot: `project5_part1_initial_commit.png`  

---

#### 1.2 Branching Strategy & Protection (7 marks)

Use a Git workflow like:

**Branch Structure:**
```text
main (Production)
├── staging (Staging pre-prod)
│   └── dev (Development / Test)
└── feature/* (short-lived feature branches)
```

**Branch Rules:**
- `feature/*` → merge into `dev` via Pull Request (PR).  
- `dev` → infra changes deployed to **test environment** only.  
- `staging` → changes deployed to **staging environment**.  
- `main` → only tagged & reviewed releases; deploy to **production**.  

**Branch Protection (GitHub):**
- Require PR reviews for `dev`, `staging`, and `main`.  
- Require a PR template checklist to be completed before merge.

**Tasks:**
- [ ] Create `dev` and `staging` branches from `main`.  
- [ ] Configure branch protection rules on GitHub.  
- [ ] Document workflow and rules in `docs/change-management.md`.  

**Deliverables:**
- Screenshot: `project5_part1_git_branches.png`  
- Screenshot: `project5_part1_branch_protection.png`  
- Screenshot: `project5_part1_branching_diagram.png`  

---

#### 1.3 PR Quality Gates (Manual) (8 marks)

Instead of using external tools, enforce quality via a PR checklist.

Your PR must show evidence that you ran:

- `terraform fmt -recursive`
- `terraform validate`
- `terraform plan -var-file="environments/test.tfvars"`

**Tasks:**
- [ ] Create a PR template in `docs/` (or describe in `docs/change-management.md`).  
- [ ] Demonstrate at least one PR where the checklist is completed with screenshots/logs.  

**Deliverables:**
- Screenshot: `project5_part1_pr_checklist.png`  
- Screenshot: `project5_part1_terraform_plan_in_pr.png`  

---

### Part 2: Terraform Infrastructure (30 marks)

#### 2.1 Network Module – VPC & Subnets (10 marks)

**Module:** `terraform/modules/network/`

Requirements:

- VPC CIDR (e.g., `10.0.0.0/16`).  
- At least:
  - 2 public subnets (for web tier).  
  - 2 private subnets (for app tier).  
- Internet Gateway + routing for public subnets.  
- **NAT Gateway (borderline but allowed)** to allow private instances to download packages/updates if required.  
- Outputs:
  - VPC ID, subnet IDs, route tables IDs (if needed).  

**Tasks:**
- [ ] Implement `main.tf`, `variables.tf`, `outputs.tf` for network.  
- [ ] Tag subnets by tier and environment.  

**Deliverables:**
- Screenshot: `project5_part2_network_main.png`  
- Screenshot: `project5_part2_network_outputs.png`  

---

#### 2.2 Compute Module – EC2 & Security Groups (10 marks)

**Module:** `terraform/modules/compute/`

Requirements:

- EC2 instances:
  - `web` instance in public subnet (Nginx reverse proxy).
  - `app` instance(s) in private subnet (app content).
- Security Groups:
  - `web_sg`: allow HTTP(80) from the internet and SSH only from your IP.
  - `app_sg`: allow app port only from `web_sg`; SSH optional (either from your IP or via web host if you choose).
- Tagging:
  - `Project`, `Environment`, `Role`

**Outputs:**
- Web public IP / DNS.
- App private IPs.

**Tasks:**
- [ ] Implement resources and SGs with least privilege.  
- [ ] Use variables for instance type, key name, etc.  

**Deliverables:**
- Screenshot: `project5_part2_compute_main.png`  
- Screenshot: `project5_part2_compute_outputs.png`  

---

#### 2.3 Web Module – Nginx Reverse Proxy Wiring (10 marks)

**Module:** `terraform/modules/web/`

Requirements:

- Provide variables/outputs that make Ansible configuration easy:
  - Output web public endpoint.
  - Output app private IP(s) for upstream config.
- Ensure tagging supports Ansible dynamic inventory:
  - `Role=web` and `Role=app`

**Tasks:**
- [ ] Implement outputs and wiring between compute resources and Ansible variables.  
- [ ] Document how Terraform outputs are used in Ansible.

**Deliverables:**
- Screenshot: `project5_part2_web_module_outputs.png`  
- Screenshot: `project5_part2_tags_for_inventory.png`  

---

### Part 3: Terraform State & Environments (20 marks)

#### 3.1 Initial Local State Backend (5 marks)

**File:** `terraform/backend-local.tf`

- Use a **local state** during initial development.
- Document in `docs/runbook-provisioning.md` how to initialize and apply using local state.

**Tasks:**
- [ ] Run `terraform init` and show local state file exists.  

**Deliverables:**
- Screenshot: `project5_part3_backend_local_tf.png`  
- Screenshot: `project5_part3_local_state_files.png`  

---

#### 3.2 S3 Remote Backend Migration (10 marks)

**File:** `terraform/backend-s3.tf`

- Configure an **S3 remote backend** for state storage (taught/allowed).
- Document migration steps in `docs/runbook-provisioning.md`:
  - `terraform init -migrate-state`

**Tasks:**
- [ ] Configure S3 backend and migrate state from local to S3.  
- [ ] Prove remote backend usage via `terraform state list`.  

**Deliverables:**
- Screenshot: `project5_part3_backend_s3_tf.png`  
- Screenshot: `project5_part3_state_migration_output.png`  

---

#### 3.3 Environment-Specific Variables & Test-first Strategy (5 marks)

**Files:**  

- `terraform/environments/test.tfvars`  
- `terraform/environments/staging.tfvars`  
- `terraform/environments/production.tfvars`  
- `terraform/terraform.tfvars.example`  

Requirements:

- Use `test` environment first:
  - smaller instance size and minimal counts.
- staging/production can vary sizes.

**Tasks:**
- [ ] Create `.tfvars` per environment.
- [ ] Document promotion strategy in `docs/change-management.md`.

**Deliverables:**
- Screenshot: `project5_part3_test_tfvars.png`  
- Screenshot: `project5_part3_staging_tfvars.png`  
- Screenshot: `project5_part3_production_tfvars.png`  

---

### Part 4: Ansible Application Deployment (15 marks)

#### 4.1 Inventory & Ansible Configuration (5 marks)

Use **aws_ec2** dynamic inventory per environment.

Example `ansible/inventory/test_aws_ec2.yml`:

```yaml
plugin: aws_ec2
regions:
  - me-central-1
filters:
  tag:Project: "Project5-IaC"
  tag:Environment: "test"
keyed_groups:
  - key: tags.Role
    prefix: role
hostnames:
  - private-ip-address
compose:
  ansible_host: private_ip_address
```

**Tasks:**
- [ ] Configure inventories for test/staging/production.  
- [ ] Confirm that Ansible can list hosts (`ansible-inventory --graph`).  

**Deliverables:**
- Screenshot: `project5_part4_ansible_inventory_test.png`  
- Screenshot: `project5_part4_ansible_inventory_graph.png`  

---

#### 4.2 Web + App Roles & Playbooks (10 marks)

**Role:** `roles/app`
- Deploy `index.html` showing:
  - environment name
  - instance hostname
  - version string (from Ansible var `app_version`)
- Ensure `/health` endpoint exists (simple static or nginx location).

**Role:** `roles/web`
- Install and configure Nginx as a reverse proxy to app private IP(s).

**Playbooks:**
- `configure-web.yml`
- `deploy-app.yml`
- `verify-app.yml`

**Tasks:**
- [ ] Implement roles idempotently.
- [ ] Deploy in **test** first, then promote.

**Deliverables:**
- Screenshot: `project5_part4_web_role_main.png`  
- Screenshot: `project5_part4_app_role_main.png`  
- Screenshot: `project5_part4_verify_app_execution.png`  

---

### Part 5: Git Tags, Releases & Change Management (15 marks)

#### 5.1 Git Tagging & Release Versions (5 marks)

Define a **tagging strategy**:

- `vX.Y.Z-test` → after successful test apply.
- `vX.Y.Z-rc` → staging release candidate.
- `vX.Y.Z` → production release.

**Tasks:**
- [ ] Create at least one tag (`v1.0.0-test`, `v1.0.0-rc`, `v1.0.0`).  
- [ ] Show which PRs/commits are included.

**Deliverables:**
- Screenshot: `project5_part5_git_tags.png`  
- Screenshot: `project5_part5_tagged_commits.png`  

---

#### 5.2 Change Management & Approval Workflow (10 marks)

In `docs/change-management.md`, describe:

- How infra changes are proposed (feature branch → PR → review).
- PR checklist gates (`fmt/validate/plan`).
- Test-first deployment on `dev` branch.
- Promotion `dev → staging → main`.
- Rollback strategy:
  - revert PR and re-apply previous tagged version.

**Deliverables:**
- Screenshot: `project5_part5_change_management_doc.png`  

---

### Part 6: Disaster Recovery & Runbooks (15 marks)

#### 6.1 Runbooks (8 marks)

Create runbooks in `docs/`:

1. `runbook-provisioning.md`
   - clone, init backend (local → S3), apply test/staging/prod, run Ansible.
2. `runbook-update.md`
   - workflow for changes, PR, test, promote.
3. `runbook-rollback-dr.md`
   - rollback using Git tags + Terraform.
   - DR scenario: destroy, recreate, redeploy.

**Tasks:**
- [ ] Fill all runbooks with step-by-step instructions and commands.

**Deliverables:**
- Screenshot: `project5_part6_runbook_provisioning.png`  
- Screenshot: `project5_part6_runbook_rollback_dr.png`  

---

#### 6.2 DR Test – Destroy & Recreate (7 marks)

Perform a DR test (use `test` environment):

1. Record baseline outputs (web IP and app response).
2. Destroy:
   - `terraform destroy -var-file="environments/test.tfvars"`
3. Recreate:
   - `terraform init`
   - `terraform apply -var-file="environments/test.tfvars"`
   - `ansible-playbook ... deploy-app.yml`
4. Validate:
   - confirm app reachable through web reverse proxy.

**Deliverables:**
- Screenshot: `project5_part6_dr_destroy.png`  
- Screenshot: `project5_part6_dr_recreate.png`  
- Screenshot: `project5_part6_dr_validation.png`  

---

## 📦 Submission Guidelines

### 1. GitHub Repository

Example:

```text
CC_<YourName>_<YourRollNumber>/Project-5-IaC-Git-Workflow
```

- ✅ Include: Terraform `.tf` files, Ansible playbooks/roles, docs, screenshots.  
- ❌ Exclude: `terraform.tfstate`, `.terraform/`, real `terraform.tfvars`, credentials (`.aws/`, `*.pem`, `*.key`, `.env`, etc.).  

---

### 2. PDF Report

Name:

```text
Project5_<YourName>_<YourRollNumber>.pdf
```

Suggested structure:

1. Cover Page  
2. Table of Contents  
3. Executive Summary  
4. Architecture Design (network + app + state diagram)  
5. Git Workflow & Branching Model  
6. Terraform Implementation (modules, backends, envs)  
7. Ansible Implementation (roles, playbooks, deployment)  
8. Change Management & Approvals  
9. Disaster Recovery Test Results  
10. Challenges & Solutions  
11. Conclusion & Future Improvements  
12. Appendices (configs, logs, screenshots)  

---

## 📊 Grading Criteria (High Level)

### Excellent (90–100%)
- Git workflow implemented with PRs, reviews, protections, tags.
- Terraform infra working for test/staging/prod.
- Local → S3 state migration completed and documented.
- Ansible deploy works reliably after provisioning.
- Runbooks and change management are clear.
- DR test completed with evidence.

### Good (80–89%)
- Most features implemented; minor gaps in documentation or promotion flow.

### Satisfactory (70–79%)
- Core infra + Git workflow; limited state migration evidence or runbooks.

### Needs Improvement (60–69%)
- Partial infra or workflow; limited testing and documentation.

### Unsatisfactory (<60%)
- Major components missing; no clear end-to-end workflow.

---

## 💡 Tips for Success

- Start with **test environment only**, then promote.
- Keep Terraform modules small and focused.
- Make Ansible roles idempotent and safe to rerun.
- Treat Git tags as “what is deployed”.

---

## 🐞 Common Issues & Solutions

**Issue:** State conflicts during migration  
- Use `terraform init -migrate-state` and confirm backend config.

**Issue:** App not reachable  
- Verify SG: web → app on correct port.
- Verify Nginx upstream points to app private IP.

**Issue:** Ansible finds no hosts  
- Check AWS tags and inventory filters (Project/Environment/Role).

---

## 📚 Resources

- [Terraform AWS Provider](https://registry.terraform.io/providers/hashicorp/aws/latest/docs)  
- [Terraform Backends](https://developer.hashicorp.com/terraform/language/settings/backends)  
- [Ansible Dynamic Inventory (aws_ec2)](https://docs.ansible.com/ansible/latest/collections/amazon/aws/aws_ec2_inventory.html)  
- [Terraform Modules](https://developer.hashicorp.com/terraform/language/modules)  
- [Nginx Reverse Proxy](https://docs.nginx.com/nginx/admin-guide/web-server/reverse-proxy/)  

---

## 📜 Academic Integrity

- Group project (max 3 persons).  
- Cite any external references used.  

---

## ⏰ Deadline

**Due Date:** 26-01-2026  
**Time:** 09:00 AM  

Late submissions follow your course policy.

---

## ✅ Final Checklist

### Git & Workflow
- [ ] Branches (`dev`, `staging`, `main`, `feature/*`) created.
- [ ] PR reviews + protections enabled.
- [ ] Tags created for test/rc/prod.

### Terraform
- [ ] VPC + subnets + SGs + EC2 deployed.
- [ ] NAT Gateway configured if needed.
- [ ] Local backend used initially.
- [ ] S3 backend configured and state migrated.
- [ ] `test`, `staging`, `production` tfvars created.

### Ansible
- [ ] Dynamic inventory works.
- [ ] Web (nginx reverse proxy) + App roles deployed.

### Docs & DR
- [ ] Runbooks completed.
- [ ] Change management documented.
- [ ] DR destroy + recreate tested and documented.
