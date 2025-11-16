# Day 23-27: Advanced Topics & Practical Labs - Practice Questions & Answers

## Section 1: Advanced Bash Scripting (10 Questions)

### Question 1
What does `set -euo pipefail` do in a bash script?

**Answer:**
- `set -e`: Exit immediately if a command exits with non-zero status
- `set -u`: Treat unset variables as an error
- `set -o pipefail`: Return exit status of last command in pipe that failed

```bash
#!/bin/bash
set -euo pipefail
```

---

### Question 2
How do you create an associative array in bash?

**Answer:**
```bash
declare -A myarray
myarray[key1]="value1"
myarray[key2]="value2"

# Access values
echo "${myarray[key1]}"

# Iterate
for key in "${!myarray[@]}"; do
    echo "$key: ${myarray[$key]}"
done
```

---

### Question 3
What is the purpose of a trap command in bash?

**Answer:**
Trap allows you to catch signals and execute cleanup code before script exits.

```bash
cleanup() {
    echo "Cleaning up..."
    rm -f /tmp/lockfile
}

trap cleanup EXIT
trap 'echo "Interrupted"; exit 1' INT TERM
```

---

### Question 4
How do you parse command-line arguments with long options?

**Answer:**
```bash
while [[ $# -gt 0 ]]; do
    case $1 in
        -h|--help)
            show_help
            exit 0
            ;;
        -f|--file)
            FILE="$2"
            shift 2
            ;;
        -v|--verbose)
            VERBOSE=true
            shift
            ;;
        *)
            echo "Unknown option: $1"
            exit 1
            ;;
    esac
done
```

---

### Question 5
How do you implement logging in a bash script?

**Answer:**
```bash
LOG_FILE="/var/log/script.log"

log() {
    local level="$1"
    shift
    echo "[$(date +'%Y-%m-%d %H:%M:%S')] [$level] $*" | tee -a "$LOG_FILE"
}

log "INFO" "Script started"
log "ERROR" "Something went wrong"
```

---

### Question 6
What is the difference between `$@` and `$*`?

**Answer:**
- `$@`: Expands to separate words ("$1" "$2" "$3")
- `$*`: Expands to single word ("$1 $2 $3")

```bash
# $@ preserves arguments with spaces
for arg in "$@"; do
    echo "$arg"
done
```

---

### Question 7
How do you check if a command exists in a script?

**Answer:**
```bash
if command -v docker &> /dev/null; then
    echo "Docker is installed"
else
    echo "Docker is not installed"
fi

# Alternative
if type docker &> /dev/null; then
    echo "Docker is installed"
fi
```

---

### Question 8
How do you create a lock file to prevent concurrent script execution?

**Answer:**
```bash
LOCK_FILE="/var/run/script.lock"

if [ -f "$LOCK_FILE" ]; then
    echo "Script is already running"
    exit 1
fi

touch "$LOCK_FILE"
trap "rm -f $LOCK_FILE" EXIT
```

---

### Question 9
How do you read a configuration file in bash?

**Answer:**
```bash
# config.conf
# KEY1=value1
# KEY2=value2

if [ -f "config.conf" ]; then
    source config.conf
    echo "$KEY1"
    echo "$KEY2"
fi

# Or with validation
while IFS='=' read -r key value; do
    [[ $key =~ ^#.*$ ]] && continue
    declare "$key=$value"
done < config.conf
```

---

### Question 10
How do you implement parallel execution in bash?

**Answer:**
```bash
# Run tasks in parallel
task1 &
task2 &
task3 &

# Wait for all to complete
wait

# Or with array
pids=()
for task in task1 task2 task3; do
    $task &
    pids+=($!)
done

for pid in "${pids[@]}"; do
    wait $pid
done
```

---

## Section 2: Docker and Containerization (10 Questions)

### Question 11
How do you build a Docker image from a Dockerfile?

**Answer:**
```bash
docker build -t myapp:1.0 .

# With build arguments
docker build --build-arg VERSION=1.0 -t myapp:1.0 .

# No cache
docker build --no-cache -t myapp:1.0 .
```

---

### Question 12
What command runs a Docker container in detached mode with port mapping?

