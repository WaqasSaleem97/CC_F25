# 📝 Project 1 – Multi-Environment Static Website (S3 + CloudFront) with Terraform & Ansible

**Due Date:** 26-01-2026  
**Total Marks:** 100  
**Submission Format:** GitHub Repository + PDF Report  

---

## 📋 Project Overview

Design and deploy a **multi-environment static website platform** on AWS using **Terraform** and **Ansible**.

**Scenario:**  
You are tasked with deploying static websites for three environments:

- `dev`
- `staging`
- `production`

For each environment, you must provision:

- 1 **S3 bucket** (private) to store static website content
- 1 **CloudFront distribution** in front of the S3 bucket
- Proper **S3 bucket policies** to prevent direct public access
- **HTTP → HTTPS** redirect behavior configured at CloudFront

All infrastructure (S3 + CloudFront) must be provisioned via **Terraform modules**, and all **content synchronization** must be automated using **Ansible** (no manual AWS Console uploads, no bash-only solutions).

---

## 🎯 Learning Objectives

By completing this project, you will demonstrate:

- Terraform code organization and modularization (root + modules)
- Infrastructure as Code (IaC) best practices for multi-environment setups
- Secure static hosting with AWS S3 and CloudFront
- Use of S3 bucket policies to restrict direct public access
- Ansible automation to sync local website content to S3
- Working with Terraform outputs as inputs to Ansible

---

## 📝 Requirements

### Part 1: Terraform Infrastructure Setup (25 marks)

#### 1.1 Project Structure (5 marks)

Create a well-organized Terraform project with the following structure:

```
Project1/
├── main.tf
├── variables.tf
├── outputs.tf
├── locals.tf
├── terraform.tfvars
├── .gitignore
├── modules/
│   ├── s3_site/
│   │   ├── main.tf
│   │   ├── variables.tf
│   │   └── outputs.tf
│   └── cloudfront/
│       ├── main.tf
│       ├── variables.tf
│       └── outputs.tf
├── ansible/
│   ├── ansible.cfg
│   ├── inventory/
│   │   └── localhost.yml
│   └── playbooks/
│       └── sync-site.yml
├── site/
│   ├── index.html
│   └── assets/
└── README.md
```

**Tasks:**

- [ ] Create all necessary files and directories
- [ ] Implement proper `.gitignore` to exclude:
  - `.terraform/`
  - `*.tfstate`, `*.tfstate.backup`
  - `*.tfvars` (except example)
  - OS / editor junk files
- [ ] Document the project structure in `README.md`

**Deliverables:**

- Screenshot: `project1_part1_project_structure.png` (tree command output)  
- Screenshot: `project1_part1_gitignore.png` (content of `.gitignore`)

---

#### 1.2 Variable Configuration (5 marks)

Define all required variables in `variables.tf`.

**Required Variables:**

- `aws_region` (string) – Region for all resources (e.g., `us-east-1`)
- `env` (string) – Environment name: `dev`, `staging`, or `prod`
- `project_name` (string) – Name used for tagging and naming resources
- `bucket_prefix` (string) – Prefix for S3 bucket names
- `enable_logging` (bool) – Whether to enable CloudFront logging
- `logging_bucket` (string) – Optional S3 bucket for logs (may be empty)
- `tags` (map(string)) – Common tags

**Tasks:**

- [ ] Add descriptions for all variables
- [ ] Add validation for `env` (must be one of: dev, staging, prod)
- [ ] Set sensible defaults where appropriate (e.g., `enable_logging = false`)
- [ ] Populate `terraform.tfvars` with your own values (local only, DO NOT COMMIT)

**Sample `terraform.tfvars` structure:**

```hcl
aws_region   = "us-east-1"
env          = "dev"
project_name = "cc-static-site"
bucket_prefix = "cc-static"
enable_logging = false
tags = {
  Owner = "YourName"
  Class = "CloudComputing"
}
```

**Deliverables:**

- Screenshot: `project1_part1_variables_tf.png`  
- Screenshot: `project1_part1_terraform_tfvars.png`

---

#### 1.3 Locals Configuration (5 marks)

Create `locals.tf` with:

- Environment-specific naming conventions
- Common tagging scheme
- Any other reusable local values

**Example:**

```hcl
locals {
  common_tags = merge(
    {
      Project     = var.project_name
      Environment = var.env
      ManagedBy   = "Terraform"
    },
    var.tags
  )

  bucket_name = lower("${var.bucket_prefix}-${var.project_name}-${var.env}")
}
```

**Tasks:**

