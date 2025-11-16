# Day 18-22: Security Hardening - Complete Guide

## Overview
Security hardening is the process of securing a system by reducing its attack surface, implementing security controls, and following best practices. This comprehensive 5-day module covers user security, file system hardening, system auditing, security tools, and compliance frameworks. You'll learn to transform a default Linux installation into a hardened, production-ready secure system.

**Duration**: 5 Days (Days 18-22)
**Prerequisites**: Linux Fundamentals, System Administration, Networking and Services
**Difficulty**: Advanced

---

## Module 1: User Security and Authentication (Day 18)

### 1.1 User Account Security

#### Understanding User Security Principles
```
Principle of Least Privilege: Users should have minimum permissions needed
Defense in Depth: Multiple layers of security controls
Separation of Duties: Critical tasks require multiple users
Account Lifecycle Management: Proper creation, maintenance, and deletion
```

#### Reviewing User Accounts
```bash
# List all users
cat /etc/passwd

# List users with login shells
grep -v '/nologin\|/false' /etc/passwd

# Find users with UID 0 (root privileges)
awk -F: '($3 == 0) {print}' /etc/passwd

# List all groups
cat /etc/group

# Show user's groups
groups username
id username

# List users in sudo group
getent group sudo

# Find users without passwords
sudo awk -F: '($2 == "") {print}' /etc/shadow

# List locked accounts
sudo passwd -S -a | grep " L "

# Find accounts with no password aging
sudo awk -F: '($5 == "") {print $1}' /etc/shadow
```

#### Securing User Accounts
```bash
# Lock user account
sudo usermod -L username
sudo passwd -l username

# Unlock user account
sudo usermod -U username
sudo passwd -u username

# Set account expiration date
sudo usermod -e 2024-12-31 username

# Remove account expiration
sudo usermod -e "" username

# Disable account (set shell to nologin)
sudo usermod -s /sbin/nologin username

# Set password expiration
sudo chage -M 90 username

# Force password change on next login
sudo chage -d 0 username

# View password aging information
sudo chage -l username

# Set minimum password age
sudo chage -m 7 username

# Set warning before password expiration
sudo chage -W 14 username

# Set account inactivity period
sudo chage -I 30 username
```

### 1.2 Password Policies

#### /etc/login.defs Configuration
```bash
# Edit login definitions
sudo nano /etc/login.defs

# Key settings:
PASS_MAX_DAYS   90      # Maximum password age
PASS_MIN_DAYS   7       # Minimum password age
PASS_MIN_LEN    12      # Minimum password length
PASS_WARN_AGE   14      # Password expiration warning
LOGIN_RETRIES   3       # Failed login attempts
LOGIN_TIMEOUT   60      # Login timeout in seconds
UMASK           027     # Default file creation mask
```


#### PAM (Pluggable Authentication Modules)

**Understanding PAM**
```
PAM provides flexible authentication for Linux systems
Located in: /etc/pam.d/
Common modules:
- pam_unix.so: Traditional password authentication
- pam_pwquality.so: Password quality checking
- pam_faillock.so: Account lockout after failed attempts
- pam_limits.so: Resource limits
- pam_tally2.so: Login counter (deprecated, use faillock)
```

**Password Quality Requirements**
```bash
# Install password quality library
sudo apt install libpam-pwquality

# Configure password requirements
sudo nano /etc/security/pwquality.conf

# Recommended settings:
minlen = 12              # Minimum length
dcredit = -1             # At least 1 digit
ucredit = -1             # At least 1 uppercase
lcredit = -1             # At least 1 lowercase
ocredit = -1             # At least 1 special character
minclass = 4             # Minimum character classes
maxrepeat = 3            # Maximum repeated characters
maxsequence = 3          # Maximum sequential characters
gecoscheck = 1           # Check against GECOS field
dictcheck = 1            # Check against dictionary
usercheck = 1            # Check if contains username
enforcing = 1            # Enforce for root too
retry = 3                # Number of retries
difok = 5                # Characters different from old password
```

