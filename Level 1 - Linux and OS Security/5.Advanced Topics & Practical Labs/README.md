# Day 23-27: Advanced Topics & Practical Labs - Complete Guide

## Overview
This advanced module brings together all the skills learned in previous modules through hands-on practical labs, real-world scenarios, and advanced security topics. You'll work on comprehensive projects that simulate production environments, implement automation, and tackle complex security challenges. This is where theory meets practice.

**Duration**: 5 Days (Days 23-27)
**Prerequisites**: All previous modules completed
**Difficulty**: Advanced/Expert

---

## Module 1: Advanced Bash Scripting and Automation (Day 23)

### 1.1 Advanced Bash Scripting Techniques

#### Script Structure and Best Practices
```bash
#!/bin/bash
#
# Script Name: advanced-script.sh
# Description: Advanced bash scripting example
# Author: Your Name
# Date: 2024-01-01
# Version: 1.0
#
# Usage: ./advanced-script.sh [options]
#

# Strict mode
set -euo pipefail
IFS=$'\n\t'

# Script directory
SCRIPT_DIR="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"

# Configuration
readonly CONFIG_FILE="${SCRIPT_DIR}/config.conf"
readonly LOG_FILE="/var/log/script.log"
readonly LOCK_FILE="/var/run/script.lock"

# Colors for output
readonly RED='\033[0;31m'
readonly GREEN='\033[0;32m'
readonly YELLOW='\033[1;33m'
readonly NC='\033[0m' # No Color

# Logging function
log() {
    local level="$1"
    shift
    echo "[$(date +'%Y-%m-%d %H:%M:%S')] [$level] $*" | tee -a "$LOG_FILE"
}

# Error handling
error_exit() {
    log "ERROR" "$1"
    exit 1
}

# Cleanup function
cleanup() {
    log "INFO" "Cleaning up..."
    rm -f "$LOCK_FILE"
}

# Trap signals
trap cleanup EXIT
trap 'error_exit "Script interrupted"' INT TERM

# Lock file to prevent concurrent execution
if [ -f "$LOCK_FILE" ]; then
    error_exit "Script is already running"
fi
touch "$LOCK_FILE"

# Main function
main() {
    log "INFO" "Script started"
    
    # Your code here
    
    log "INFO" "Script completed successfully"
}

# Run main function
main "$@"
```

#### Advanced Functions
```bash
# Function with return values
check_service() {
    local service="$1"
    if systemctl is-active --quiet "$service"; then
        return 0
    else
        return 1
    fi
}

# Function with multiple return values
get_system_info() {
    local -n result=$1
    result[hostname]=$(hostname)
    result[kernel]=$(uname -r)
    result[uptime]=$(uptime -p)
    result[load]=$(uptime | awk -F'load average:' '{print $2}')
}

# Usage
declare -A sysinfo
get_system_info sysinfo
echo "Hostname: ${sysinfo[hostname]}"

# Error handling in functions
safe_command() {
    local cmd="$1"
    if ! eval "$cmd"; then
        log "ERROR" "Command failed: $cmd"
        return 1
    fi
    return 0
}

# Parallel execution
parallel_tasks() {
    local -a pids=()
    
    for task in task1 task2 task3; do
        process_task "$task" &
        pids+=($!)
    done
    
    # Wait for all tasks
    for pid in "${pids[@]}"; do
        wait "$pid" || log "ERROR" "Task failed: $pid"
    done
}
```

#### Advanced String Manipulation
```bash
# String operations
text="Hello World"
echo "${text,,}"           # lowercase: hello world
echo "${text^^}"           # uppercase: HELLO WORLD
echo "${text:0:5}"         # substring: Hello
echo "${text/World/Bash}"  # replace: Hello Bash
echo "${text//o/0}"        # replace all: Hell0 W0rld

# Array operations
arr=(one two three four five)
echo "${arr[@]}"           # all elements
echo "${arr[0]}"           # first element
echo "${#arr[@]}"          # array length
echo "${arr[@]:1:3}"       # slice: two three four

# Associative arrays
declare -A config
config[host]="localhost"
config[port]="8080"
config[user]="admin"

for key in "${!config[@]}"; do
    echo "$key: ${config[$key]}"
done
```