- [ ] Define `common_tags` using `var.tags` plus project/env info
- [ ] Define a deterministic `bucket_name` using `bucket_prefix` and `env`
- [ ] (Optional) Add locals for friendly names or CloudFront comment strings

**Deliverables:**

- Screenshot: `project1_part1_locals_tf.png`

---

#### 1.4 S3 Site Module (10 marks)

Create a `s3_site` module that provisions:

- One **private S3 bucket** per environment
- Block public access for the bucket
- A bucket policy that **does NOT allow public access**; it should only allow access from trusted principals (will be wired to CloudFront module via variables)

**Module Location:** `modules/s3_site/`

**Required Variables (module):**

- `bucket_name` (string)
- `tags` (map(string))

**Required Resources:**

- `aws_s3_bucket`  
- `aws_s3_bucket_public_access_block`  
- `aws_s3_bucket_policy` (restrict direct public access)

**Required Outputs:**

- `bucket_name`
- `bucket_arn`
- `bucket_domain_name`

**Tasks:**

- [ ] Create `main.tf`, `variables.tf`, and `outputs.tf` under `modules/s3_site/`
- [ ] Ensure public access is blocked (`block_public_*` flags)
- [ ] Add descriptive tags using `tags` input

**Deliverables:**

- Screenshot: `project1_part1_s3_module_main.png`  
- Screenshot: `project1_part1_s3_module_outputs.png`

---

### Part 2: CloudFront Module (15 marks)

#### 2.1 Module Design (10 marks)

Create a reusable `cloudfront` module in `modules/cloudfront/`.

**Module Requirements:**

**Variables:**

- `project_name` (string)
- `env` (string)
- `bucket_domain_name` (string)
- `comment` (string)
- `enable_logging` (bool)
- `logging_bucket` (string)
- `tags` (map(string))

**Resources to Create:**

- One `aws_cloudfront_distribution` with:
  - Origin pointing to the S3 bucket (using `bucket_domain_name`)
  - Default behavior:
    - Allowed methods: GET, HEAD
    - HTTP to HTTPS redirect (`viewer_protocol_policy = "redirect-to-https"`)
  - (Optional within course scope) Simple error handling like 404
  - Tags for traceability

**Outputs:**

- `distribution_id`
- `domain_name`

**Tasks:**

- [ ] Create `variables.tf` with required vars and descriptions
- [ ] Create `main.tf` for the CloudFront distribution
- [ ] Create `outputs.tf` exposing the distribution domain name and ID
- [ ] Ensure the module is reusable across environments (dev/staging/prod)

**Deliverables:**

- Screenshot: `project1_part2_cloudfront_module_variables.png`  
- Screenshot: `project1_part2_cloudfront_module_main.png`  
- Screenshot: `project1_part2_cloudfront_module_outputs.png`

---

#### 2.2 Module Usage (5 marks)

In root `main.tf`, instantiate the modules for a **single environment** (driven by `var.env`).

**Example structure in `main.tf`:**

```hcl
module "s3_site" {
  source      = "./modules/s3_site"
  bucket_name = local.bucket_name
  tags        = local.common_tags
}

module "cdn" {
  source            = "./modules/cloudfront"
  project_name      = var.project_name
  env               = var.env
  bucket_domain_name = module.s3_site.bucket_domain_name
  comment           = "Static site for ${var.project_name}-${var.env}"
  enable_logging    = var.enable_logging
  logging_bucket    = var.logging_bucket
  tags              = local.common_tags
}
```

**Tasks:**

- [ ] Wire `s3_site` and `cloudfront` modules together in `main.tf`
- [ ] Ensure naming/tags incorporate `env` and `project_name`
- [ ] Confirm `terraform plan` works with one environment at a time

**Deliverables:**

- Screenshot: `project1_part2_main_tf_modules.png`

---

### Part 3: Ansible Content Deployment (20 marks)

#### 3.1 Ansible Setup (5 marks)

Create Ansible configuration under `ansible/`:

**Files:**

- `ansible/ansible.cfg`
- `ansible/inventory/localhost.yml`

**Sample `ansible.cfg`:**

```ini
[defaults]
inventory = inventory/localhost.yml
host_key_checking = False
retry_files_enabled = False
stdout_callback = yaml
```

**Sample `inventory/localhost.yml`:**

```yaml
all:
  hosts:
    localhost:
      ansible_connection: local
```

**Tasks:**

- [ ] Configure Ansible to use local connection (`localhost`)
- [ ] Verify Ansible installation and inventory (`ansible-inventory -i inventory/localhost.yml --list`)