**Answer:**
```bash
docker run -d -p 8080:80 --name mycontainer nginx

# With environment variables
docker run -d -p 8080:80 -e ENV=production --name mycontainer nginx

# With volume mount
docker run -d -p 8080:80 -v /host/path:/container/path nginx
```

---

### Question 13
How do you view logs from a Docker container?

**Answer:**
```bash
docker logs container_name

# Follow logs
docker logs -f container_name

# Last 100 lines
docker logs --tail 100 container_name

# With timestamps
docker logs -t container_name
```

---

### Question 14
What command executes a command inside a running container?

**Answer:**
```bash
docker exec -it container_name bash

# Run specific command
docker exec container_name ls -la /app

# As specific user
docker exec -u 1000 container_name whoami
```

---

### Question 15
How do you create a Docker volume?

**Answer:**
```bash
docker volume create myvolume

# List volumes
docker volume ls

# Inspect volume
docker volume inspect myvolume

# Use volume
docker run -v myvolume:/data nginx

# Remove volume
docker volume rm myvolume
```

---

### Question 16
What is a multi-stage Docker build?

**Answer:**
Multi-stage builds allow you to use multiple FROM statements to create smaller final images.

```dockerfile
# Build stage
FROM node:14 AS builder
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

# Production stage
FROM node:14-alpine
WORKDIR /app
COPY --from=builder /app/dist ./dist
COPY package*.json ./
RUN npm install --production
CMD ["node", "dist/index.js"]
```

---

### Question 17
How do you start services defined in docker-compose.yml?

**Answer:**
```bash
docker-compose up -d

# Build and start
docker-compose up -d --build

# Scale service
docker-compose up -d --scale web=3

# View logs
docker-compose logs -f

# Stop services
docker-compose down
```

---

### Question 18
How do you limit resources for a Docker container?

**Answer:**
```bash
docker run -d \
  --memory="512m" \
  --cpus="1.0" \
  --pids-limit 100 \
  nginx

# Check resource usage
docker stats container_name
```

---

### Question 19
What command removes all stopped containers?

**Answer:**
```bash
docker container prune

# Remove all containers (including running)
docker rm -f $(docker ps -aq)

# Clean up everything
docker system prune -a
```

---

### Question 20
How do you create a Docker network?

**Answer:**
```bash
docker network create mynetwork

# Create with specific driver
docker network create --driver bridge mynetwork

# Connect container to network
docker network connect mynetwork container_name

# List networks
docker network ls

# Inspect network
docker network inspect mynetwork
```

---

## Section 3: Infrastructure as Code (10 Questions)

### Question 21
How do you run an Ansible playbook?

**Answer:**
```bash
ansible-playbook playbook.yml

# With inventory
ansible-playbook -i inventory/hosts playbook.yml

# Check mode (dry run)
ansible-playbook playbook.yml --check

# Verbose output
ansible-playbook playbook.yml -vvv

# Limit to specific hosts
ansible-playbook playbook.yml --limit webservers
```

---

### Question 22
What is an Ansible role and how do you create one?

**Answer:**
An Ansible role is a way to organize playbooks and reusable content.

```bash
# Create role structure
ansible-galaxy init roles/nginx

# Role structure:
roles/nginx/
├── defaults/      # Default variables
├── files/         # Static files
├── handlers/      # Handlers
├── tasks/         # Main tasks
├── templates/     # Jinja2 templates
└── vars/          # Variables
```

---

### Question 23
How do you test Ansible connectivity to hosts?

**Answer:**
```bash
ansible all -m ping -i inventory/hosts

# Test specific group
ansible webservers -m ping

# Test with sudo
ansible all -m ping --become

# Gather facts
ansible all -m setup
```

---

### Question 24
What is Terraform and what command initializes a Terraform project?

**Answer:**
Terraform is an Infrastructure as Code tool for building, changing, and versioning infrastructure.

```bash
# Initialize Terraform
terraform init

# Validate configuration
terraform validate

# Plan changes
terraform plan

# Apply changes
terraform apply

# Destroy infrastructure
terraform destroy
```

---

### Question 25
How do you define a variable in Terraform?

