# Lab Exam – Cloud & DevOps (IAM, Terraform, Ansible) – Reference Solution (Updated Provider Block)

We must actually run the commands, create resources, and take the required screenshots in your own AWS account and repo.

---

## Q1 – AWS IAM Setup Using AWS CLI and Console Verification

Assumptions:

- Region defaults from your AWS CLI configuration.
- IAM username example: `Ali` (replace `Ali` with your own name everywhere).

### 1. Create IAM group `SoftwareEngineering`

```bash
aws iam create-group --group-name SoftwareEngineering
# Screenshot: q1_create_group.png
```

View group details:

```bash
aws iam get-group --group-name SoftwareEngineering
# Screenshot: q1_group_details.png
```

Make sure output shows:

- `GroupName`: `SoftwareEngineering`
- `Arn`: `arn:aws:iam::<account-id>:group/SoftwareEngineering`

---

### 2. Create IAM user (your name) and view details

```bash
aws iam create-user --user-name Ali
# Screenshot: q1_create_user.png
```

View user details:

```bash
aws iam get-user --user-name Ali
# Screenshot: q1_user_details.png
```

Output should show:

- `UserName`: `Ali`
- `Arn`: `arn:aws:iam::<account-id>:user/Ali`

---

### 3. Add the IAM user to `SoftwareEngineering` group

```bash
aws iam add-user-to-group \
  --user-name Ali \
  --group-name SoftwareEngineering
# Screenshot: q1_add_user_to_group.png
```

Confirm membership:

```bash
aws iam get-group --group-name SoftwareEngineering
# Screenshot: q1_group_membership.png
```

Your user (`Ali`) should appear in `Users`.

---

### 4. Attach `AdministratorAccess` policy to the group

Find policy ARN:

```bash
aws iam list-policies \
  --scope AWS \
  --query 'Policies[?PolicyName==`AdministratorAccess`]' \
  --output table
# Screenshot: q1_find_admin_policy.png
```

ARN is typically:

```text
arn:aws:iam::aws:policy/AdministratorAccess
```

Attach policy:

```bash
aws iam attach-group-policy \
  --group-name SoftwareEngineering \
  --policy-arn arn:aws:iam::aws:policy/AdministratorAccess
# Screenshot: q1_attach_admin_policy.png
```

---

### 5. List attached policies of the group

```bash
aws iam list-attached-group-policies \
  --group-name SoftwareEngineering
# Screenshot: q1_list_group_policies.png
```

Must show `AdministratorAccess` attached.

---

### 6. Verify in AWS Management Console

- `q1_console_group.png`: IAM → Groups → `SoftwareEngineering`.
- `q1_console_user_in_group.png`: IAM → Users → your user → Groups tab shows `SoftwareEngineering`.
- `q1_console_group_policy.png`: IAM → Groups → `SoftwareEngineering` → Permissions → `AdministratorAccess` attached.

---

## Q2 – Terraform Lab: AWS Environment with Nginx over HTTPS

Suggested repo structure:

```text
Lab_exam/
  terraform/
    main.tf
    variables.tf
    outputs.tf
    terraform.tfvars        (optional)
    entry-script.sh
```

### 1. AWS provider configuration (`main.tf`) – **UPDATED AS YOU REQUESTED**

You can keep the `terraform` block as before (for required providers), but update the **provider block** to use `shared_config_files` and `shared_credentials_files`.

```hcl
provider "aws" {
  shared_config_files      = ["~/.aws/config"]
  shared_credentials_files = ["~/.aws/credentials"]
}
```

- Screenshot: `q2_provider.png`.

---

### 2. Define input variables (`variables.tf`)

```hcl
variable "vpc_cidr_block" {}
variable "subnet_cidr_block" {}
variable "availability_zone" {}
variable "env_prefix" {}
variable "instance_type" {}
```

- Screenshot: `q2_variables.png`.

---

### 3. VPC and subnet (`main.tf`)