**Account Lockout Policy**
```bash
# Configure faillock
sudo nano /etc/security/faillock.conf

# Settings:
deny = 5                 # Lock after 5 failed attempts
unlock_time = 900        # Unlock after 15 minutes
fail_interval = 900      # Count failures in 15 min window
audit                    # Enable audit logging
silent                   # Don't tell user about lockout

# Edit PAM configuration
sudo nano /etc/pam.d/common-auth

# Add before pam_unix.so:
auth required pam_faillock.so preauth
auth [default=die] pam_faillock.so authfail
auth sufficient pam_unix.so nullok
auth required pam_faillock.so authsucc

# View failed login attempts
sudo faillock --user username

# Reset failed attempts
sudo faillock --user username --reset
```

### 1.3 Sudo Configuration

#### Understanding sudo
```bash
# Sudo configuration file
/etc/sudoers

# Additional configurations
/etc/sudoers.d/

# NEVER edit directly, always use:
sudo visudo

# Test sudo configuration
sudo visudo -c
```

#### Basic sudo Rules
```bash
# Edit sudoers
sudo visudo

# User privilege specification
root    ALL=(ALL:ALL) ALL
username ALL=(ALL:ALL) ALL

# Allow user to run specific commands
username ALL=(ALL) /usr/bin/systemctl, /usr/bin/apt

# Allow without password (use carefully!)
username ALL=(ALL) NOPASSWD: /usr/bin/systemctl restart nginx

# Group privileges
%sudo   ALL=(ALL:ALL) ALL
%admin  ALL=(ALL) ALL

# Allow user to run commands as specific user
username ALL=(webuser) /usr/bin/php

# Command aliases
Cmnd_Alias NETWORKING = /sbin/route, /sbin/ifconfig, /bin/ping
Cmnd_Alias SOFTWARE = /bin/rpm, /usr/bin/yum, /usr/bin/apt
Cmnd_Alias SERVICES = /usr/bin/systemctl

# User aliases
User_Alias ADMINS = user1, user2, user3

# Apply aliases
ADMINS ALL=(ALL) NETWORKING, SOFTWARE, SERVICES
```


#### Advanced sudo Configuration
```bash
# Sudo security options
Defaults env_reset                    # Reset environment
Defaults mail_badpass                 # Email on bad password
Defaults secure_path="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
Defaults logfile="/var/log/sudo.log"  # Sudo log file
Defaults log_input, log_output        # Log commands and output
Defaults use_pty                      # Use pseudo-terminal
Defaults requiretty                   # Require TTY
Defaults timestamp_timeout=15         # Sudo timeout (minutes)
Defaults passwd_tries=3               # Password attempts
Defaults insults                      # Fun insults on wrong password

# Per-user settings
Defaults:username !requiretty

# Restrict commands with arguments
username ALL=(ALL) /usr/bin/systemctl restart nginx, !/usr/bin/systemctl * *

# Sudo logging
sudo tail -f /var/log/sudo.log
sudo journalctl -u sudo
```

### 1.4 SSH Hardening