#### Command-Line Argument Parsing
```bash
#!/bin/bash

# Usage function
usage() {
    cat << EOF
Usage: $0 [OPTIONS]

Options:
    -h, --help          Show this help message
    -v, --verbose       Enable verbose output
    -f, --file FILE     Input file
    -o, --output DIR    Output directory
    -n, --number NUM    Number of iterations
EOF
    exit 1
}

# Default values
VERBOSE=false
INPUT_FILE=""
OUTPUT_DIR="/tmp"
ITERATIONS=1

# Parse arguments
while [[ $# -gt 0 ]]; do
    case $1 in
        -h|--help)
            usage
            ;;
        -v|--verbose)
            VERBOSE=true
            shift
            ;;
        -f|--file)
            INPUT_FILE="$2"
            shift 2
            ;;
        -o|--output)
            OUTPUT_DIR="$2"
            shift 2
            ;;
        -n|--number)
            ITERATIONS="$2"
            shift 2
            ;;
        *)
            echo "Unknown option: $1"
            usage
            ;;
    esac
done

# Validate required arguments
if [ -z "$INPUT_FILE" ]; then
    echo "Error: Input file is required"
    usage
fi

# Use getopts for short options
while getopts "hvf:o:n:" opt; do
    case $opt in
        h) usage ;;
        v) VERBOSE=true ;;
        f) INPUT_FILE="$OPTARG" ;;
        o) OUTPUT_DIR="$OPTARG" ;;
        n) ITERATIONS="$OPTARG" ;;
        \?) usage ;;
    esac
done
```

### 1.2 System Monitoring Scripts

#### Comprehensive System Monitor
```bash
#!/bin/bash
# system-monitor.sh - Comprehensive system monitoring

REPORT_FILE="/var/log/system-monitor-$(date +%Y%m%d-%H%M%S).txt"
THRESHOLD_CPU=80
THRESHOLD_MEM=80
THRESHOLD_DISK=85

# Initialize report
cat > "$REPORT_FILE" << EOF
System Monitoring Report
Generated: $(date)
Hostname: $(hostname)
========================================

EOF

# CPU Usage
echo "CPU Usage:" >> "$REPORT_FILE"
top -bn1 | grep "Cpu(s)" >> "$REPORT_FILE"
echo "" >> "$REPORT_FILE"

# Top CPU processes
echo "Top 10 CPU Processes:" >> "$REPORT_FILE"
ps aux --sort=-%cpu | head -11 >> "$REPORT_FILE"
echo "" >> "$REPORT_FILE"

# Memory Usage
echo "Memory Usage:" >> "$REPORT_FILE"
free -h >> "$REPORT_FILE"
echo "" >> "$REPORT_FILE"

# Top Memory processes
echo "Top 10 Memory Processes:" >> "$REPORT_FILE"
ps aux --sort=-%mem | head -11 >> "$REPORT_FILE"
echo "" >> "$REPORT_FILE"

# Disk Usage
echo "Disk Usage:" >> "$REPORT_FILE"
df -h >> "$REPORT_FILE"
echo "" >> "$REPORT_FILE"

# Network Connections
echo "Network Connections:" >> "$REPORT_FILE"
ss -tuln >> "$REPORT_FILE"
echo "" >> "$REPORT_FILE"

# System Load
echo "System Load:" >> "$REPORT_FILE"
uptime >> "$REPORT_FILE"
echo "" >> "$REPORT_FILE"

# Check thresholds and alert
CPU_USAGE=$(top -bn1 | grep "Cpu(s)" | awk '{print $2}' | cut -d'%' -f1)
MEM_USAGE=$(free | grep Mem | awk '{print ($3/$2) * 100.0}' | cut -d'.' -f1)

if (( $(echo "$CPU_USAGE > $THRESHOLD_CPU" | bc -l) )); then
    echo "ALERT: CPU usage is ${CPU_USAGE}%" | tee -a "$REPORT_FILE"
fi

if [ "$MEM_USAGE" -gt "$THRESHOLD_MEM" ]; then
    echo "ALERT: Memory usage is ${MEM_USAGE}%" | tee -a "$REPORT_FILE"
fi

# Check disk usage
while read line; do
    usage=$(echo "$line" | awk '{print $5}' | sed 's/%//')
    partition=$(echo "$line" | awk '{print $6}')
    if [ "$usage" -gt "$THRESHOLD_DISK" ]; then
        echo "ALERT: Disk usage on $partition is ${usage}%" | tee -a "$REPORT_FILE"
    fi
done < <(df -h | grep -v "Filesystem" | grep -v "tmpfs")

echo "Report saved to: $REPORT_FILE"
```