```hcl
resource "aws_vpc" "myapp_vpc" {
  cidr_block           = var.vpc_cidr_block
  enable_dns_support   = true
  enable_dns_hostnames = true

  tags = {
    Name = "${var.env_prefix}-vpc"
  }
}

resource "aws_subnet" "myapp_subnet_1" {
  vpc_id                  = aws_vpc.myapp_vpc.id
  cidr_block              = var.subnet_cidr_block
  availability_zone       = var.availability_zone
  map_public_ip_on_launch = true

  tags = {
    Name = "${var.env_prefix}-subnet-1"
  }
}
```

- Screenshot: `q2_vpc_subnet.png`.

---

### 4. Internet Gateway and default route table (`main.tf`)

Use `aws_default_route_table` (simple and clear):

```hcl
resource "aws_internet_gateway" "myapp_igw" {
  vpc_id = aws_vpc.myapp_vpc.id

  tags = {
    Name = "${var.env_prefix}-igw"
  }
}

resource "aws_default_route_table" "default_rt" {
  default_route_table_id = aws_vpc.myapp_vpc.default_route_table_id

  route {
    cidr_block = "0.0.0.0/0"
    gateway_id = aws_internet_gateway.myapp_igw.id
  }

  tags = {
    Name = "${var.env_prefix}-rt"
  }
}
```

- Screenshot: `q2_igw_route_table.png`.

---

### 5. Discover public IP with `data "http"` and `locals` (`main.tf`)

```hcl
data "http" "public_ip" {
  url = "https://icanhazip.com"
}

locals {
  my_ip = "${chomp(data.http.public_ip.response_body)}/32"
}
```

- Screenshot: `q2_http_and_locals.png`.

---

### 6. Configure default security group (`main.tf`)

```hcl
resource "aws_default_security_group" "default_sg" {
  vpc_id = aws_vpc.myapp_vpc.id

  # SSH from your /32 IP
  ingress {
    description = "Allow SSH from my IP"
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = [local.my_ip]
  }

  # HTTP from anywhere
  ingress {
    description = "Allow HTTP from anywhere"
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  # HTTPS from anywhere
  ingress {
    description = "Allow HTTPS from anywhere"
    from_port   = 443
    to_port     = 443
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  # Egress all
  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }

  tags = {
    Name = "${var.env_prefix}-default-sg"
  }
}
```

- Screenshot: `q2_default_sg.png`.

---

### 7. AWS key pair (`main.tf`)

```hcl
resource "aws_key_pair" "serverkey" {
  key_name   = "serverkey"
  public_key = file("~/.ssh/id_ed25519.pub")
}
```

- Screenshot: `q2_keypair.png`.

---

### 8. EC2 instance resource (`main.tf`)

```hcl
resource "aws_instance" "myapp-server" {
  ami           = "ami-05524d6658fcf35b6" # Amazon Linux 2023 Kernel 6.1 AMI
  instance_type = var.instance_type
  subnet_id     = aws_subnet.myapp_subnet_1.id
  security_groups = [aws_default_security_group.default_sg. id]
  availability_zone = var.availability_zone
  associate_public_ip_address = true
  key_name = aws_key_pair.ssh-key. key_name

  user_data = file("./entry-script.sh")

  tags = {
     Name = "${var.env_prefix}-ec2-instance"
  }
}
```

- Screenshot: `q2_ec2_resource.png`.

---

### 9. `entry-script.sh` – Nginx + HTTPS