**Deliverables:**

- Screenshot: `project1_part3_ansible_cfg.png`  
- Screenshot: `project1_part3_inventory_list.png`

---

#### 3.2 `sync-site.yml` Playbook (15 marks)

Create `ansible/playbooks/sync-site.yml` that:

- Syncs content from `site/` to the S3 bucket specified via variable
- Uses the `amazon.aws.s3_sync` module
- Uses `bucket_name` passed via `--extra-vars` or environment variable

**Example Playbook:**

```yaml
---
- name: Sync static site to S3
  hosts: localhost
  gather_facts: false
  vars:
    bucket_name: "{{ bucket_name | default(lookup('env', 'BUCKET_NAME')) }}"
    site_dir: "../../site"
  tasks:
    - name: Ensure bucket name provided
      assert:
        that:
          - bucket_name is defined
          - bucket_name | length > 0
        fail_msg: "bucket_name is required (pass via --extra-vars or BUCKET_NAME env)."

    - name: Sync site content
      amazon.aws.s3_sync:
        region: "{{ lookup('env', 'AWS_REGION') | default('us-east-1') }}"
        bucket: "{{ bucket_name }}"
        file_root: "{{ site_dir }}"
        delete: false
```

**Tasks:**

- [ ] Install required Ansible collection: `ansible-galaxy collection install amazon.aws`
- [ ] Implement idempotent sync of the `site/` directory to S3
- [ ] Use `bucket_name` and `AWS_REGION` from environment or extra-vars

**Deliverables:**

- Screenshot: `project1_part3_sync_site_yaml.png`  
- Screenshot: `project1_part3_sync_site_run.png` (successful playbook run)

---

### Part 4: Deployment & Testing (25 marks)

#### 4.1 Terraform Deployment (10 marks)

Deploy for at least **one environment** (e.g., `dev`):

**Tasks:**

- [ ] Run `terraform init`
- [ ] Run `terraform validate`
- [ ] Run `terraform plan`
- [ ] Run `terraform apply -auto-approve`

**Commands (example):**

```bash
terraform init
terraform validate
terraform plan  -var="env=dev"
terraform apply -auto-approve -var="env=dev"
```

**Deliverables:**

- Screenshot: `project1_part4_terraform_init.png`  
- Screenshot: `project1_part4_terraform_validate.png`  
- Screenshot: `project1_part4_terraform_plan.png`  
- Screenshot: `project1_part4_terraform_apply.png`

---

#### 4.2 Terraform Outputs (5 marks)

Create comprehensive outputs in `outputs.tf`:

```hcl
output "bucket_name" {
  description = "S3 bucket name for static site"
  value       = module.s3_site.bucket_name
}

output "cloudfront_domain_name" {
  description = "CloudFront distribution domain name"
  value       = module.cdn.domain_name
}
```

**Tasks:**

- [ ] Add outputs for bucket name and CloudFront URL
- [ ] Use `terraform output` to display them
- [ ] Export `BUCKET_NAME` and `AWS_REGION` for Ansible

**Commands:**

```bash
terraform output
export BUCKET_NAME=$(terraform output -raw bucket_name)
export AWS_REGION=<your-region>
```

**Deliverables:**

- Screenshot: `project1_part4_terraform_output.png`

---

#### 4.3 Ansible Deployment (5 marks)

Use Ansible to upload the content:

```bash
cd ansible
ansible-playbook playbooks/sync-site.yml \
  --extra-vars "bucket_name=$BUCKET_NAME"
```

**Tasks:**

- [ ] Run the playbook successfully
- [ ] Confirm objects exist in the S3 bucket (AWS Console or CLI)

**Deliverables:**

- Screenshot: `project1_part4_ansible_sync_output.png`  
- Screenshot: `project1_part4_s3_objects.png` (S3 console showing uploaded files)

---

#### 4.4 Website Testing (5 marks)

Test the website via CloudFront URL:

**Tasks:**

- [ ] Open the CloudFront URL in a browser
- [ ] Confirm `index.html` loads correctly
- [ ] Take screenshots for at least one environment (dev)

**Deliverables:**

- Screenshot: `project1_part4_cloudfront_website.png`

---

### Part 5: Security & Cleanup (15 marks)

#### 5.1 Bucket Access Verification (5 marks)

Verify S3 bucket is private:

**Tasks:**

- [ ] Try to access an object via direct S3 URL (should return 403)
- [ ] Access the same object via CloudFront URL (should return 200)

**Deliverables:**