#### Automated Backup Script
```bash
#!/bin/bash
# backup-script.sh - Automated backup with rotation

BACKUP_SOURCE="/home /etc /var/www"
BACKUP_DEST="/backup"
BACKUP_NAME="backup-$(date +%Y%m%d-%H%M%S)"
RETENTION_DAYS=7
LOG_FILE="/var/log/backup.log"

log() {
    echo "[$(date +'%Y-%m-%d %H:%M:%S')] $*" | tee -a "$LOG_FILE"
}

# Create backup directory
mkdir -p "$BACKUP_DEST"

# Create backup
log "Starting backup..."
tar -czf "${BACKUP_DEST}/${BACKUP_NAME}.tar.gz" $BACKUP_SOURCE 2>&1 | tee -a "$LOG_FILE"

if [ ${PIPESTATUS[0]} -eq 0 ]; then
    log "Backup completed successfully: ${BACKUP_NAME}.tar.gz"
    
    # Calculate size
    SIZE=$(du -h "${BACKUP_DEST}/${BACKUP_NAME}.tar.gz" | cut -f1)
    log "Backup size: $SIZE"
else
    log "ERROR: Backup failed"
    exit 1
fi

# Rotate old backups
log "Rotating old backups (keeping last $RETENTION_DAYS days)..."
find "$BACKUP_DEST" -name "backup-*.tar.gz" -mtime +$RETENTION_DAYS -delete

# List current backups
log "Current backups:"
ls -lh "$BACKUP_DEST"/backup-*.tar.gz | tee -a "$LOG_FILE"

# Optional: Upload to remote server
# rsync -avz "${BACKUP_DEST}/${BACKUP_NAME}.tar.gz" user@remote:/backups/

log "Backup process completed"
```

### 1.3 Security Automation Scripts

#### Security Audit Script
```bash
#!/bin/bash
# security-audit.sh - Comprehensive security audit

REPORT="/tmp/security-audit-$(date +%Y%m%d).txt"

exec > >(tee "$REPORT")
exec 2>&1

echo "================================"
echo "Security Audit Report"
echo "Date: $(date)"
echo "Hostname: $(hostname)"
echo "================================"

# Check for users with UID 0
echo -e "\n[*] Users with UID 0 (root privileges):"
awk -F: '($3 == 0) {print $1}' /etc/passwd

# Check for users without passwords
echo -e "\n[*] Users without passwords:"
sudo awk -F: '($2 == "" || $2 == "!") {print $1}' /etc/shadow

# Check for accounts with no password aging
echo -e "\n[*] Accounts with no password expiration:"
sudo awk -F: '($5 == "" || $5 == "99999") {print $1}' /etc/shadow

# Check world-writable files
echo -e "\n[*] World-writable files (first 20):"
find / -xdev -type f -perm -0002 -ls 2>/dev/null | head -20

# Check SUID/SGID files
echo -e "\n[*] SUID files:"
find / -xdev -type f -perm -4000 -ls 2>/dev/null

echo -e "\n[*] SGID files:"
find / -xdev -type f -perm -2000 -ls 2>/dev/null

# Check listening ports
echo -e "\n[*] Listening ports:"
ss -tlnp

# Check failed login attempts
echo -e "\n[*] Recent failed login attempts:"
grep "Failed password" /var/log/auth.log 2>/dev/null | tail -20

# Check sudo usage
echo -e "\n[*] Recent sudo usage:"
grep sudo /var/log/auth.log 2>/dev/null | tail -20

# Check for suspicious processes
echo -e "\n[*] Processes running as root:"
ps aux | awk '$1 == "root"' | head -20

# Check firewall status
echo -e "\n[*] Firewall status:"
if command -v ufw &> /dev/null; then
    sudo ufw status verbose
elif command -v firewall-cmd &> /dev/null; then
    sudo firewall-cmd --list-all
fi

# Check for updates
echo -e "\n[*] Available security updates:"
if command -v apt &> /dev/null; then
    apt list --upgradable 2>/dev/null | grep -i security
elif command -v yum &> /dev/null; then
    yum list updates --security
fi

echo -e "\n================================"
echo "Audit complete. Report saved to: $REPORT"
```

---

## Module 2: Containerization and Virtualization (Day 24)

### 2.1 Docker Fundamentals

#### Installing Docker
```bash
# Install Docker on Ubuntu
sudo apt update
sudo apt install -y apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io

# Add user to docker group
sudo usermod -aG docker $USER

# Start and enable Docker
sudo systemctl start docker
sudo systemctl enable docker

# Verify installation
docker --version
docker run hello-world
```


#### Basic Docker Commands
```bash
# Image management
docker images                    # List images
docker pull nginx               # Pull image
docker rmi image_name           # Remove image
docker build -t myapp:1.0 .     # Build image
docker tag myapp:1.0 myapp:latest  # Tag image

# Container management
docker ps                       # List running containers
docker ps -a                    # List all containers
docker run -d -p 80:80 nginx   # Run container
docker stop container_id        # Stop container
docker start container_id       # Start container
docker restart container_id     # Restart container
docker rm container_id          # Remove container
docker logs container_id        # View logs
docker exec -it container_id bash  # Execute command

# System management
docker system df                # Show disk usage
docker system prune             # Clean up
docker volume ls                # List volumes
docker network ls               # List networks
```