#### Advanced SSH Security
```bash
# Edit SSH configuration
sudo nano /etc/ssh/sshd_config

# Security settings:
Protocol 2                           # Use SSH protocol 2 only
Port 2222                           # Change default port
PermitRootLogin no                  # Disable root login
PasswordAuthentication no           # Disable password auth
PubkeyAuthentication yes            # Enable key-based auth
PermitEmptyPasswords no             # No empty passwords
ChallengeResponseAuthentication no  # Disable challenge-response
UsePAM yes                          # Use PAM
X11Forwarding no                    # Disable X11 forwarding
PrintMotd no                        # Disable MOTD
PrintLastLog yes                    # Show last login
TCPKeepAlive yes                    # Enable TCP keepalive
ClientAliveInterval 300             # Send keepalive every 5 min
ClientAliveCountMax 2               # Disconnect after 2 missed
MaxAuthTries 3                      # Max authentication attempts
MaxSessions 2                       # Max concurrent sessions
LoginGraceTime 60                   # Login timeout
MaxStartups 10:30:60                # Connection rate limiting

# Restrict users/groups
AllowUsers user1 user2
AllowGroups sshusers
DenyUsers baduser
DenyGroups nologin

# Restrict to specific IPs
Match Address 192.168.1.0/24
    PasswordAuthentication yes

# Chroot SFTP users
Match Group sftponly
    ChrootDirectory /home/%u
    ForceCommand internal-sftp
    AllowTcpForwarding no
    X11Forwarding no

# Test configuration
sudo sshd -t

# Restart SSH
sudo systemctl restart sshd
```

#### SSH Key Management
```bash
# Generate strong SSH keys
ssh-keygen -t ed25519 -a 100 -C "user@host"
ssh-keygen -t rsa -b 4096 -C "user@host"

# Set proper permissions
chmod 700 ~/.ssh
chmod 600 ~/.ssh/id_rsa
chmod 644 ~/.ssh/id_rsa.pub
chmod 600 ~/.ssh/authorized_keys
chmod 600 ~/.ssh/config

# Disable weak algorithms
# Add to /etc/ssh/sshd_config:
KexAlgorithms curve25519-sha256,curve25519-sha256@libssh.org
Ciphers chacha20-poly1305@openssh.com,aes256-gcm@openssh.com,aes128-gcm@openssh.com
MACs hmac-sha2-512-etm@openssh.com,hmac-sha2-256-etm@openssh.com
```

---

## Module 2: File System Security (Day 19)

### 2.1 File Permissions and Ownership

#### Advanced Permission Management
```bash
# Review file permissions
ls -la /path/to/directory

# Find files with specific permissions
find / -type f -perm 0777 2>/dev/null
find / -type f -perm -4000 2>/dev/null  # SUID files
find / -type f -perm -2000 2>/dev/null  # SGID files
find / -type f -perm -1000 2>/dev/null  # Sticky bit

# Find world-writable files
find / -xdev -type f -perm -0002 2>/dev/null

# Find files with no owner
find / -nouser -o -nogroup 2>/dev/null

# Find files owned by specific user
find / -user username 2>/dev/null

# Secure important directories
sudo chmod 700 /root
sudo chmod 600 /boot/grub/grub.cfg
sudo chmod 644 /etc/passwd
sudo chmod 640 /etc/shadow
sudo chmod 644 /etc/group
sudo chmod 640 /etc/gshadow
sudo chmod 600 /etc/ssh/sshd_config
```


#### Special Permissions (SUID, SGID, Sticky Bit)
```bash
# SUID (Set User ID) - Run as file owner
chmod u+s file
chmod 4755 file

# SGID (Set Group ID) - Run as group owner
chmod g+s file
chmod 2755 file

# Sticky Bit - Only owner can delete
chmod +t directory
chmod 1777 directory

# Find and audit SUID/SGID files
sudo find / -type f \( -perm -4000 -o -perm -2000 \) -exec ls -la {} \; 2>/dev/null

# Remove SUID/SGID if not needed
sudo chmod u-s /path/to/file
sudo chmod g-s /path/to/file

# Common legitimate SUID files:
/usr/bin/passwd
/usr/bin/sudo
/usr/bin/su
/bin/ping
/usr/bin/mount
/usr/bin/umount
```

#### File Attributes (chattr/lsattr)
```bash
# View file attributes
lsattr filename

# Make file immutable (cannot be modified/deleted)
sudo chattr +i filename

# Remove immutable attribute
sudo chattr -i filename

# Append-only (for log files)
sudo chattr +a logfile

# Prevent deletion
sudo chattr +u filename

# Secure deletion (overwrite on delete)
sudo chattr +s filename

# Common use cases:
sudo chattr +i /etc/passwd      # Protect password file
sudo chattr +i /etc/shadow      # Protect shadow file
sudo chattr +a /var/log/auth.log # Append-only logs
```

