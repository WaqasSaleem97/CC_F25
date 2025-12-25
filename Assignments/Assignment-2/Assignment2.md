# 📝 Assignment 2 – Advanced Terraform & Nginx Multi-Tier Architecture

**Due Date:** 28-11-2025  
**Total Marks:** 100  
**Submission Format:** GitHub Repository + PDF Report

---

## 📋 Assignment Overview

Design and deploy a production-ready multi-tier web infrastructure on AWS using Terraform modules and Nginx as a reverse proxy/load balancer with advanced configurations.

**Scenario:**  
You are tasked with deploying a high-availability web application infrastructure for a client. The infrastructure must include:
- 1 Nginx server (reverse proxy/load balancer with HTTPS)
- 3 Backend web servers (web-1, web-2, web-3)
- Proper security configurations
- Caching for performance optimization
- High availability with backup server configuration

---

## 🎯 Learning Objectives

By completing this assignment, you will demonstrate:
- Terraform code organization and modularization
- Infrastructure as Code (IaC) best practices
- Nginx advanced configurations (reverse proxy, load balancer, caching, SSL/TLS)
- High availability and failover patterns
- AWS networking and security group management
- SSH key management and secure connections

---

## 📝 Requirements

### Part 1: Infrastructure Setup (25 marks)

#### 1.1 Project Structure (5 marks)
Create a well-organized Terraform project with the following structure: 

```
Assignment_2/
├── main.tf
├── variables.tf
├── outputs.tf
├── locals.tf
├── terraform.tfvars
├── . gitignore
├── modules/
│   ├── networking/
│   │   ├── main.tf
│   │   ├── variables.tf
│   │   └── outputs. tf
│   ├── security/
│   │   ├── main.tf
│   │   ├── variables.tf
│   │   └── outputs. tf
│   └── webserver/
│       ├── main.tf
│       ├── variables.tf
│       └── outputs.tf
├── scripts/
│   ├── nginx-setup.sh
│   └── apache-setup.sh
└── README.md
```

**Tasks:**
- [ ] Create all necessary files and directories
- [ ] Implement proper `.gitignore` to exclude sensitive files
- [ ] Document the project structure in `README.md`

**Deliverables:**
- Screenshot:  `assignment_part1_project_structure.png` (tree command output)
- Screenshot: `assignment_part1_gitignore.png` (content of .gitignore)

---

#### 1.2 Variable Configuration (5 marks)

Define all required variables in `variables.tf`:

**Required Variables:**
- `vpc_cidr_block` (string)
- `subnet_cidr_block` (string)
- `availability_zone` (string)
- `env_prefix` (string)
- `instance_type` (string)
- `public_key` (string)
- `private_key` (string)
- `backend_servers` (list of objects with name and script_path)

**Tasks:**
- [ ] Add validation rules for CIDR blocks
- [ ] Add descriptions for all variables
- [ ] Set appropriate defaults where applicable
- [ ] Populate `terraform.tfvars` with your values

**Sample terraform.tfvars structure:**
```hcl
vpc_cidr_block    = "10.0.0.0/16"
subnet_cidr_block = "10.0.10.0/24"
availability_zone = "me-central-1a"
env_prefix        = "prod"
instance_type     = "t3.micro"
public_key        = "~/.ssh/id_ed25519.pub"
private_key       = "~/.ssh/id_ed25519"
```

**Deliverables:**
- Screenshot: `assignment_part1_variables_tf.png`
- Screenshot: `assignment_part1_terraform_tfvars.png`

---

#### 1.3 Networking Module (5 marks)

Create a `networking` module that provisions:
- VPC with specified CIDR block
- Subnet with public IP assignment enabled
- Internet Gateway
- Route table with default route to IGW
- Associate route table with subnet

**Module Location:** `modules/networking/`

**Required Outputs:**
- `vpc_id`
- `subnet_id`
- `igw_id`
- `route_table_id`

**Tasks:**
- [ ] Create VPC resource
- [ ] Create subnet with `map_public_ip_on_launch = true`
- [ ] Create and attach Internet Gateway
- [ ] Configure routing table
- [ ] Add proper tags to all resources using `env_prefix`

**Deliverables:**
- Screenshot: `assignment_part1_networking_module_main.png`
- Screenshot: `assignment_part1_networking_module_outputs.png`

---

#### 1.4 Security Module (5 marks)

Create a `security` module that provisions:

**Two Security Groups:**

1. **Nginx Security Group** (for reverse proxy/load balancer):
   - Ingress:  Port 22 (SSH) from your IP only
   - Ingress: Port 80 (HTTP) from anywhere (0.0.0.0/0)
   - Ingress: Port 443 (HTTPS) from anywhere (0.0.0.0/0)
   - Egress: All traffic

2. **Backend Security Group** (for web servers):
   - Ingress: Port 22 (SSH) from your IP only
   - Ingress: Port 80 (HTTP) from Nginx security group only
   - Egress: All traffic

**Module Location:** `modules/security/`

**Required Variables:**
- `vpc_id`
- `env_prefix`
- `my_ip`

**Required Outputs:**
- `nginx_sg_id`
- `backend_sg_id`

**Tasks:**
- [ ] Create Nginx security group with appropriate rules
- [ ] Create backend security group with appropriate rules
- [ ] Use security group IDs for backend ingress (not CIDR blocks)
- [ ] Add descriptive names and tags

**Deliverables:**
- Screenshot: `assignment_part1_security_module. png`
- Screenshot: `assignment_part1_security_groups_console.png` (AWS Console)

---

#### 1.5 Locals Configuration (5 marks)