#### Creating a Dockerfile
```dockerfile
# Dockerfile for a Python web application
FROM python:3.9-slim

# Set working directory
WORKDIR /app

# Install dependencies
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Copy application
COPY . .

# Create non-root user
RUN useradd -m -u 1000 appuser && \
    chown -R appuser:appuser /app
USER appuser

# Expose port
EXPOSE 8000

# Health check
HEALTHCHECK --interval=30s --timeout=3s \
    CMD curl -f http://localhost:8000/health || exit 1

# Run application
CMD ["python", "app.py"]
```

#### Docker Compose Example
```yaml
# docker-compose.yml
version: '3.8'

services:
  web:
    build: .
    ports:
      - "8000:8000"
    environment:
      - DATABASE_URL=postgresql://db:5432/myapp
    depends_on:
      - db
    volumes:
      - ./app:/app
    restart: unless-stopped
    networks:
      - app-network

  db:
    image: postgres:13
    environment:
      - POSTGRES_DB=myapp
      - POSTGRES_USER=dbuser
      - POSTGRES_PASSWORD=dbpass
    volumes:
      - postgres-data:/var/lib/postgresql/data
    restart: unless-stopped
    networks:
      - app-network

  nginx:
    image: nginx:alpine
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf
      - ./ssl:/etc/nginx/ssl
    depends_on:
      - web
    restart: unless-stopped
    networks:
      - app-network

volumes:
  postgres-data:

networks:
  app-network:
    driver: bridge
```

#### Docker Security Best Practices
```bash
# Scan image for vulnerabilities
docker scan nginx:latest

# Run container with security options
docker run -d \
  --name secure-app \
  --read-only \
  --tmpfs /tmp \
  --cap-drop ALL \
  --cap-add NET_BIND_SERVICE \
  --security-opt=no-new-privileges \
  --user 1000:1000 \
  nginx:latest

# Limit resources
docker run -d \
  --name limited-app \
  --memory="512m" \
  --cpus="1.0" \
  --pids-limit 100 \
  nginx:latest

# Use secrets for sensitive data
echo "my_secret_password" | docker secret create db_password -
docker service create \
  --name myapp \
  --secret db_password \
  myapp:latest
```

### 2.2 Virtual Machine Management

#### KVM/QEMU Setup
```bash
# Install KVM and tools
sudo apt install -y qemu-kvm libvirt-daemon-system libvirt-clients bridge-utils virt-manager

# Check virtualization support
egrep -c '(vmx|svm)' /proc/cpuinfo
kvm-ok

# Add user to libvirt group
sudo usermod -aG libvirt $USER
sudo usermod -aG kvm $USER

# Start libvirt service
sudo systemctl start libvirtd
sudo systemctl enable libvirtd

# List VMs
virsh list --all

# Create VM from ISO
virt-install \
  --name ubuntu-vm \
  --ram 2048 \
  --disk path=/var/lib/libvirt/images/ubuntu-vm.qcow2,size=20 \
  --vcpus 2 \
  --os-type linux \
  --os-variant ubuntu20.04 \
  --network bridge=virbr0 \
  --graphics none \
  --console pty,target_type=serial \
  --location 'http://archive.ubuntu.com/ubuntu/dists/focal/main/installer-amd64/' \
  --extra-args 'console=ttyS0,115200n8 serial'

# VM management
virsh start vm-name
virsh shutdown vm-name
virsh destroy vm-name
virsh undefine vm-name
virsh autostart vm-name

# Snapshot management
virsh snapshot-create-as vm-name snapshot1 "Description"
virsh snapshot-list vm-name
virsh snapshot-revert vm-name snapshot1
virsh snapshot-delete vm-name snapshot1
```

---

## Module 3: Infrastructure as Code (Day 25)

### 3.1 Ansible Basics

#### Installing Ansible
```bash
# Install Ansible
sudo apt update
sudo apt install -y ansible

# Verify installation
ansible --version

# Create project structure
mkdir -p ansible-project/{inventory,playbooks,roles,group_vars,host_vars}
cd ansible-project
```

#### Inventory File
```ini
# inventory/hosts
[webservers]
web1 ansible_host=192.168.1.10
web2 ansible_host=192.168.1.11

[databases]
db1 ansible_host=192.168.1.20

[all:vars]
ansible_user=ubuntu
ansible_ssh_private_key_file=~/.ssh/id_rsa
ansible_python_interpreter=/usr/bin/python3
```