### 2.2 Disk Encryption

#### LUKS (Linux Unified Key Setup)
```bash
# Install cryptsetup
sudo apt install cryptsetup

# Create encrypted partition
sudo cryptsetup luksFormat /dev/sdb1

# Open encrypted partition
sudo cryptsetup luksOpen /dev/sdb1 encrypted_volume

# Create filesystem
sudo mkfs.ext4 /dev/mapper/encrypted_volume

# Mount encrypted volume
sudo mount /dev/mapper/encrypted_volume /mnt/encrypted

# Close encrypted volume
sudo umount /mnt/encrypted
sudo cryptsetup luksClose encrypted_volume

# Add additional key
sudo cryptsetup luksAddKey /dev/sdb1

# Remove key
sudo cryptsetup luksRemoveKey /dev/sdb1

# Backup LUKS header
sudo cryptsetup luksHeaderBackup /dev/sdb1 --header-backup-file luks-header.img

# Restore LUKS header
sudo cryptsetup luksHeaderRestore /dev/sdb1 --header-backup-file luks-header.img
```


#### Automatic Mounting of Encrypted Volumes
```bash
# Create key file
sudo dd if=/dev/urandom of=/root/keyfile bs=1024 count=4
sudo chmod 400 /root/keyfile

# Add key to LUKS
sudo cryptsetup luksAddKey /dev/sdb1 /root/keyfile

# Configure crypttab
sudo nano /etc/crypttab

# Add entry:
encrypted_volume /dev/sdb1 /root/keyfile luks

# Configure fstab
sudo nano /etc/fstab

# Add entry:
/dev/mapper/encrypted_volume /mnt/encrypted ext4 defaults 0 2

# Test
sudo mount -a
```

### 2.3 File Integrity Monitoring

#### AIDE (Advanced Intrusion Detection Environment)
```bash
# Install AIDE
sudo apt install aide

# Initialize AIDE database
sudo aideinit

# Move database to production location
sudo mv /var/lib/aide/aide.db.new /var/lib/aide/aide.db

# Configure AIDE
sudo nano /etc/aide/aide.conf

# Key settings:
database=file:/var/lib/aide/aide.db
database_out=file:/var/lib/aide/aide.db.new
gzip_dbout=yes

# Define what to monitor:
/etc p+i+n+u+g+s+b+m+c+md5+sha256
/bin p+i+n+u+g+s+b+m+c+md5+sha256
/sbin p+i+n+u+g+s+b+m+c+md5+sha256
/usr/bin p+i+n+u+g+s+b+m+c+md5+sha256
/usr/sbin p+i+n+u+g+s+b+m+c+md5+sha256

# Exclude directories:
!/var/log
!/tmp
!/proc
!/sys

# Run integrity check
sudo aide --check

# Update database after legitimate changes
sudo aide --update
sudo mv /var/lib/aide/aide.db.new /var/lib/aide/aide.db

# Automate with cron
sudo crontab -e
0 2 * * * /usr/bin/aide --check | mail -s "AIDE Report" admin@example.com
```

---

## Module 3: System Auditing and Logging (Day 20)

### 3.1 System Auditing with auditd

#### Installing and Configuring auditd
```bash
# Install auditd
sudo apt install auditd audispd-plugins

# Start and enable
sudo systemctl start auditd
sudo systemctl enable auditd

# Check status
sudo systemctl status auditd

# Main configuration
sudo nano /etc/audit/auditd.conf

# Key settings:
log_file = /var/log/audit/audit.log
log_format = ENRICHED
flush = INCREMENTAL_ASYNC
freq = 50
max_log_file = 8
num_logs = 5
max_log_file_action = ROTATE
```