**Answer:**
```hcl
# variables.tf
variable "instance_type" {
  description = "EC2 instance type"
  type        = string
  default     = "t2.micro"
}

variable "instance_count" {
  description = "Number of instances"
  type        = number
  default     = 1
}

# Use variable
resource "aws_instance" "web" {
  instance_type = var.instance_type
  count         = var.instance_count
}
```

---

### Question 26
How do you use Ansible templates?

**Answer:**
```yaml
# In playbook
- name: Deploy configuration
  template:
    src: templates/nginx.conf.j2
    dest: /etc/nginx/nginx.conf
  notify: Reload Nginx

# Template file (nginx.conf.j2)
server {
    listen {{ nginx_port }};
    server_name {{ server_name }};
    root {{ web_root }};
}
```

---

### Question 27
What is Terraform state and where is it stored?

**Answer:**
Terraform state tracks the current state of infrastructure.

```bash
# Local state (default)
terraform.tfstate

# Remote state (recommended)
terraform {
  backend "s3" {
    bucket = "my-terraform-state"
    key    = "prod/terraform.tfstate"
    region = "us-east-1"
  }
}

# View state
terraform show

# List resources
terraform state list
```

---

### Question 28
How do you use Ansible vault for secrets?

**Answer:**
```bash
# Create encrypted file
ansible-vault create secrets.yml

# Edit encrypted file
ansible-vault edit secrets.yml

# Encrypt existing file
ansible-vault encrypt vars.yml

# Decrypt file
ansible-vault decrypt vars.yml

# Run playbook with vault
ansible-playbook playbook.yml --ask-vault-pass

# Use vault password file
ansible-playbook playbook.yml --vault-password-file ~/.vault_pass
```

---

### Question 29
How do you output values in Terraform?

**Answer:**
```hcl
# outputs.tf
output "instance_ip" {
  description = "Public IP of instance"
  value       = aws_instance.web.public_ip
}

output "instance_id" {
  description = "Instance ID"
  value       = aws_instance.web.id
}

# View outputs
terraform output

# Specific output
terraform output instance_ip
```

---

### Question 30
How do you use Ansible handlers?

**Answer:**
```yaml
# In tasks
- name: Update Nginx config
  copy:
    src: nginx.conf
    dest: /etc/nginx/nginx.conf
  notify: Reload Nginx

# In handlers/main.yml
- name: Reload Nginx
  systemd:
    name: nginx
    state: reloaded
```

---

## Section 4: Monitoring and Logging (10 Questions)

### Question 31
How do you check Docker container resource usage?

**Answer:**
```bash
docker stats

# Specific container
docker stats container_name

# No streaming
docker stats --no-stream

# Format output
docker stats --format "table {{.Container}}\t{{.CPUPerc}}\t{{.MemUsage}}"
```

---

### Question 32
What command shows system resource usage in real-time?

**Answer:**
```bash
# CPU and memory
top
htop

# Disk I/O
iotop

# Network
iftop -i eth0

# All together
glances
```

---

### Question 33
How do you configure log rotation for a custom application?

**Answer:**
```bash
# Create /etc/logrotate.d/myapp
/var/log/myapp/*.log {
    daily
    rotate 7
    compress
    delaycompress
    missingok
    notifempty
    create 0640 appuser appgroup
    sharedscripts
    postrotate
        systemctl reload myapp > /dev/null 2>&1 || true
    endscript
}

# Test
sudo logrotate -d /etc/logrotate.d/myapp
```

---

### Question 34
How do you search logs with journalctl?

**Answer:**
```bash
# View all logs
journalctl

# Follow logs
journalctl -f

# Specific service
journalctl -u nginx

# Since time
journalctl --since "1 hour ago"
journalctl --since "2024-01-01"

# Priority level
journalctl -p err

# Kernel messages
journalctl -k
```

---

### Question 35
What command shows network connections and listening ports?

**Answer:**
```bash
# Modern command
ss -tulnp

# Legacy command
netstat -tulnp

# Show all connections
ss -a

# Show statistics
ss -s
```

---

### Question 36
How do you monitor file changes in real-time?

**Answer:**
```bash
# Install inotify-tools
sudo apt install inotify-tools

# Watch directory
inotifywait -m -r -e modify,create,delete /etc/

# Watch specific file
inotifywait -m /etc/passwd

# With script action
inotifywait -m /var/www/html -e create -e modify |
while read path action file; do
    echo "File $file was $action"
done
```