#### Basic Playbook
```yaml
# playbooks/webserver.yml
---
- name: Configure Web Servers
  hosts: webservers
  become: yes
  
  vars:
    nginx_port: 80
    app_user: www-data
  
  tasks:
    - name: Update apt cache
      apt:
        update_cache: yes
        cache_valid_time: 3600
    
    - name: Install Nginx
      apt:
        name: nginx
        state: present
    
    - name: Start and enable Nginx
      systemd:
        name: nginx
        state: started
        enabled: yes
    
    - name: Copy Nginx configuration
      template:
        src: templates/nginx.conf.j2
        dest: /etc/nginx/sites-available/default
      notify: Reload Nginx
    
    - name: Create web directory
      file:
        path: /var/www/html
        state: directory
        owner: "{{ app_user }}"
        group: "{{ app_user }}"
        mode: '0755'
    
    - name: Deploy website
      copy:
        src: files/index.html
        dest: /var/www/html/index.html
        owner: "{{ app_user }}"
        group: "{{ app_user }}"
        mode: '0644'
  
  handlers:
    - name: Reload Nginx
      systemd:
        name: nginx
        state: reloaded
```

#### Advanced Playbook with Roles
```yaml
# playbooks/full-stack.yml
---
- name: Deploy Full Stack Application
  hosts: all
  become: yes
  
  roles:
    - common
    - firewall
    - { role: nginx, when: "'webservers' in group_names" }
    - { role: postgresql, when: "'databases' in group_names" }
    - { role: application, when: "'webservers' in group_names" }
```

#### Ansible Role Structure
```bash
# Create role
ansible-galaxy init roles/nginx

# Role structure
roles/nginx/
├── defaults/
│   └── main.yml
├── files/
├── handlers/
│   └── main.yml
├── meta/
│   └── main.yml
├── tasks/
│   └── main.yml
├── templates/
│   └── nginx.conf.j2
└── vars/
    └── main.yml
```


### 3.2 Terraform Basics

#### Installing Terraform
```bash
# Install Terraform
wget -O- https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
sudo apt update
sudo apt install terraform

# Verify installation
terraform --version
```

#### Basic Terraform Configuration
```hcl
# main.tf
terraform {
  required_version = ">= 1.0"
  
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 4.0"
    }
  }
}

provider "aws" {
  region = var.aws_region
}

# Variables
variable "aws_region" {
  description = "AWS region"
  type        = string
  default     = "us-east-1"
}

variable "instance_type" {
  description = "EC2 instance type"
  type        = string
  default     = "t2.micro"
}

# Data sources
data "aws_ami" "ubuntu" {
  most_recent = true
  owners      = ["099720109477"] # Canonical

  filter {
    name   = "name"
    values = ["ubuntu/images/hvm-ssd/ubuntu-focal-20.04-amd64-server-*"]
  }
}

# Resources
resource "aws_instance" "web" {
  ami           = data.aws_ami.ubuntu.id
  instance_type = var.instance_type
  
  tags = {
    Name        = "WebServer"
    Environment = "Production"
  }
  
  user_data = <<-EOF
              #!/bin/bash
              apt-get update
              apt-get install -y nginx
              systemctl start nginx
              EOF
}

# Outputs
output "instance_ip" {
  description = "Public IP of the instance"
  value       = aws_instance.web.public_ip
}
```

---

## Module 4: Practical Lab Projects (Day 26)

### 4.1 Lab 1: Secure Web Server Deployment

#### Project Overview
Deploy a production-ready, hardened web server with:
- Nginx web server
- SSL/TLS with Let's Encrypt
- Firewall configuration
- Security hardening
- Monitoring and logging
- Automated backups