#### Audit Rules
```bash
# View current rules
sudo auditctl -l

# Delete all rules
sudo auditctl -D

# Add audit rules
# Monitor file access
sudo auditctl -w /etc/passwd -p wa -k passwd_changes
sudo auditctl -w /etc/shadow -p wa -k shadow_changes
sudo auditctl -w /etc/group -p wa -k group_changes
sudo auditctl -w /etc/sudoers -p wa -k sudoers_changes

# Monitor authentication
sudo auditctl -w /var/log/auth.log -p wa -k auth_log
sudo auditctl -w /var/log/faillog -p wa -k login_failures

# Monitor network configuration
sudo auditctl -w /etc/network/ -p wa -k network_changes
sudo auditctl -w /etc/hosts -p wa -k hosts_changes

# Monitor system calls
sudo auditctl -a always,exit -F arch=b64 -S adjtimex -S settimeofday -k time_change
sudo auditctl -a always,exit -F arch=b64 -S mount -S umount2 -k mounts

# Monitor privileged commands
sudo auditctl -a always,exit -F path=/usr/bin/sudo -F perm=x -k sudo_usage
sudo auditctl -a always,exit -F path=/usr/bin/passwd -F perm=x -k passwd_usage

# Make rules persistent
sudo nano /etc/audit/rules.d/audit.rules

# Add rules to file, then reload:
sudo augenrules --load
```

#### Searching Audit Logs
```bash
# Search audit logs
sudo ausearch -k passwd_changes
sudo ausearch -k sudo_usage
sudo ausearch -ts today
sudo ausearch -ts 10:00 -te 11:00
sudo ausearch -ua username
sudo ausearch -f /etc/passwd

# Generate audit reports
sudo aureport
sudo aureport --summary
sudo aureport --auth
sudo aureport --failed
sudo aureport --file
sudo aureport --executable

# Real-time monitoring
sudo ausearch -ts recent -i

# Export to file
sudo ausearch -k passwd_changes > audit_report.txt
```

### 3.2 Centralized Logging

#### Configuring rsyslog
```bash
# Edit rsyslog configuration
sudo nano /etc/rsyslog.conf

# Enable remote logging (server)
module(load="imudp")
input(type="imudp" port="514")

module(load="imtcp")
input(type="imtcp" port="514")

# Template for remote logs
$template RemoteLogs,"/var/log/remote/%HOSTNAME%/%PROGRAMNAME%.log"
*.* ?RemoteLogs

# Client configuration (send logs to server)
*.* @192.168.1.100:514    # UDP
*.* @@192.168.1.100:514   # TCP

# Restart rsyslog
sudo systemctl restart rsyslog

# Test logging
logger -t test "Test message from $(hostname)"
```

#### Log Rotation
```bash
# Configure logrotate
sudo nano /etc/logrotate.conf

# Global settings:
weekly
rotate 4
create
compress
include /etc/logrotate.d

# Custom log rotation
sudo nano /etc/logrotate.d/custom

/var/log/custom/*.log {
    daily
    rotate 30
    compress
    delaycompress
    missingok
    notifempty
    create 0640 root adm
    sharedscripts
    postrotate
        /usr/bin/systemctl reload rsyslog > /dev/null 2>&1 || true
    endscript
}

# Test logrotate
sudo logrotate -d /etc/logrotate.conf
sudo logrotate -f /etc/logrotate.conf
```

### 3.3 Security Monitoring

#### Real-time Log Monitoring
```bash
# Monitor authentication logs
sudo tail -f /var/log/auth.log

# Monitor all logs
sudo journalctl -f

# Monitor specific service
sudo journalctl -u sshd -f

# Monitor failed logins
sudo tail -f /var/log/auth.log | grep Failed

# Monitor sudo usage
sudo tail -f /var/log/auth.log | grep sudo

# Monitor file changes with inotify
sudo apt install inotify-tools

# Watch directory for changes
inotifywait -m -r -e modify,create,delete /etc/
```