Create `locals.tf` with:
- Dynamic IP detection for `my_ip`
- Resource naming conventions
- Common tags
- Backend server configurations

**Example:**
```hcl
locals {
  my_ip = "${chomp(data.http.my_ip.response_body)}/32"
  
  common_tags = {
    Environment = var.env_prefix
    Project     = "Lab12-Assignment"
    ManagedBy   = "Terraform"
  }
  
  backend_servers = [
    {
      name        = "web-1"
      suffix      = "1"
      script_path = "./scripts/apache-setup.sh"
    },
    {
      name        = "web-2"
      suffix      = "2"
      script_path = "./scripts/apache-setup.sh"
    },
    {
      name        = "web-3"
      suffix      = "3"
      script_path = "./scripts/apache-setup.sh"
    }
  ]
}

data "http" "my_ip" {
  url = "https://icanhazip.com"
}
```

**Tasks:**
- [ ] Implement dynamic IP detection
- [ ] Define common tags
- [ ] Define backend server list
- [ ] Add any other reusable local values

**Deliverables:**
- Screenshot: `assignment_part1_locals_tf.png`

---

### Part 2: Webserver Module (15 marks)

#### 2.1 Module Design (10 marks)

Create a reusable `webserver` module in `modules/webserver/`

**Module Requirements:**

**Variables:**
- `env_prefix`
- `instance_name`
- `instance_type`
- `availability_zone`
- `vpc_id`
- `subnet_id`
- `security_group_id`
- `public_key`
- `script_path`
- `instance_suffix`
- `common_tags`

**Resources to Create:**
1. AWS Key Pair (unique per instance using suffix)
2. EC2 Instance with: 
   - Specified AMI (Amazon Linux 2023)
   - Public IP enabled
   - User data from script file
   - Proper tags

**Outputs:**
- `instance_id`
- `public_ip`
- `private_ip`

**Tasks:**
- [ ] Create variables. tf with all required variables
- [ ] Create main.tf with key pair and instance resources
- [ ] Create outputs.tf with all required outputs
- [ ] Ensure module is reusable for both Nginx and backend servers

**Deliverables:**
- Screenshot: `assignment_part2_webserver_module_variables.png`
- Screenshot: `assignment_part2_webserver_module_main.png`
- Screenshot: `assignment_part2_webserver_module_outputs.png`

---

#### 2.2 Module Usage (5 marks)

In root `main.tf`, instantiate the webserver module for: 
1. One Nginx server (using `nginx-setup.sh`)
2. Three backend servers (web-1, web-2, web-3 using `apache-setup.sh`)

**Use dynamic blocks or for_each for backend servers:**

```hcl
# Nginx Server
module "nginx_server" {
  source            = "./modules/webserver"
  env_prefix        = var. env_prefix
  instance_name     = "nginx-proxy"
  instance_type     = var.instance_type
  availability_zone = var.availability_zone
  vpc_id            = module.networking.vpc_id
  subnet_id         = module.networking.subnet_id
  security_group_id = module.security. nginx_sg_id
  public_key        = var.public_key
  script_path       = "./scripts/nginx-setup.sh"
  instance_suffix   = "nginx"
  common_tags       = local.common_tags
}

# Backend Servers (use for_each or count)
module "backend_servers" {
  for_each = { for idx, server in local.backend_servers : server.name => server }
  
  source            = "./modules/webserver"
  env_prefix        = var.env_prefix
  instance_name     = each.value.name
  instance_type     = var.instance_type
  availability_zone = var.availability_zone
  vpc_id            = module. networking.vpc_id
  subnet_id         = module.networking. subnet_id
  security_group_id = module.security.backend_sg_id
  public_key        = var.public_key
  script_path       = each.value.script_path
  instance_suffix   = each.value.suffix
  common_tags       = local.common_tags
}
```

**Tasks:**
- [ ] Create Nginx server module instance
- [ ] Create backend server module instances using for_each
- [ ] Ensure proper security group assignment
- [ ] Pass all required variables

**Deliverables:**
- Screenshot: `assignment_part2_main_tf_modules.png`

---

### Part 3: Server Configuration Scripts (20 marks)

#### 3.1 Apache Backend Server Script (10 marks)

Create `scripts/apache-setup.sh` that:
- Updates system packages
- Installs Apache HTTP Server
- Starts and enables Apache service
- Creates custom HTML page displaying: 
  - Server name/hostname
  - Private IP address
  - Public IP address
  - Public DNS hostname
  - Custom message indicating which backend server (web-1, web-2, or web-3)
  - Timestamp of deployment
  - Server status (Primary/Backup)

**Enhanced Script Requirements:**