---

### Question 37
How do you analyze disk I/O performance?

**Answer:**
```bash
# I/O statistics
iostat -x 2

# Per-process I/O
sudo iotop

# Disk usage
df -h

# Inode usage
df -i

# Find large files
du -h / | sort -rh | head -20
```

---

### Question 38
What command shows process tree?

**Answer:**
```bash
pstree

# With PIDs
pstree -p

# For specific user
pstree username

# With command line arguments
pstree -a

# Highlight specific process
pstree -H PID
```

---

### Question 39
How do you send logs to a remote syslog server?

**Answer:**
```bash
# Edit /etc/rsyslog.conf
*.* @192.168.1.100:514    # UDP
*.* @@192.168.1.100:514   # TCP

# Restart rsyslog
sudo systemctl restart rsyslog

# Test
logger -t test "Test message"
```

---

### Question 40
How do you check system uptime and load average?

**Answer:**
```bash
uptime

# Output: 10:30:15 up 5 days, 2:15, 3 users, load average: 0.50, 0.75, 0.80
# Load averages: 1 min, 5 min, 15 min

# Detailed system info
w

# Boot time
who -b
```

---

## Section 5: Practical Scenarios (10 Questions)

### Question 41
How do you troubleshoot a container that keeps restarting?

**Answer:**
```bash
# Check container status
docker ps -a

# View logs
docker logs container_name

# Inspect container
docker inspect container_name

# Check events
docker events --filter container=container_name

# Run with different command
docker run -it image_name /bin/bash

# Check resource limits
docker stats container_name
```

---

### Question 42
How do you backup and restore a Docker volume?

**Answer:**
```bash
# Backup volume
docker run --rm \
  -v myvolume:/data \
  -v $(pwd):/backup \
  alpine tar czf /backup/volume-backup.tar.gz /data

# Restore volume
docker run --rm \
  -v myvolume:/data \
  -v $(pwd):/backup \
  alpine tar xzf /backup/volume-backup.tar.gz -C /
```

---

### Question 43
How do you deploy a web application with Ansible?

**Answer:**
```yaml
---
- name: Deploy Web Application
  hosts: webservers
  become: yes
  
  tasks:
    - name: Install Nginx
      apt:
        name: nginx
        state: present
    
    - name: Copy application files
      copy:
        src: app/
        dest: /var/www/html/
    
    - name: Start Nginx
      systemd:
        name: nginx
        state: started
        enabled: yes
```

---

### Question 44
How do you implement health checks in Docker?

**Answer:**
```dockerfile
# In Dockerfile
HEALTHCHECK --interval=30s --timeout=3s --retries=3 \
  CMD curl -f http://localhost/ || exit 1

# In docker-compose.yml
services:
  web:
    image: nginx
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost"]
      interval: 30s
      timeout: 3s
      retries: 3
```

---

### Question 45
How do you create a systemd service for a custom application?

**Answer:**
```bash
# Create /etc/systemd/system/myapp.service
[Unit]
Description=My Application
After=network.target

[Service]
Type=simple
User=appuser
WorkingDirectory=/opt/myapp
ExecStart=/usr/bin/python3 /opt/myapp/app.py
Restart=always
RestartSec=10

[Install]
WantedBy=multi-user.target

# Reload and start
sudo systemctl daemon-reload
sudo systemctl enable myapp
sudo systemctl start myapp
```

---

### Question 46
How do you implement blue-green deployment with Docker?

**Answer:**
```bash
# Deploy green version
docker run -d --name app-green -p 8081:80 myapp:v2

# Test green version
curl http://localhost:8081

# Switch traffic (update load balancer)
# Stop blue version
docker stop app-blue

# Rename green to blue
docker rename app-green app-blue

# Update port mapping
docker update --publish 8080:80 app-blue
```

---

### Question 47
How do you secure a Docker container?

**Answer:**
```bash
docker run -d \
  --name secure-app \
  --read-only \
  --tmpfs /tmp \
  --cap-drop ALL \
  --cap-add NET_BIND_SERVICE \
  --security-opt=no-new-privileges \
  --user 1000:1000 \
  --memory="512m" \
  --cpus="1.0" \
  myapp:latest
```