```bash
#!/bin/bash
set -e
yum update -y
yum install -y nginx
systemctl start nginx
systemctl enable nginx

# Create directories for SSL certificates if they don't exist
mkdir -p /etc/ssl/private
mkdir -p /etc/ssl/certs

# Get IMDSv2 token
TOKEN=$(curl -s -X PUT "http://169.254.169.254/latest/api/token" \
  -H "X-aws-ec2-metadata-token-ttl-seconds: 21600")

# Get current public IP
PUBLIC_IP=$(curl -s -H "X-aws-ec2-metadata-token: $TOKEN" \
  http://169.254.169.254/latest/meta-data/public-ipv4)

PUBLIC_HOSTNAME=$(curl -s -H "X-aws-ec2-metadata-token:  $TOKEN" \
  http://169.254.169.254/latest/meta-data/public-hostname)

# Generate self-signed certificate with dynamic IP
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
  -keyout /etc/ssl/private/selfsigned.key \
  -out /etc/ssl/certs/selfsigned.crt \
  -subj "/CN=$PUBLIC_IP" \
  -addext "subjectAltName=IP:$PUBLIC_IP" \
  -addext "basicConstraints=CA:FALSE" \
  -addext "keyUsage=digitalSignature,keyEncipherment" \
  -addext "extendedKeyUsage=serverAuth"

echo "Self-signed certificate created for IP: $PUBLIC_IP"

# Backup existing nginx. conf
cp /etc/nginx/nginx.conf /etc/nginx/nginx.conf.bak

# Overwrite nginx.conf with the desired content
cat <<EOF > /etc/nginx/nginx.conf
user nginx;
worker_processes auto;
error_log /var/log/nginx/error.log notice;
pid /run/nginx. pid;

events {
    worker_connections 1024;
}

http {
    log_format  main  '\$remote_addr - \$remote_user [\$time_local] "\$request"'
                      '\$status \$body_bytes_sent "\$http_referer"'
                      '"\$http_user_agent" "\$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile            on;
    tcp_nopush          on;
    keepalive_timeout   65;
    types_hash_max_size 4096;

    include             /etc/nginx/mime.types;
    default_type        application/octet-stream;

    upstream backend_servers {
        server 158.252.94.241:80;
        server 158.252.94.242:80 backup;
    }

    server {
        listen 443 ssl;
        server_name $PUBLIC_IP;
        ssl_certificate /etc/ssl/certs/selfsigned.crt;
        ssl_certificate_key /etc/ssl/private/selfsigned.key;

        location / {
            root /usr/share/nginx/html;
            index index.html;
    #       proxy_pass http://158.252.94.241:80;
    #       proxy_pass http://backend_servers;
            
        }
    }

    server {
        listen 80;
        server_name _;
        return 301 https://\$host\$request_uri;
    }
}
EOF

# Test and restart Nginx
systemctl restart nginx
```

```bash
chmod +x entry-script.sh
```

- Screenshot: `q2_entry_script.png`.

---

### 10. Terraform output for public IP (`outputs.tf`)

```hcl
output "ec2_public_ip" {
  description = "Public IP of the EC2 instance"
  value       = aws_instance.myapp_server.public_ip
}
```

- Screenshot: `q2_output_block.png`.

---

### 11. Variable values (`terraform.tfvars`)

```hcl
vpc_cidr_block    = "10.0.0.0/16"
subnet_cidr_block = "10.0.10.0/24"
availability_zone = "me-central-1a"
env_prefix        = "dev"
instance_type     = "t3.micro"
```

- Screenshot: `q2_tfvars_or_vars.png`.

---

### 12. Run Terraform commands

From `Lab_exam/terraform/`:

```bash
terraform init
# Screenshot: q2_terraform_init.png

terraform plan
# Screenshot: q2_terraform_plan.png

terraform apply -auto-approve
# Screenshot: q2_terraform_apply.png

terraform output
# Screenshot: q2_terraform_output.png
```

---

### 13. Verify resources in AWS console

- `q2_console_vpc.png`: VPC CIDR `10.0.0.0/16`, tag `Name = dev-vpc`.
- `q2_console_subnet.png`: Subnet CIDR `10.0.10.0/24`, AZ `me-central-1a`, tag `Name = dev-subnet-1`.
- `q2_console_igw.png`: IGW attached to that VPC, tag `Name = dev-igw`.
- `q2_console_route_table.png`: Route table tag `Name = dev-rt`, route `0.0.0.0/0` → IGW.
- `q2_console_sg.png`: Default SG rules: SSH from `your-ip/32`, HTTP/HTTPS from `0.0.0.0/0`, egress all; tag `Name = dev-default-sg`.
- `q2_console_ec2.png`: EC2 instance in correct subnet/AZ, with public IP, key pair `serverkey`, tag `Name = dev-ec2-instance`.