```bash
#!/bin/bash
set -e

# Update system
yum update -y

# Install Apache
yum install httpd -y

# Start and enable Apache
systemctl start httpd
systemctl enable httpd

# Get metadata token (IMDSv2)
TOKEN=$(curl -s -X PUT "http://169.254.169.254/latest/api/token" \
  -H "X-aws-ec2-metadata-token-ttl-seconds: 21600")

# Get instance metadata
PRIVATE_IP=$(curl -s -H "X-aws-ec2-metadata-token: $TOKEN" \
  http://169.254.169.254/latest/meta-data/local-ipv4)
PUBLIC_IP=$(curl -s -H "X-aws-ec2-metadata-token: $TOKEN" \
  http://169.254.169.254/latest/meta-data/public-ipv4)
PUBLIC_DNS=$(curl -s -H "X-aws-ec2-metadata-token: $TOKEN" \
  http://169.254.169.254/latest/meta-data/public-hostname)
INSTANCE_ID=$(curl -s -H "X-aws-ec2-metadata-token: $TOKEN" \
  http://169.254.169.254/latest/meta-data/instance-id)

# Set hostname
hostnamectl set-hostname myapp-webserver

# Create custom HTML page
cat > /var/www/html/index.html <<EOF
<!DOCTYPE html>
<html>
<head>
    <title>Backend Web Server</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin:  50px;
            background:  linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
        }
        .container {
            background: rgba(255, 255, 255, 0.1);
            padding: 30px;
            border-radius: 10px;
            box-shadow: 0 8px 32px 0 rgba(31, 38, 135, 0.37);
        }
        h1 { color: #fff; text-shadow: 2px 2px 4px rgba(0,0,0,0.3); }
        .info { margin: 15px 0; padding: 10px; background: rgba(255,255,255,0.2); border-radius: 5px; }
        .label { font-weight: bold; color: #ffd700; }
    </style>
</head>
<body>
    <div class="container">
        <h1>🚀 Backend Web Server - Lab 12 Assignment</h1>
        <div class="info"><span class="label">Hostname:</span> $(hostname)</div>
        <div class="info"><span class="label">Instance ID:</span> $INSTANCE_ID</div>
        <div class="info"><span class="label">Private IP:</span> $PRIVATE_IP</div>
        <div class="info"><span class="label">Public IP:</span> $PUBLIC_IP</div>
        <div class="info"><span class="label">Public DNS:</span> $PUBLIC_DNS</div>
        <div class="info"><span class="label">Deployed: </span> $(date)</div>
        <div class="info"><span class="label">Status:</span> ✅ Active and Running</div>
        <div class="info"><span class="label">Managed By:</span> Terraform</div>
    </div>
</body>
</html>
EOF

# Set permissions
chmod 644 /var/www/html/index.html

echo "Apache setup completed successfully!"
```

**Tasks:**
- [ ] Create the script with all required features
- [ ] Ensure it uses IMDSv2 for metadata
- [ ] Create visually appealing HTML output
- [ ] Make script executable
- [ ] Test script independently

**Deliverables:**
- Screenshot: `assignment_part3_apache_script.png`
- Screenshot: `assignment_part3_backend_webpage.png` (browser showing backend server page)

---

#### 3.2 Nginx Server Setup Script (10 marks)

Create `scripts/nginx-setup.sh` that:
- Updates system packages
- Installs Nginx
- Generates self-signed SSL certificate
- Configures Nginx with:
  - HTTPS on port 443
  - HTTP to HTTPS redirect
  - Upstream backend servers (web-1, web-2, web-3)
  - Load balancing configuration
  - Caching configuration
  - Proper logging
  - Security headers

**Enhanced Script Template:**

```bash
#!/bin/bash
set -e

# Update and install Nginx
yum update -y
yum install -y nginx openssl
systemctl start nginx
systemctl enable nginx

# Create SSL directories
mkdir -p /etc/ssl/private
mkdir -p /etc/ssl/certs

# Get metadata token
TOKEN=$(curl -s -X PUT "http://169.254.169.254/latest/api/token" \
  -H "X-aws-ec2-metadata-token-ttl-seconds: 21600")

# Get public IP
PUBLIC_IP=$(curl -s -H "X-aws-ec2-metadata-token: $TOKEN" \
  http://169.254.169.254/latest/meta-data/public-ipv4)

# Generate self-signed certificate
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
  -keyout /etc/ssl/private/selfsigned.key \
  -out /etc/ssl/certs/selfsigned.crt \
  -subj "/CN=$PUBLIC_IP" \
  -addext "subjectAltName=IP:$PUBLIC_IP" \
  -addext "basicConstraints=CA:FALSE" \
  -addext "keyUsage=digitalSignature,keyEncipherment" \
  -addext "extendedKeyUsage=serverAuth"

echo "Self-signed certificate created for IP: $PUBLIC_IP"

# Backup original config
cp /etc/nginx/nginx.conf /etc/nginx/nginx.conf.bak

# Create Nginx configuration
# Note: Backend IPs will be added manually after deployment
cat > /etc/nginx/nginx.conf <<'EOF'
user nginx;
worker_processes auto;
error_log /var/log/nginx/error.log notice;
pid /run/nginx. pid;

events {
    worker_connections 1024;
}

http {
    # Logging
    log_format main '$remote_addr - $remote_user [$time_local] "$request" '
                    '$status $body_bytes_sent "$http_referer" '
                    '"$http_user_agent" "$http_x_forwarded_for" '
                    'Cache:  $upstream_cache_status';

    access_log /var/log/nginx/access.log main;

    # Basic settings
    sendfile on;
    tcp_nopush on;
    keepalive_timeout 65;
    types_hash_max_size 4096;
    
    include /etc/nginx/mime.types;
    default_type application/octet-stream;

    # Gzip compression
    gzip on;
    gzip_vary on;
    gzip_types text/plain text/css application/json application/javascript text/xml application/xml;

    # Cache configuration
    proxy_cache_path /var/cache/nginx 
                     levels=1:2 
                     keys_zone=my_cache:10m 
                     max_size=1g 
                     inactive=60m 
                     use_temp_path=off;

    # Upstream backend servers
    # PLACEHOLDER: Update these IPs after deployment
    upstream backend_servers {
        # Primary servers (active load balancing)
        server BACKEND_IP_1:80;
        server BACKEND_IP_2:80;
        
        # Backup server (only used when primary servers are down)
        server BACKEND_IP_3:80 backup;
    }

    # HTTPS Server
    server {
        listen 443 ssl http2;
        server_name _;

        # SSL Configuration
        ssl_certificate /etc/ssl/certs/selfsigned.crt;
        ssl_certificate_key /etc/ssl/private/selfsigned.key;
        ssl_protocols TLSv1.2 TLSv1.3;
        ssl_ciphers HIGH:!aNULL:! MD5;
        ssl_prefer_server_ciphers on;

        # Security Headers
        add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;
        add_header X-Frame-Options "SAMEORIGIN" always;
        add_header X-Content-Type-Options "nosniff" always;
        add_header X-XSS-Protection "1; mode=block" always;

        # Proxy settings
        location / {
            proxy_pass http://backend_servers;
            
            # Proxy headers
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
            
            # Cache settings
            proxy_cache my_cache;
            proxy_cache_valid 200 60m;
            proxy_cache_valid 404 10m;
            proxy_cache_key "$scheme$request_method$host$request_uri";
            proxy_cache_bypass $http_cache_control;
            add_header X-Cache-Status $upstream_cache_status;
            
            # Timeouts
            proxy_connect_timeout 60s;
            proxy_send_timeout 60s;
            proxy_read_timeout 60s;
        }

        # Health check endpoint
        location /health {
            access_log off;
            return 200 "Nginx is healthy\n";
            add_header Content-Type text/plain;
        }
    }

    # HTTP Server (redirect to HTTPS)
    server {
        listen 80;
        server_name _;
        
        location / {
            return 301 https://$host$request_uri;
        }

        # Allow health checks over HTTP
        location /health {
            access_log off;
            return 200 "Nginx is healthy\n";
            add_header Content-Type text/plain;
        }
    }
}
EOF

# Create cache directory
mkdir -p /var/cache/nginx
chown -R nginx:nginx /var/cache/nginx

# Test and restart Nginx
nginx -t && systemctl restart nginx

echo "Nginx setup completed successfully!"
echo "Remember to update backend server IPs in /etc/nginx/nginx.conf"
```