---

### Question 48
How do you implement automated backups with a script?

**Answer:**
```bash
#!/bin/bash
BACKUP_DIR="/backup"
DATE=$(date +%Y%m%d)
RETENTION=7

# Create backup
tar -czf $BACKUP_DIR/backup-$DATE.tar.gz /data

# Rotate old backups
find $BACKUP_DIR -name "backup-*.tar.gz" -mtime +$RETENTION -delete

# Upload to remote (optional)
rsync -avz $BACKUP_DIR/backup-$DATE.tar.gz user@remote:/backups/

# Send notification
echo "Backup completed: backup-$DATE.tar.gz" | mail -s "Backup Report" admin@example.com
```

---

### Question 49
How do you troubleshoot high CPU usage?

**Answer:**
```bash
# Identify process
top
# Press 'P' to sort by CPU

# Detailed process info
ps aux --sort=-%cpu | head -10

# Process tree
pstree -p PID

# System calls
strace -p PID

# Profile application
perf top

# Check for runaway processes
ps aux | awk '$3 > 50'
```

---

### Question 50
How do you implement a complete CI/CD pipeline?

**Answer:**
```yaml
# .github/workflows/deploy.yml
name: CI/CD Pipeline

on:
  push:
    branches: [ main ]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      
      - name: Build Docker image
        run: docker build -t myapp:${{ github.sha }} .
      
      - name: Run tests
        run: docker run myapp:${{ github.sha }} npm test
      
      - name: Security scan
        run: docker scan myapp:${{ github.sha }}
      
      - name: Push to registry
        run: |
          docker login -u ${{ secrets.DOCKER_USER }} -p ${{ secrets.DOCKER_PASS }}
          docker push myapp:${{ github.sha }}
      
      - name: Deploy to production
        run: |
          ansible-playbook -i inventory/prod deploy.yml \
            -e "image_tag=${{ github.sha }}"
```

---

## Summary

**Total Questions: 50**

- Section 1: Advanced Bash Scripting (10 questions)
- Section 2: Docker and Containerization (10 questions)
- Section 3: Infrastructure as Code (10 questions)
- Section 4: Monitoring and Logging (10 questions)
- Section 5: Practical Scenarios (10 questions)

**Practice Tips:**
1. Set up a complete lab environment
2. Practice each technology hands-on
3. Build real projects, not just examples
4. Document your solutions
5. Automate everything possible
6. Test in isolated environments first
7. Learn from failures
8. Join communities and share knowledge

**Scoring Guide:**
- 45-50 correct: Expert! Ready for professional work
- 38-44 correct: Very Good! Minor practice needed
- 30-37 correct: Good! More hands-on experience needed
- 22-29 correct: Fair. Review and practice more
- Below 22: Review all modules and retry

**Study Schedule:**
- **Day 23**: Advanced Bash Scripting (Questions 1-10)
- **Day 24**: Docker and Containerization (Questions 11-20)
- **Day 25**: Infrastructure as Code (Questions 21-30)
- **Day 26**: Monitoring and Logging (Questions 31-40)
- **Day 27**: Practical Scenarios (Questions 41-50)

**Next Steps:**
- Build a complete portfolio project
- Contribute to open-source projects
- Pursue relevant certifications
- Apply for DevOps/SRE positions
- Continue learning new technologies
- Mentor others in the community

**Certification Preparation:**
These questions align with:
- Docker Certified Associate (DCA)
- Certified Kubernetes Administrator (CKA)
- Red Hat Certified Engineer (RHCE)
- AWS Certified DevOps Engineer
- HashiCorp Certified: Terraform Associate

**Final Project Ideas:**
1. Complete CI/CD pipeline with automated testing
2. Multi-tier application with monitoring
3. Infrastructure as Code for cloud deployment
4. Security monitoring and alerting system
5. Automated disaster recovery solution
6. Container orchestration platform
7. Configuration management system
8. Log aggregation and analysis platform

Congratulations on completing the entire Linux and OS Security learning path! You now have comprehensive skills in Linux administration, security, automation, and modern DevOps practices. Keep building, keep learning, and good luck in your career!