---

### 14. Verify HTTPS access

```text
https://<public-ip-of-instance>
```

- Accept self-signed certificate warning.
- Page must include your name and word “Terraform”.
- Screenshot: `q2_https_browser.png`.

---

## Q3 – Ansible Playbook for EC2 Web Server Using Q2 Instance

Directory:

```text
Lab_exam/
  ansible/
    hosts
    ansible.cfg
    my-playbook.yml
```

Use the public IP from Q2.

### 1. Inventory `hosts`

```ini
[ec2]
<public-ip-from-q2>

[ec2:vars]
ansible_user = ec2-user
ansible_ssh_private_key_file = ~/.ssh/id_ed25519
ansible_ssh_common_args = -o StrictHostKeyChecking=no
```

- Screenshot: `q3_hosts.png`.

---

### 2. `ansible.cfg`

```ini
[defaults]
inventory = ./hosts
host_key_checking = False
remote_user = ec2-user
interpreter_python = /usr/bin/python3

[ssh_connection]
ssh_args = -o StrictHostKeyChecking=no
```

- Screenshot: `q3_ansible_cfg.png`.

---

### 3. Playbook `my-playbook.yml`

```yaml
---
- name: Configure EC2 instance with Apache and IMDSv2
  hosts: ec2
  become: true

  tasks:
    - name: Update all packages
      yum:
        name: "*"
        state: latest

    - name: Install Apache httpd
      yum:
        name: httpd
        state: present

    - name: Start and enable httpd
      service:
        name: httpd
        state: started
        enabled: true

    - name: Get IMDSv2 token
      uri:
        url: "http://169.254.169.254/latest/api/token"
        method: PUT
        headers:
          X-aws-ec2-metadata-token-ttl-seconds: "21600"
      register: imds_token

    - name: Get public IPv4 using IMDSv2
      uri:
        url: "http://169.254.169.254/latest/meta-data/public-ipv4"
        method: GET
        headers:
          X-aws-ec2-metadata-token: "{{ imds_token.content }}"
      register: public_ipv4

    - name: Get public hostname using IMDSv2
      uri:
        url: "http://169.254.169.254/latest/meta-data/public-hostname"
        method: GET
        headers:
          X-aws-ec2-metadata-token: "{{ imds_token.content }}"
      register: public_hostname

    - name: Debug public IP
      debug:
        msg: "Public IP of this instance is {{ public_ipv4.content }}"

    - name: Restart httpd
      service:
        name: httpd
        state: restarted
```

- Screenshot: `q3_playbook.png`.

---

### 4. Run the playbook

From `Lab_exam/ansible/`:

```bash
ansible-playbook my-playbook.yml
# or: ansible-playbook -i hosts my-playbook.yml
```

- Screenshot: `q3_play_run.png`.

---

### 5. Verify HTTP access

```bash
curl http://<public-ip-from-q2>
# or open in browser:
# http://<public-ip-from-q2>
```

- You should see Apache response.
- Screenshot: `q3_http_browser.png` (recommended).

---

## Cleanup (optional but recommended)

From `Lab_exam/terraform/`:

```bash
terraform destroy -auto-approve
# Screenshot: cleanup_terraform_destroy.png (optional)
```

Verify in AWS console that no lab-related EC2 instances remain:

- Screenshot: `cleanup_ec2_console.png` (optional).

---

## Final Checklist

- All required code files in `Lab_exam` repo.
- All required screenshots (`q1_*`, `q2_*`, `q3_*`) created with your own runs.
- Push everything to GitHub.