**Tasks:**
- [ ] Create script with SSL certificate generation
- [ ] Configure upstream with placeholder IPs
- [ ] Implement caching
- [ ] Add security headers
- [ ] Configure HTTP to HTTPS redirect
- [ ] Add health check endpoint

**Deliverables:**
- Screenshot: `assignment_part3_nginx_script.png`
- Screenshot: `assignment_part3_nginx_default_page.png` (before backend configuration)

---

### Part 4: Infrastructure Deployment (15 marks)

#### 4.1 Initial Deployment (5 marks)

Deploy the infrastructure using Terraform. 

**Tasks:**
- [ ] Generate SSH key pair if not exists
- [ ] Initialize Terraform (`terraform init`)
- [ ] Validate configuration (`terraform validate`)
- [ ] Plan deployment (`terraform plan`)
- [ ] Apply configuration (`terraform apply -auto-approve`)

**Deliverables:**
- Screenshot: `assignment_part4_ssh_keygen.png`
- Screenshot: `assignment_part4_terraform_init.png`
- Screenshot: `assignment_part4_terraform_validate.png`
- Screenshot: `assignment_part4_terraform_plan.png`
- Screenshot: `assignment_part4_terraform_apply.png` (showing all 4 instances created)

---

#### 4.2 Output Configuration (5 marks)

Create comprehensive outputs in `outputs.tf`:

```hcl
# Networking Outputs
output "vpc_id" {
  description = "VPC ID"
  value       = module.networking.vpc_id
}

output "subnet_id" {
  description = "Subnet ID"
  value       = module.networking.subnet_id
}

# Nginx Server Outputs
output "nginx_public_ip" {
  description = "Nginx server public IP"
  value       = module.nginx_server.public_ip
}

output "nginx_instance_id" {
  description = "Nginx server instance ID"
  value       = module.nginx_server.instance_id
}

# Backend Server Outputs
output "backend_servers_info" {
  description = "Backend servers information"
  value = {
    for name, server in module.backend_servers : name => {
      instance_id = server.instance_id
      public_ip   = server.public_ip
      private_ip  = server.private_ip
    }
  }
}

# Quick Configuration Guide
output "configuration_guide" {
  value = <<-EOT
    
    ========================================
    DEPLOYMENT SUCCESSFUL! 
    ========================================
    
    Next Steps:
    1. SSH into Nginx server: ssh ec2-user@${module.nginx_server.public_ip}
    2. Edit Nginx config: sudo vim /etc/nginx/nginx.conf
    3. Update backend IPs in upstream block: 
       - BACKEND_IP_1: ${module.backend_servers["web-1"].private_ip}
       - BACKEND_IP_2: ${module. backend_servers["web-2"]. private_ip}
       - BACKEND_IP_3: ${module.backend_servers["web-3"].private_ip}
    4.  Restart Nginx: sudo systemctl restart nginx
    5. Test: https://${module.nginx_server.public_ip}
    
    Backend Servers:
    ${join("\n    ", [for name, server in module.backend_servers : "- ${name}: ${server.public_ip} (private: ${server.private_ip})"])}
    
    ========================================
  EOT
}
```

**Tasks:**
- [ ] Create all required outputs
- [ ] Add helpful descriptions
- [ ] Include configuration guide
- [ ] Display outputs after apply

**Commands:**
```bash
terraform output
terraform output -json > outputs.json
```

**Deliverables:**
- Screenshot: `assignment_part4_terraform_output.png`
- Screenshot: `assignment_part4_outputs_json.png`

---

#### 4.3 AWS Console Verification (5 marks)

Verify all resources in AWS Console. 