#### Implementation Steps
```bash
#!/bin/bash
# deploy-secure-webserver.sh

set -euo pipefail

# Variables
DOMAIN="example.com"
EMAIL="admin@example.com"
WEB_ROOT="/var/www/html"

# Update system
apt update && apt upgrade -y

# Install required packages
apt install -y nginx certbot python3-certbot-nginx ufw fail2ban

# Configure firewall
ufw default deny incoming
ufw default allow outgoing
ufw allow ssh
ufw allow 'Nginx Full'
ufw --force enable

# Configure Nginx
cat > /etc/nginx/sites-available/default << 'EOF'
server {
    listen 80;
    server_name example.com www.example.com;
    root /var/www/html;
    index index.html;
    
    # Security headers
    add_header X-Frame-Options "SAMEORIGIN" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header X-XSS-Protection "1; mode=block" always;
    add_header Referrer-Policy "no-referrer-when-downgrade" always;
    
    # Logging
    access_log /var/log/nginx/access.log;
    error_log /var/log/nginx/error.log;
    
    location / {
        try_files $uri $uri/ =404;
    }
    
    # Deny access to hidden files
    location ~ /\. {
        deny all;
    }
}
EOF

# Test and reload Nginx
nginx -t
systemctl reload nginx

# Obtain SSL certificate
certbot --nginx -d $DOMAIN -d www.$DOMAIN --non-interactive --agree-tos -m $EMAIL

# Configure Fail2ban
cat > /etc/fail2ban/jail.local << 'EOF'
[DEFAULT]
bantime = 3600
findtime = 600
maxretry = 5

[sshd]
enabled = true

[nginx-http-auth]
enabled = true

[nginx-noscript]
enabled = true

[nginx-badbots]
enabled = true
EOF

systemctl restart fail2ban

# Set up automated backups
cat > /usr/local/bin/backup-website.sh << 'EOF'
#!/bin/bash
BACKUP_DIR="/backup"
DATE=$(date +%Y%m%d)
tar -czf $BACKUP_DIR/website-$DATE.tar.gz /var/www/html /etc/nginx
find $BACKUP_DIR -name "website-*.tar.gz" -mtime +7 -delete
EOF

chmod +x /usr/local/bin/backup-website.sh

# Add to crontab
(crontab -l 2>/dev/null; echo "0 2 * * * /usr/local/bin/backup-website.sh") | crontab -

echo "Secure web server deployment complete!"
```

### 4.2 Lab 2: Multi-Tier Application Stack

#### Architecture
```
Load Balancer (Nginx)
    ↓
Application Servers (x2)
    ↓
Database Server (PostgreSQL)
    ↓
Cache Server (Redis)
```

#### Docker Compose Implementation
```yaml
# docker-compose.yml
version: '3.8'

services:
  nginx:
    image: nginx:alpine
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./nginx/nginx.conf:/etc/nginx/nginx.conf:ro
      - ./nginx/ssl:/etc/nginx/ssl:ro
    depends_on:
      - app1
      - app2
    networks:
      - frontend
    restart: unless-stopped

  app1:
    build: ./app
    environment:
      - DATABASE_URL=postgresql://postgres:password@db:5432/myapp
      - REDIS_URL=redis://cache:6379
      - APP_ENV=production
    depends_on:
      - db
      - cache
    networks:
      - frontend
      - backend
    restart: unless-stopped

  app2:
    build: ./app
    environment:
      - DATABASE_URL=postgresql://postgres:password@db:5432/myapp
      - REDIS_URL=redis://cache:6379
      - APP_ENV=production
    depends_on:
      - db
      - cache
    networks:
      - frontend
      - backend
    restart: unless-stopped

  db:
    image: postgres:13
    environment:
      - POSTGRES_DB=myapp
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=password
    volumes:
      - postgres-data:/var/lib/postgresql/data
      - ./db/init.sql:/docker-entrypoint-initdb.d/init.sql
    networks:
      - backend
    restart: unless-stopped

  cache:
    image: redis:alpine
    command: redis-server --appendonly yes
    volumes:
      - redis-data:/data
    networks:
      - backend
    restart: unless-stopped

  monitoring:
    image: prom/prometheus
    ports:
      - "9090:9090"
    volumes:
      - ./prometheus/prometheus.yml:/etc/prometheus/prometheus.yml
      - prometheus-data:/prometheus
    networks:
      - backend
    restart: unless-stopped

volumes:
  postgres-data:
  redis-data:
  prometheus-data:

networks:
  frontend:
    driver: bridge
  backend:
    driver: bridge
```

### 4.3 Lab 3: Security Monitoring Dashboard

#### ELK Stack Setup
```yaml
# elk-stack/docker-compose.yml
version: '3.8'

services:
  elasticsearch:
    image: docker.elastic.co/elasticsearch/elasticsearch:7.15.0
    environment:
      - discovery.type=single-node
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    volumes:
      - elasticsearch-data:/usr/share/elasticsearch/data
    ports:
      - "9200:9200"
    networks:
      - elk

  logstash:
    image: docker.elastic.co/logstash/logstash:7.15.0
    volumes:
      - ./logstash/pipeline:/usr/share/logstash/pipeline
      - ./logstash/config/logstash.yml:/usr/share/logstash/config/logstash.yml
    ports:
      - "5044:5044"
      - "9600:9600"
    depends_on:
      - elasticsearch
    networks:
      - elk

  kibana:
    image: docker.elastic.co/kibana/kibana:7.15.0
    ports:
      - "5601:5601"
    environment:
      - ELASTICSEARCH_HOSTS=http://elasticsearch:9200
    depends_on:
      - elasticsearch
    networks:
      - elk

  filebeat:
    image: docker.elastic.co/beats/filebeat:7.15.0
    user: root
    volumes:
      - ./filebeat/filebeat.yml:/usr/share/filebeat/filebeat.yml:ro
      - /var/log:/var/log:ro
      - /var/lib/docker/containers:/var/lib/docker/containers:ro
    depends_on:
      - elasticsearch
      - logstash
    networks:
      - elk

volumes:
  elasticsearch-data:

networks:
  elk:
    driver: bridge
```