- Screenshot: `project1_part5_s3_403.png` (Forbidden via S3 URL)  
- Screenshot: `project1_part5_cloudfront_200.png` (OK via CloudFront)

---

#### 5.2 README Documentation (5 marks)

Create a comprehensive `README.md` with:

**Required Sections:**

1. **Project Overview**
2. **Architecture Diagram** (ASCII or image)
3. **Terraform Setup & Usage**
4. **Ansible Usage**
5. **Testing Instructions**
6. **Cleanup Instructions**
7. **Known Issues / Troubleshooting**

**Deliverables:**

- Screenshot: `project1_part5_readme.png`

---

#### 5.3 Cleanup (5 marks)

Destroy all resources:

**Tasks:**

- [ ] Run `terraform destroy -var="env=dev"`
- [ ] Confirm all resources (S3, CloudFront) are deleted in AWS Console

**Deliverables:**

- Screenshot: `project1_part5_terraform_destroy.png`  
- Screenshot: `project1_part5_aws_console_empty.png`

---

## Submission Guidelines

### 1. GitHub Repository

Create a public GitHub repository named:

```
CC_<YourName>_<YourRollNumber>/Project-1-Static-Website
```

**Repository Structure:**

```
Project1/
├── README.md
├── main.tf
├── variables.tf
├── outputs.tf
├── locals.tf
├── terraform.tfvars.example   # Example only (NO REAL VALUES)
├── .gitignore
├── modules/
│   ├── s3_site/
│   └── cloudfront/
├── ansible/
│   ├── ansible.cfg
│   ├── inventory/
│   └── playbooks/
├── site/
├── screenshots/
└── docs/
    ├── architecture.md
    └── troubleshooting.md
```

**Important:**

- ✅ DO commit: `.tf` files, modules, Ansible files, README, example `terraform.tfvars.example`, screenshots  
- ❌ DO NOT commit: real `terraform.tfvars`, `terraform.tfstate`, `.terraform/`, AWS credentials, private keys  

---

### 2. PDF Report

Create a PDF report named:

```
Project1_<YourName>_<YourRollNumber>.pdf
```

**Suggested Report Structure:**

1. **Cover Page**  
2. **Table of Contents**  
3. **Executive Summary**  
4. **Architecture Design**  
5. **Implementation Details (Terraform + Ansible)**  
6. **Testing & Results**  
7. **Challenges & Lessons Learned**  
8. **Conclusion**  
9. **Appendices (Code snippets, screenshots)**  

---

### 3. Submission Checklist

Before submitting:

- [ ] GitHub repo public and accessible  
- [ ] `.gitignore` configured correctly  
- [ ] No secrets or real tfvars committed  
- [ ] README.md complete  
- [ ] All required screenshots captured and added to `screenshots/`  
- [ ] PDF report complete and well-formatted  

---

## Grading Rubric

| Component                              | Points |
|----------------------------------------|--------|
| Part 1: Terraform Structure & Variables| 25     |
| Part 2: Modules (S3 + CloudFront)      | 15     |
| Part 3: Ansible Automation             | 20     |
| Part 4: Deployment & Testing           | 25     |
| Part 5: Security, Docs & Cleanup       | 15     |
| Code Quality & Report                  | ±10    |
| **TOTAL**                              | **100**|

---

## Tips for Success

- Start early and build/test incrementally  
- Keep modules small and reusable  
- Use tags consistently across all resources  
- Document issues and solutions as you go  
- Always run `terraform fmt` and `terraform validate`  

---

## Common Issues & Solutions

**Issue 1: Ansible `amazon.aws` module not found**  
Solution:  
```bash
ansible-galaxy collection install amazon.aws
```

**Issue 2: CloudFront not showing latest content**  
Solution:  
- Check if your browser is caching  
- Add a query string (e.g., `?v=1`)  
- (Optional) Invalidate CloudFront cache manually via console  

**Issue 3: 403 from S3 direct URL**  
Solution:  
- This is expected; bucket is private  
- Use CloudFront URL instead  

---

## FAQ

**Q: Can I implement all three environments (dev, staging, prod)?**  
A: Yes, encouraged. You may run `terraform apply` for each `env` value separately.

**Q: Can I use a different region than `us-east-1`?**  
A: Yes, but update `aws_region` and ensure services are available in that region.

**Q: Is Ansible strictly required?**  
A: Yes, all S3 content synchronization must be done via Ansible, not manual uploads.

---

## Academic Integrity

- This is an individual project  
- Discussion of concepts is allowed; sharing full code is not  
- Cite any external code snippets or references used  

---

**Good luck with Project 1! 🚀**  