**Tasks:**
- [ ] Verify VPC created
- [ ] Verify Subnet created
- [ ] Verify Internet Gateway attached
- [ ] Verify Route Table configured
- [ ] Verify Security Groups created with correct rules
- [ ] Verify all 4 EC2 instances running
- [ ] Verify Key Pairs created

**Deliverables:**
- Screenshot: `assignment_part4_aws_vpc. png` (VPC console)
- Screenshot: `assignment_part4_aws_subnet.png` (Subnet details)
- Screenshot: `assignment_part4_aws_security_groups.png` (Both security groups)
- Screenshot: `assignment_part4_aws_instances.png` (All 4 running instances)

---

### Part 5: Nginx Configuration & Testing (25 marks)

#### 5.1 Update Nginx Backend Configuration (5 marks)

SSH into the Nginx server and update the configuration with actual backend IPs.

**Tasks:**
- [ ] SSH into Nginx server
- [ ] Edit `/etc/nginx/nginx.conf`
- [ ] Replace placeholder IPs with actual private IPs of backend servers
- [ ] Test Nginx configuration
- [ ] Restart Nginx service

**Commands:**
```bash
# SSH into Nginx server
ssh ec2-user@<nginx-public-ip>

# Edit configuration
sudo vim /etc/nginx/nginx.conf

# Update upstream block: 
upstream backend_servers {
    server <web-1-private-ip>:80;
    server <web-2-private-ip>:80;
    server <web-3-private-ip>:80 backup;
}

# Test configuration
sudo nginx -t

# Restart Nginx
sudo systemctl restart nginx

# Check status
sudo systemctl status nginx
```

**Deliverables:**
- Screenshot: `assignment_part5_ssh_nginx.png` (SSH session)
- Screenshot: `assignment_part5_nginx_conf_updated.png` (updated upstream block)
- Screenshot: `assignment_part5_nginx_test.png` (nginx -t output)
- Screenshot: `assignment_part5_nginx_restart.png` (restart and status)

---

#### 5.2 Test Load Balancing (5 marks)

Test that Nginx is properly load balancing between web-1 and web-2.