---

## Module 5: Final Project and Assessment (Day 27)

### 5.1 Capstone Project: Enterprise Infrastructure

#### Project Requirements
Build a complete enterprise infrastructure with:

1. **Network Architecture**
   - DMZ with web servers
   - Internal application servers
   - Database tier
   - Management network

2. **Security Implementation**
   - Firewall rules and network segmentation
   - IDS/IPS (Snort/Suricata)
   - Centralized logging (ELK Stack)
   - Security monitoring and alerting

3. **High Availability**
   - Load balancing
   - Database replication
   - Automated failover
   - Backup and disaster recovery

4. **Automation**
   - Infrastructure as Code (Terraform/Ansible)
   - CI/CD pipeline
   - Automated security scanning
   - Configuration management

5. **Monitoring and Alerting**
   - System metrics (Prometheus/Grafana)
   - Application performance monitoring
   - Log aggregation and analysis
   - Security event monitoring


#### Implementation Checklist
```markdown
## Phase 1: Infrastructure Setup
- [ ] Design network architecture
- [ ] Set up virtual machines/containers
- [ ] Configure network segmentation
- [ ] Implement firewall rules
- [ ] Set up VPN access

## Phase 2: Application Deployment
- [ ] Deploy web servers (Nginx/Apache)
- [ ] Deploy application servers
- [ ] Set up database cluster
- [ ] Configure load balancer
- [ ] Implement SSL/TLS

## Phase 3: Security Hardening
- [ ] Harden all systems (CIS Benchmarks)
- [ ] Configure SELinux/AppArmor
- [ ] Implement intrusion detection
- [ ] Set up Fail2ban
- [ ] Configure audit rules

## Phase 4: Monitoring and Logging
- [ ] Deploy ELK Stack
- [ ] Configure Prometheus/Grafana
- [ ] Set up centralized logging
- [ ] Create monitoring dashboards
- [ ] Configure alerting

## Phase 5: Automation
- [ ] Write Ansible playbooks
- [ ] Create Terraform configurations
- [ ] Set up CI/CD pipeline
- [ ] Automate backups
- [ ] Implement auto-scaling

## Phase 6: Testing and Documentation
- [ ] Perform security testing
- [ ] Conduct penetration testing
- [ ] Load testing
- [ ] Disaster recovery testing
- [ ] Complete documentation
```

### 5.2 Assessment Scenarios

#### Scenario 1: Security Incident Response
```
Situation: Your web server has been compromised. Suspicious processes 
are running, and there's unusual network traffic.

Tasks:
1. Identify the compromised system
2. Isolate the affected server
3. Analyze logs to determine attack vector
4. Identify malicious processes and files
5. Remove malware and backdoors
6. Patch vulnerabilities
7. Restore from clean backup
8. Document the incident
9. Implement preventive measures
```

#### Scenario 2: Performance Troubleshooting
```
Situation: Application response time has degraded significantly. 
Users are complaining about slow page loads.

Tasks:
1. Identify the bottleneck (CPU, memory, disk, network)
2. Analyze application logs
3. Check database performance
4. Review system metrics
5. Identify resource-intensive processes
6. Optimize configuration
7. Implement caching if needed
8. Document findings and solutions
```

#### Scenario 3: Disaster Recovery
```
Situation: Primary database server has failed. You need to restore 
service with minimal downtime.

Tasks:
1. Assess the situation
2. Activate disaster recovery plan
3. Promote standby database to primary
4. Update application configuration
5. Verify data integrity
6. Test application functionality
7. Monitor for issues
8. Document the recovery process
```

---

## Practical Exercises

### Exercise 1: Advanced Scripting (Day 23)
1. Create a comprehensive system monitoring script
2. Build an automated backup solution with rotation
3. Develop a security audit script
4. Implement log analysis and alerting
5. Create a deployment automation script

### Exercise 2: Containerization (Day 24)
1. Containerize a multi-tier application
2. Create Docker Compose stack
3. Implement container security best practices
4. Set up container monitoring
5. Deploy to container orchestration platform

### Exercise 3: Infrastructure as Code (Day 25)
1. Write Ansible playbooks for full stack deployment
2. Create Terraform configurations for cloud infrastructure
3. Implement configuration management
4. Set up automated provisioning
5. Create infrastructure testing procedures