**Tasks:**
- [ ] Open browser to `https://<nginx-public-ip>`
- [ ] Accept the security warning for self-signed certificate
- [ ] Reload page multiple times (at least 10 times)
- [ ] Verify traffic alternates between web-1 and web-2
- [ ] Verify web-3 is NOT serving traffic (it's backup only)
- [ ] Document the load balancing pattern

**Deliverables:**
- Screenshot:  `assignment_part5_ssl_warning.png` (browser security warning)
- Screenshot: `assignment_part5_web1_response.png` (showing web-1 content)
- Screenshot: `assignment_part5_web2_response. png` (showing web-2 content)
- Screenshot: `assignment_part5_load_balancing_demo.png` (multiple reloads showing alternation)

---

#### 5.3 Test Cache Functionality (5 marks)

Verify that Nginx caching is working correctly.

**Tasks:**
- [ ] Open browser developer tools (F12)
- [ ] Navigate to Network tab
- [ ] Clear browser cache
- [ ] Load `https://<nginx-public-ip>`
- [ ] Check response headers for `X-Cache-Status:  MISS` (first request)
- [ ] Reload page
- [ ] Check response headers for `X-Cache-Status: HIT` (cached request)
- [ ] Verify cache directory on Nginx server

**Commands on Nginx server:**
```bash
# Check cache directory
ls -la /var/cache/nginx/

# Monitor access logs
sudo tail -f /var/log/nginx/access.log

# Check cache status in logs
sudo grep "Cache:" /var/log/nginx/access.log | tail -20
```

**Deliverables:**
- Screenshot: `assignment_part5_cache_miss.png` (first request - MISS)
- Screenshot: `assignment_part5_cache_hit. png` (second request - HIT)
- Screenshot: `assignment_part5_cache_directory.png` (cache folder contents)
- Screenshot: `assignment_part5_access_log_cache.png` (access log showing cache status)

---

#### 5.4 Test High Availability (Backup Server) (5 marks)

Test the backup server functionality by simulating primary server failure.

**Tasks:**
- [ ] SSH into web-1 and stop Apache
- [ ] Reload Nginx page - should show web-2 only
- [ ] SSH into web-2 and stop Apache
- [ ] Reload Nginx page - should now show web-3 (backup activated)
- [ ] Restart web-1 and web-2
- [ ] Verify traffic returns to web-1 and web-2

**Commands:**
```bash
# On web-1
ssh ec2-user@<web-1-public-ip>
sudo systemctl stop httpd
sudo systemctl status httpd

# On web-2
ssh ec2-user@<web-2-public-ip>
sudo systemctl stop httpd

# Verify on Nginx
ssh ec2-user@<nginx-public-ip>
sudo tail -f /var/log/nginx/error.log

# Restart services
sudo systemctl start httpd
```

**Deliverables:**
- Screenshot: `assignment_part5_web1_stopped.png` (web-1 Apache stopped)
- Screenshot: `assignment_part5_web2_stopped.png` (web-2 Apache stopped)
- Screenshot: `assignment_part5_backup_activated.png` (web-3 serving traffic)
- Screenshot: `assignment_part5_nginx_error_log.png` (error log showing backend failures)
- Screenshot: `assignment_part5_services_restored.png` (all services back online)

---

#### 5.5 Security & Performance Analysis (5 marks)

Analyze the security headers and performance of your Nginx setup.

**Tasks:**
- [ ] Check SSL/TLS certificate details
- [ ] Verify security headers in response
- [ ] Test HTTP to HTTPS redirect
- [ ] Check response times
- [ ] Analyze Nginx logs

**Tools & Commands:**
```bash
# Check certificate
openssl s_client -connect <nginx-ip>:443 -showcerts

# On Nginx server - view certificate
sudo openssl x509 -in /etc/ssl/certs/selfsigned.crt -text -noout

# Check security headers (from local machine)
curl -I -k https://<nginx-public-ip>

# Test HTTP redirect
curl -I http://<nginx-public-ip>

# Monitor error logs
sudo tail -50 /var/log/nginx/error.log

# Monitor access logs
sudo tail -50 /var/log/nginx/access.log

# Check Nginx worker processes
ps aux | grep nginx
```

**Deliverables:**
- Screenshot: `assignment_part5_ssl_certificate.png` (certificate details)
- Screenshot: `assignment_part5_security_headers. png` (response headers showing security headers)
- Screenshot: `assignment_part5_http_redirect.png` (301 redirect test)
- Screenshot: `assignment_part5_error_log_analysis.png` (error log review)
- Screenshot: `assignment_part5_access_log_analysis.png` (access log patterns)

---

## Bonus Tasks (10 marks extra credit)

### Bonus 1: Custom Error Pages (3 marks)

Create custom error pages for Nginx (404, 502, 503).

**Tasks:**
- [ ] Create custom HTML error pages
- [ ] Configure Nginx to use custom error pages
- [ ] Test by accessing non-existent URL and stopping backend servers

**Deliverables:**
- Screenshot:  `bonus1_custom_404.png`
- Screenshot: `bonus1_custom_502.png`

---

### Bonus 2: Implement Rate Limiting (3 marks)

Add rate limiting to prevent abuse. 

**Configuration Example:**
```nginx
http {
    limit_req_zone $binary_remote_addr zone=mylimit:10m rate=10r/s;
    
    server {
        location / {
            limit_req zone=mylimit burst=20;
            proxy_pass http://backend_servers;
        }
    }
}
```

**Tasks:**
- [ ] Implement rate limiting
- [ ] Test with rapid requests
- [ ] Show 429 (Too Many Requests) response

**Deliverables:**
- Screenshot: `bonus2_rate_limit_config.png`
- Screenshot: `bonus2_rate_limit_test.png`

---

### Bonus 3: Health Check Automation (4 marks)

Create a shell script that monitors backend server health.

**Script Requirements:**
- Check each backend server every 30 seconds
- Log health status
- Alert if server is down
- Restart Apache if needed

**Deliverables:**
- Health check script
- Screenshot: `bonus3_health_check_script.png`
- Screenshot: `bonus3_health_log.png`

---

## Part 6: Documentation & Cleanup (10 marks)

### 6.1 README Documentation (5 marks)

Create a comprehensive `README.md` with:

**Required Sections:**
1. **Project Overview**
   - Architecture diagram (can be text-based or image)
   - Components description
   
2. **Prerequisites**
   - Required tools
   - AWS credentials setup
   - SSH key setup

3. **Deployment Instructions**
   - Step-by-step guide
   - Variable configuration
   - Terraform commands

4. **Configuration Guide**
   - How to update backend IPs
   - Nginx configuration explanation
   - Testing procedures

5. **Architecture Details**
   - Network topology
   - Security groups explanation
   - Load balancing strategy

6. **Troubleshooting**
   - Common issues and solutions
   - Log locations
   - Debug commands

**Example Structure:**

## Assignment 2 - Multi-Tier Web Infrastructure

### Architecture Overview

```markdown
┌─────────────────────────────────────────────────┐
│                  Internet                       │
└─────────────────┬───────────────────────────────┘
                  │
                  │ HTTPS (443)
                  │ HTTP (80)
                  ▼
         ┌────────────────────┐
         │   Nginx Server     │
         │  (Load Balancer)   │
         │   - SSL/TLS        │
         │   - Caching        │
         │   - Reverse Proxy  │
         └────────┬───────────┘
                  │
      ┌───────────┼───────────┐
      │           │           │
      ▼           ▼           ▼
   ┌─────┐     ┌─────┐     ┌─────┐
   │Web-1│     │Web-2│     │Web-3│
   │     │     │     │     │(BKP)│
   └─────┘     └─────┘     └─────┘
   Primary     Primary     Backup
```

## Components

**Deliverables:**
- Screenshot: `assignment_part6_readme.png` (README.md content)



### 6.2 Infrastructure Cleanup (5 marks)

Properly destroy all resources and verify cleanup.

**Tasks:**
- [ ] Run terraform destroy
- [ ] Confirm resource deletion in AWS Console
- [ ] Verify no remaining resources
- [ ] Document the destruction process

**Commands:**
```bash
# Destroy infrastructure
terraform destroy

# Type 'yes' when prompted

# Verify state file is empty
cat terraform.tfstate

# List any remaining resources (should be empty)
aws ec2 describe-instances --filters "Name=tag:Project,Values=Assignment2" --query "Reservations[]. Instances[].InstanceId"
```

**Deliverables:**
- Screenshot: `assignment_part6_terraform_destroy_prompt.png`
- Screenshot: `assignment_part6_terraform_destroy_complete.png`
- Screenshot: `assignment_part6_aws_instances_destroyed.png` (AWS Console showing no instances)
- Screenshot: `assignment_part6_empty_state. png` (empty terraform.tfstate)

---

## Submission Guidelines

### 1. GitHub Repository

Create a public GitHub repository named: 
```
CC_<YourName>_<YourRollNumber>/Lab12_Assignment
```

**Repository Structure:**
```
Lab12_Assignment/
├── README. md
├── main.tf
├── variables.tf
├── outputs.tf
├── locals.tf
├── terraform.tfvars. example    # Example file (NO REAL VALUES)
├── .gitignore
├── modules/
│   ├── networking/
│   │   ├── main.tf
│   │   ├── variables.tf
│   │   └── outputs.tf
│   ├── security/
│   │   ├── main.tf
│   │   ├── variables.tf
│   │   └── outputs.tf
│   └── webserver/
│       ├── main.tf
│       ├── variables.tf
│       └── outputs.tf
├── scripts/
│   ├── nginx-setup.sh
│   └── apache-setup.sh
├── screenshots/
│   ├── part1/
│   ├── part2/
│   ├── part3/
│   ├── part4/
│   ├── part5/
│   ├── part6/
│   └── bonus/
└── docs/
    ├── architecture. md
    └── troubleshooting.md
```

**Important:**
- ✅ DO commit:  `.tf` files, scripts, README, screenshots
- ❌ DON'T commit:  `terraform.tfstate`, `.terraform/`, `*.tfvars` (except example), `*.pem`, AWS credentials

---

### 2. PDF Report

Create a comprehensive PDF report named:
```
Lab12_Assignment_<YourName>_<YourRollNumber>.pdf
```

**Report Structure:**

**Cover Page:**
- Assignment title
- Your name and roll number
- Submission date
- Course name

**Table of Contents**

**1. Executive Summary** (1 page)
- Brief overview of the assignment
- Infrastructure deployed
- Key achievements

**2. Architecture Design** (2-3 pages)
- Architecture diagram
- Component descriptions
- Network topology
- Security design

**3. Implementation Details** (10-15 pages)
- Part 1: Infrastructure setup (with screenshots)
- Part 2: Webserver module (with screenshots)
- Part 3: Server scripts (with screenshots and code snippets)
- Part 4: Deployment (with screenshots)
- Part 5: Testing and verification (with screenshots)
- Part 6: Cleanup (with screenshots)

**4. Testing Results** (3-5 pages)
- Load balancing tests
- Cache performance tests
- High availability tests
- Security tests
- Performance metrics

**5. Challenges & Solutions** (1-2 pages)
- Problems encountered
- How you solved them
- Lessons learned

**6. Conclusion** (1 page)
- Summary of work completed
- Skills acquired
- Future improvements

**7. Appendices**
- Complete code listings
- Configuration files
- Additional screenshots
- References

---

### 3. Submission Checklist

Before submitting, verify: 

- [ ] GitHub repository is public and accessible
- [ ] All required files are committed
- [ ] `.gitignore` properly configured
- [ ] README.md is comprehensive
- [ ] All screenshots are clear and labeled
- [ ] No sensitive information committed (keys, passwords, real IPs in code)
- [ ] terraform.tfvars.example provided (not real tfvars)
- [ ] PDF report is complete with all sections
- [ ] All screenshots are embedded in PDF
- [ ] Code snippets are properly formatted in PDF
- [ ] Repository link is included in PDF
- [ ] PDF is named correctly
- [ ] File size is reasonable (compress images if needed)

---

### 4. Submission Method

**Submit via [Your Learning Management System]:**

1. **GitHub Repository URL**
   - Paste the full URL to your repository

2. **PDF Report**
   - Upload the PDF file

3. **Submission Form** (if required)
   - Name: 
   - Roll Number:
   - GitHub Repository:
   - Number of screenshots:
   - Bonus tasks completed: 
   - Hours spent: 
   - Self-assessment score (/100):

---

## Grading Rubric

| Component | Points | Criteria |
|-----------|--------|----------|
| **Part 1: Infrastructure Setup** | 25 | Project structure, variables, modules (networking & security), locals |
| **Part 2: Webserver Module** | 15 | Module design, reusability, proper variable usage |
| **Part 3: Server Scripts** | 20 | Apache script, Nginx script, functionality, code quality |
| **Part 4: Deployment** | 15 | Successful deployment, outputs, AWS verification |
| **Part 5: Testing** | 25 | Load balancing, caching, HA, security, performance analysis |
| **Part 6: Documentation** | 10 | README quality, cleanup verification |
| **Bonus Tasks** | +10 | Extra credit for bonus implementations |
| **Code Quality** | -5 to +5 | Code organization, comments, best practices |
| **Report Quality** | -5 to +5 | Clarity, completeness, professionalism |
| **TOTAL** | **100** | (+10 bonus possible) |

---

## Detailed Grading Criteria

### Excellent (90-100%)
- All requirements met perfectly
- Clean, well-organized code
- Comprehensive documentation
- All tests passing with screenshots
- Creative bonus implementations
- Professional report

### Good (80-89%)
- Most requirements met
- Code works with minor issues
- Good documentation
- Most tests passing
- Clear screenshots
- Complete report

### Satisfactory (70-79%)
- Basic requirements met
- Code works but needs improvement
- Adequate documentation
- Some tests passing
- Screenshots present but may lack detail
- Report covers main points

### Needs Improvement (60-69%)
- Some requirements missing
- Code has significant issues
- Minimal documentation
- Limited testing
- Few screenshots
- Incomplete report

### Unsatisfactory (<60%)
- Major requirements missing
- Code doesn't work
- Poor or no documentation
- No testing evidence
- Missing screenshots
- Inadequate report

---

## Tips for Success

### 1. Start Early
- Don't wait until the last day
- Test incrementally as you build
- Debug issues early

### 2. Test Thoroughly
- Test each component before moving to the next
- Document test results immediately
- Keep logs of any errors

### 3. Take Good Screenshots
- Ensure screenshots are clear and readable
- Crop appropriately to show relevant information
- Label screenshots clearly
- Organize in folders by part

### 4. Document As You Go
- Write README sections as you complete each part
- Take notes on problems and solutions
- Document configuration decisions

### 5. Follow Best Practices
- Use meaningful variable names
- Add comments to complex code
- Follow Terraform style guide
- Keep code DRY (Don't Repeat Yourself)

### 6. Security First
- Never commit sensitive data
- Use . gitignore properly
- Rotate any exposed credentials
- Follow principle of least privilege

### 7. Version Control
- Commit frequently with meaningful messages
- Use branches for experimentation
- Tag your final submission

---

## Common Issues & Solutions

### Issue 1: Terraform State Lock
**Problem:** State file is locked  
**Solution:**
```bash
terraform force-unlock <lock-id>
```

### Issue 2: Backend Servers Not Reachable
**Problem:** Nginx shows 502 Bad Gateway  
**Solution:**
- Check security group rules
- Verify backend private IPs in Nginx config
- Ensure Apache is running on backends
- Check Nginx error logs

### Issue 3: SSL Certificate Warnings
**Problem:** Browser shows security warning  
**Solution:**
- This is expected with self-signed certificates
- Click "Advanced" and "Accept Risk"
- Document this in your report

### Issue 4: Cache Not Working
**Problem:** X-Cache-Status always shows MISS  
**Solution:**
- Verify cache directory exists and has correct permissions
- Check Nginx config syntax
- Restart Nginx service
- Clear browser cache before testing

### Issue 5: Terraform Destroy Fails
**Problem:** Resources won't delete  
**Solution:**
```bash
# Manually delete resources in AWS Console
# Then remove from state
terraform state rm <resource>
# Or start fresh
rm -rf .terraform terraform.tfstate*
terraform init
```

---

## Resources

### Terraform Documentation
- [Terraform AWS Provider](https://registry.terraform.io/providers/hashicorp/aws/latest/docs)
- [Terraform Modules](https://developer.hashicorp.com/terraform/language/modules)
- [Terraform Variables](https://developer.hashicorp.com/terraform/language/values/variables)

### Nginx Documentation
- [Nginx Reverse Proxy](https://docs.nginx.com/nginx/admin-guide/web-server/reverse-proxy/)
- [Nginx Load Balancing](https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/)
- [Nginx Caching](https://docs.nginx.com/nginx/admin-guide/content-cache/content-caching/)
- [Nginx SSL/TLS](https://nginx.org/en/docs/http/configuring_https_servers.html)

### AWS Documentation
- [EC2 User Guide](https://docs.aws.amazon.com/ec2/)
- [VPC User Guide](https://docs.aws.amazon.com/vpc/)
- [Security Groups](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html)

---

## FAQ

**Q: Can I use a different AWS region?**  
A: Yes, but update all references to the region in your code and documentation.

**Q: Can I use different instance types?**  
A: Yes, but ensure they're available in your region and within free tier if cost is a concern.

**Q:  How long should deployment take?**  
A: Initial terraform apply should complete in 3-5 minutes. 

**Q: Can I work in pairs?**  
A: No.

**Q: What if I exceed AWS free tier?**  
A: Monitor your usage.  Destroy resources when not actively working.  Use t3.micro instances. 

**Q: Can I use an existing VPC?**  
A: No, you must create all resources using Terraform as specified.

**Q: How do I handle the self-signed certificate warning?**  
A: This is expected behavior. Document it and show how to bypass it safely.

**Q: Can I use different web servers instead of Apache?**  
A:  Stick to the requirements for consistency in grading.

---

## Academic Integrity

- This is an individual assignment
- You may discuss concepts but not share code
- Cite any external resources used
- Plagiarism will result in zero marks and disciplinary action
- All submitted code must be your own work

---

## Support

If you encounter issues: 

1. **Check the troubleshooting section** in this document
2. **Review lab materials** from Lab 12
3. **Consult documentation** (Terraform, Nginx, AWS)
4. **Post in course forum** (don't share complete code)
5. **Attend office hours** for guidance
6. **Email instructor** as last resort

---

## Deadline

**Due Date:** 28-12-2025  
**Time:** 11:59 pm  
**Late Policy:** 50% marks reduction

---

## Final Checklist

Before final submission: 

### Code Quality
- [ ] All Terraform code is properly formatted (`terraform fmt`)
- [ ] All Terraform configurations validate (`terraform validate`)
- [ ] No hardcoded values (use variables)
- [ ] Meaningful variable and resource names
- [ ] Comments explain complex logic
- [ ] No sensitive data in code

### Functionality
- [ ] All 4 instances deploy successfully
- [ ] Load balancing works between web-1 and web-2
- [ ] Web-3 acts as backup only
- [ ] Caching works (verified with headers)
- [ ] HTTPS works with self-signed cert
- [ ] HTTP redirects to HTTPS
- [ ] All security groups configured correctly

### Documentation
- [ ] README.md is comprehensive
- [ ] All screenshots are clear and labeled
- [ ] Architecture diagram included
- [ ] Code is commented
- [ ] PDF report is complete
- [ ] No spelling/grammar errors

### Submission
- [ ] GitHub repo is public
- [ ] Correct naming convention
- [ ] . gitignore configured
- [ ] No sensitive files committed
- [ ] PDF uploaded
- [ ] Repository URL submitted

---

**Good luck with your assignment!  🚀**

**Remember:**
- Start early
- Test incrementally  
- Document everything
- Ask for help when needed
- Have fun learning!

---

*End of Assignment*