### Exercise 4: Lab Projects (Day 26)
1. Deploy secure web server with all hardening
2. Build multi-tier application stack
3. Implement security monitoring dashboard
4. Set up high availability configuration
5. Create disaster recovery procedures

### Exercise 5: Final Project (Day 27)
1. Design complete enterprise infrastructure
2. Implement all security controls
3. Deploy monitoring and logging
4. Automate everything possible
5. Document and present solution

---

## Troubleshooting Guide

### Docker Issues
```bash
# Container won't start
docker logs container_name
docker inspect container_name

# Network issues
docker network ls
docker network inspect network_name

# Permission issues
docker exec -it container_name ls -la /path

# Clean up
docker system prune -a
docker volume prune
```

### Ansible Issues
```bash
# Test connectivity
ansible all -m ping -i inventory/hosts

# Verbose output
ansible-playbook playbook.yml -vvv

# Syntax check
ansible-playbook playbook.yml --syntax-check

# Dry run
ansible-playbook playbook.yml --check

# Debug variables
ansible-playbook playbook.yml -e "debug=true"
```

### Performance Issues
```bash
# Identify bottleneck
top
htop
iotop
iftop

# Analyze logs
journalctl -xe
tail -f /var/log/syslog

# Check resources
free -h
df -h
iostat -x 2

# Network analysis
ss -s
netstat -s
tcpdump -i any -n
```

---

## Best Practices

### Scripting
1. Use strict mode (set -euo pipefail)
2. Implement proper error handling
3. Add comprehensive logging
4. Use functions for reusability
5. Document code thoroughly
6. Test in isolated environment
7. Version control everything
8. Follow naming conventions

### Containerization
1. Use official base images
2. Minimize image layers
3. Don't run as root
4. Use multi-stage builds
5. Scan for vulnerabilities
6. Implement health checks
7. Use secrets management
8. Monitor resource usage

### Infrastructure as Code
1. Use version control
2. Implement code review
3. Test before deployment
4. Use modules/roles
5. Document everything
6. Implement state management
7. Use variables for flexibility
8. Follow DRY principle

### Security
1. Defense in depth
2. Principle of least privilege
3. Regular security audits
4. Automated vulnerability scanning
5. Incident response plan
6. Regular backups
7. Encryption everywhere
8. Continuous monitoring

---

## Additional Resources

### Documentation
- [Docker Documentation](https://docs.docker.com/)
- [Ansible Documentation](https://docs.ansible.com/)
- [Terraform Documentation](https://www.terraform.io/docs)
- [Kubernetes Documentation](https://kubernetes.io/docs/)

### Tools
- [Docker Hub](https://hub.docker.com/)
- [Ansible Galaxy](https://galaxy.ansible.com/)
- [Terraform Registry](https://registry.terraform.io/)
- [GitHub Actions](https://github.com/features/actions)

### Books
- "Docker Deep Dive" by Nigel Poulton
- "Ansible for DevOps" by Jeff Geerling
- "Terraform: Up & Running" by Yevgeniy Brikman
- "Site Reliability Engineering" by Google

### Online Platforms
- [Katacoda](https://www.katacoda.com/)
- [Play with Docker](https://labs.play-with-docker.com/)
- [Terraform Cloud](https://app.terraform.io/)

---

## Summary and Next Steps

After completing this Advanced Topics module, you should be proficient in:

✓ Advanced bash scripting and automation
✓ Containerization with Docker
✓ Infrastructure as Code with Ansible and Terraform
✓ Building and deploying multi-tier applications
✓ Implementing comprehensive security monitoring
✓ Troubleshooting complex infrastructure issues
✓ Designing enterprise-grade solutions
✓ Automating security and operations tasks

**Certification Path**: Consider pursuing:
- Docker Certified Associate (DCA)
- Certified Kubernetes Administrator (CKA)
- Red Hat Certified Engineer (RHCE)
- AWS Certified Solutions Architect
- Certified Information Systems Security Professional (CISSP)

**Career Paths**:
- DevOps Engineer
- Site Reliability Engineer (SRE)
- Cloud Security Engineer
- Infrastructure Architect
- Security Operations Engineer

**Next Steps**:
1. Build your portfolio with real projects
2. Contribute to open-source projects
3. Pursue relevant certifications
4. Stay updated with industry trends
5. Join professional communities
6. Attend conferences and workshops
7. Mentor others in the community

Congratulations on completing the Linux and OS Security learning path! You now have the skills to design, deploy, and secure enterprise Linux infrastructure. Keep practicing, stay curious, and continue learning!
