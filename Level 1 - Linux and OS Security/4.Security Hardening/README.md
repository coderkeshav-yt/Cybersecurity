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


---

## Module 4: Security Tools and Scanning (Day 21)

### 4.1 Vulnerability Scanning

#### Lynis - Security Auditing Tool
```bash
# Install Lynis
sudo apt install lynis

# Run full system audit
sudo lynis audit system

# Run specific tests
sudo lynis audit system --tests-from-group authentication
sudo lynis audit system --tests-from-group networking

# Generate report
sudo lynis audit system --report-file /tmp/lynis-report.txt

# View suggestions
sudo cat /var/log/lynis.log | grep Suggestion

# View warnings
sudo cat /var/log/lynis.log | grep Warning

# Automated scanning
sudo crontab -e
0 3 * * 0 /usr/bin/lynis audit system --cronjob
```

#### OpenVAS (Greenbone Vulnerability Manager)
```bash
# Install OpenVAS
sudo apt install openvas

# Setup OpenVAS
sudo gvm-setup

# Start services
sudo gvm-start

# Access web interface
# https://localhost:9392

# Update feeds
sudo gvm-feed-update

# Create scan target
# Create scan task
# Review results
```

#### Nikto - Web Server Scanner
```bash
# Install Nikto
sudo apt install nikto

# Basic scan
nikto -h http://target.com

# Scan with SSL
nikto -h https://target.com

# Scan specific port
nikto -h target.com -p 8080

# Save output
nikto -h target.com -o report.html -Format html

# Scan multiple hosts
nikto -h hosts.txt

# Tuning options
nikto -h target.com -Tuning 123456789
```

### 4.2 Rootkit Detection

#### rkhunter (Rootkit Hunter)
```bash
# Install rkhunter
sudo apt install rkhunter

# Update database
sudo rkhunter --update

# Run check
sudo rkhunter --check

# Skip keypress prompts
sudo rkhunter --check --skip-keypress

# Report warnings only
sudo rkhunter --check --report-warnings-only

# Update file properties
sudo rkhunter --propupd

# Configuration
sudo nano /etc/rkhunter.conf

# Key settings:
UPDATE_MIRRORS=1
MIRRORS_MODE=0
WEB_CMD="/usr/bin/wget"
ALLOW_SSH_ROOT_USER=no
ALLOW_SSH_PROT_V1=0

# Automate with cron
sudo crontab -e
0 3 * * * /usr/bin/rkhunter --check --skip-keypress --report-warnings-only | mail -s "rkhunter Report" admin@example.com
```

#### chkrootkit
```bash
# Install chkrootkit
sudo apt install chkrootkit

# Run check
sudo chkrootkit

# Check specific test
sudo chkrootkit -l
sudo chkrootkit sniffer

# Quiet mode (show only infected)
sudo chkrootkit -q

# Automate
sudo crontab -e
0 4 * * * /usr/sbin/chkrootkit | mail -s "chkrootkit Report" admin@example.com
```

### 4.3 Malware Scanning

#### ClamAV - Antivirus
```bash
# Install ClamAV
sudo apt install clamav clamav-daemon

# Update virus definitions
sudo freshclam

# Scan directory
sudo clamscan -r /home

# Scan and remove infected files
sudo clamscan -r --remove /home

# Scan and move infected files
sudo clamscan -r --move=/quarantine /home

# Scan with verbose output
sudo clamscan -r -v /home

# Scan and log
sudo clamscan -r -l /var/log/clamav/scan.log /home

# Real-time scanning daemon
sudo systemctl start clamav-daemon
sudo systemctl enable clamav-daemon

# Configure on-access scanning
sudo nano /etc/clamav/clamd.conf

# Automate daily scans
sudo crontab -e
0 2 * * * /usr/bin/clamscan -r /home --log=/var/log/clamav/daily-scan.log
```

---

## Module 5: Compliance and Best Practices (Day 22)

### 5.1 CIS Benchmarks

#### Understanding CIS Benchmarks
```
CIS (Center for Internet Security) provides security configuration benchmarks
Levels:
- Level 1: Basic security, minimal impact on functionality
- Level 2: Enhanced security, may impact functionality

Key areas:
1. Initial Setup
2. Services
3. Network Configuration
4. Logging and Auditing
5. Access, Authentication and Authorization
6. System Maintenance
```


#### Implementing CIS Controls
```bash
# 1. Filesystem Configuration
# Ensure separate partitions
/tmp
/var
/var/tmp
/var/log
/var/log/audit
/home

# Mount options for /tmp
sudo nano /etc/fstab
tmpfs /tmp tmpfs defaults,rw,nosuid,nodev,noexec,relatime 0 0

# 2. Disable unnecessary services
sudo systemctl disable avahi-daemon
sudo systemctl disable cups
sudo systemctl disable isc-dhcp-server
sudo systemctl disable isc-dhcp-server6
sudo systemctl disable slapd
sudo systemctl disable nfs-server
sudo systemctl disable rpcbind
sudo systemctl disable bind9
sudo systemctl disable vsftpd
sudo systemctl disable apache2
sudo systemctl disable dovecot
sudo systemctl disable smbd
sudo systemctl disable snmpd
sudo systemctl disable rsync

# 3. Network hardening
sudo nano /etc/sysctl.conf

# IP forwarding
net.ipv4.ip_forward = 0
net.ipv6.conf.all.forwarding = 0

# Packet redirect
net.ipv4.conf.all.send_redirects = 0
net.ipv4.conf.default.send_redirects = 0

# Source routing
net.ipv4.conf.all.accept_source_route = 0
net.ipv4.conf.default.accept_source_route = 0
net.ipv6.conf.all.accept_source_route = 0
net.ipv6.conf.default.accept_source_route = 0

# ICMP redirects
net.ipv4.conf.all.accept_redirects = 0
net.ipv4.conf.default.accept_redirects = 0
net.ipv6.conf.all.accept_redirects = 0
net.ipv6.conf.default.accept_redirects = 0

# Secure ICMP redirects
net.ipv4.conf.all.secure_redirects = 0
net.ipv4.conf.default.secure_redirects = 0

# Log suspicious packets
net.ipv4.conf.all.log_martians = 1
net.ipv4.conf.default.log_martians = 1

# Ignore ICMP ping
net.ipv4.icmp_echo_ignore_all = 1

# Ignore broadcast pings
net.ipv4.icmp_echo_ignore_broadcasts = 1

# SYN flood protection
net.ipv4.tcp_syncookies = 1
net.ipv4.tcp_max_syn_backlog = 2048
net.ipv4.tcp_synack_retries = 2
net.ipv4.tcp_syn_retries = 5

# Apply settings
sudo sysctl -p

# 4. Disable IPv6 (if not needed)
sudo nano /etc/default/grub
GRUB_CMDLINE_LINUX="ipv6.disable=1"
sudo update-grub
```

### 5.2 SELinux / AppArmor

#### AppArmor (Ubuntu/Debian)
```bash
# Check AppArmor status
sudo aa-status

# Install AppArmor utilities
sudo apt install apparmor-utils

# List profiles
sudo aa-status

# Set profile to enforce mode
sudo aa-enforce /etc/apparmor.d/usr.sbin.nginx

# Set profile to complain mode
sudo aa-complain /etc/apparmor.d/usr.sbin.nginx

# Disable profile
sudo aa-disable /etc/apparmor.d/usr.sbin.nginx

# Reload profiles
sudo systemctl reload apparmor

# Create custom profile
sudo aa-genprof /usr/bin/myapp

# Update profile
sudo aa-logprof

# View AppArmor logs
sudo journalctl -fx | grep apparmor
sudo dmesg | grep apparmor
```

#### SELinux (RHEL/CentOS)
```bash
# Check SELinux status
sestatus
getenforce

# Set SELinux mode
sudo setenforce 0  # Permissive
sudo setenforce 1  # Enforcing

# Permanent configuration
sudo nano /etc/selinux/config
SELINUX=enforcing
SELINUXTYPE=targeted

# View SELinux contexts
ls -Z /path/to/file
ps -eZ

# Change file context
sudo chcon -t httpd_sys_content_t /var/www/html/index.html

# Restore default context
sudo restorecon -Rv /var/www/html/

# SELinux booleans
getsebool -a
sudo setsebool -P httpd_can_network_connect on

# View SELinux denials
sudo ausearch -m avc -ts recent
sudo grep AVC /var/log/audit/audit.log

# Generate policy from denials
sudo audit2allow -a
sudo audit2allow -a -M mypolicy
sudo semodule -i mypolicy.pp
```

### 5.3 Kernel Hardening

#### Kernel Parameters
```bash
# Edit sysctl configuration
sudo nano /etc/sysctl.d/99-security.conf

# Kernel hardening
kernel.dmesg_restrict = 1                    # Restrict dmesg
kernel.kptr_restrict = 2                     # Hide kernel pointers
kernel.yama.ptrace_scope = 1                 # Restrict ptrace
kernel.kexec_load_disabled = 1               # Disable kexec
kernel.unprivileged_bpf_disabled = 1         # Disable unprivileged BPF
kernel.unprivileged_userns_clone = 0         # Disable user namespaces
fs.protected_hardlinks = 1                   # Protect hardlinks
fs.protected_symlinks = 1                    # Protect symlinks
fs.suid_dumpable = 0                         # Disable core dumps for SUID

# Apply settings
sudo sysctl -p /etc/sysctl.d/99-security.conf
```


#### Kernel Module Management
```bash
# List loaded modules
lsmod

# Load module
sudo modprobe module_name

# Remove module
sudo modprobe -r module_name

# Blacklist modules
sudo nano /etc/modprobe.d/blacklist.conf

# Disable uncommon protocols
install dccp /bin/true
install sctp /bin/true
install rds /bin/true
install tipc /bin/true

# Disable uncommon filesystems
install cramfs /bin/true
install freevxfs /bin/true
install jffs2 /bin/true
install hfs /bin/true
install hfsplus /bin/true
install udf /bin/true

# Disable USB storage (if not needed)
install usb-storage /bin/true

# Update initramfs
sudo update-initramfs -u
```

### 5.4 Security Automation Scripts

#### System Hardening Script
```bash
#!/bin/bash
# system-hardening.sh - Automated security hardening

echo "Starting system hardening..."

# Update system
apt update && apt upgrade -y

# Install security tools
apt install -y fail2ban ufw aide rkhunter clamav auditd

# Configure firewall
ufw default deny incoming
ufw default allow outgoing
ufw allow ssh
ufw enable

# Secure SSH
sed -i 's/#PermitRootLogin yes/PermitRootLogin no/' /etc/ssh/sshd_config
sed -i 's/#PasswordAuthentication yes/PasswordAuthentication no/' /etc/ssh/sshd_config
systemctl restart sshd

# Configure automatic updates
apt install -y unattended-upgrades
dpkg-reconfigure -plow unattended-upgrades

# Set password policies
sed -i 's/PASS_MAX_DAYS\t99999/PASS_MAX_DAYS\t90/' /etc/login.defs
sed -i 's/PASS_MIN_DAYS\t0/PASS_MIN_DAYS\t7/' /etc/login.defs

# Disable unnecessary services
systemctl disable avahi-daemon
systemctl disable cups

# Initialize AIDE
aideinit
mv /var/lib/aide/aide.db.new /var/lib/aide/aide.db

# Update ClamAV
freshclam

# Configure auditd
systemctl enable auditd
systemctl start auditd

echo "System hardening complete!"
```

#### Security Audit Script
```bash
#!/bin/bash
# security-audit.sh - Automated security audit

REPORT="/tmp/security-audit-$(date +%Y%m%d).txt"

echo "Security Audit Report - $(date)" > $REPORT
echo "======================================" >> $REPORT

# Check for users with UID 0
echo -e "\n[*] Users with UID 0:" >> $REPORT
awk -F: '($3 == 0) {print}' /etc/passwd >> $REPORT

# Check for users without passwords
echo -e "\n[*] Users without passwords:" >> $REPORT
awk -F: '($2 == "") {print $1}' /etc/shadow >> $REPORT

# Check for world-writable files
echo -e "\n[*] World-writable files:" >> $REPORT
find / -xdev -type f -perm -0002 2>/dev/null | head -20 >> $REPORT

# Check for SUID files
echo -e "\n[*] SUID files:" >> $REPORT
find / -xdev -type f -perm -4000 2>/dev/null >> $REPORT

# Check listening ports
echo -e "\n[*] Listening ports:" >> $REPORT
ss -tlnp >> $REPORT

# Check failed login attempts
echo -e "\n[*] Recent failed logins:" >> $REPORT
grep "Failed password" /var/log/auth.log | tail -20 >> $REPORT

# Check sudo usage
echo -e "\n[*] Recent sudo usage:" >> $REPORT
grep sudo /var/log/auth.log | tail -20 >> $REPORT

# Check firewall status
echo -e "\n[*] Firewall status:" >> $REPORT
ufw status verbose >> $REPORT

# Check for rootkits
echo -e "\n[*] Rootkit scan:" >> $REPORT
rkhunter --check --skip-keypress --report-warnings-only >> $REPORT

echo "Audit complete. Report saved to $REPORT"
```

---

## Practical Exercises

### Exercise 1: User Security (Day 18)
1. Implement strong password policies with PAM
2. Configure account lockout after failed attempts
3. Set up password aging for all users
4. Configure sudo with least privilege
5. Harden SSH configuration
6. Implement two-factor authentication (optional)

### Exercise 2: File System Security (Day 19)
1. Audit and secure file permissions
2. Find and review all SUID/SGID files
3. Implement file attributes with chattr
4. Set up encrypted partition with LUKS
5. Configure AIDE for file integrity monitoring
6. Create automated integrity check reports

### Exercise 3: System Auditing (Day 20)
1. Configure auditd with comprehensive rules
2. Set up centralized logging with rsyslog
3. Implement log rotation policies
4. Create real-time monitoring alerts
5. Generate audit reports
6. Set up automated log analysis

### Exercise 4: Security Scanning (Day 21)
1. Run Lynis security audit
2. Perform vulnerability scan with OpenVAS
3. Scan web applications with Nikto
4. Check for rootkits with rkhunter and chkrootkit
5. Scan for malware with ClamAV
6. Document and remediate findings

### Exercise 5: Compliance (Day 22)
1. Implement CIS Benchmark Level 1 controls
2. Configure AppArmor or SELinux
3. Harden kernel parameters
4. Disable unnecessary services and protocols
5. Create security hardening script
6. Perform final security audit


---

## Daily Challenges

### Day 18 Challenge: Secure Authentication System
Build a comprehensive authentication system:
- Multi-layered password policies
- Account lockout mechanism
- SSH key-only authentication
- Sudo access with logging
- Session timeout policies
- Failed login monitoring and alerting

### Day 19 Challenge: Secure File Server
Create a hardened file server:
- Encrypted storage with LUKS
- Strict file permissions
- File integrity monitoring
- Immutable system files
- Audit trail for all file access
- Automated security checks

### Day 20 Challenge: Security Monitoring System
Implement comprehensive monitoring:
- System-wide audit rules
- Centralized log collection
- Real-time alert system
- Automated log analysis
- Security event correlation
- Daily security reports

### Day 21 Challenge: Vulnerability Management
Build a vulnerability management system:
- Automated security scanning
- Vulnerability assessment
- Rootkit detection
- Malware scanning
- Remediation tracking
- Compliance reporting

### Day 22 Challenge: Hardened Production Server
Deploy a fully hardened server:
- CIS Benchmark compliance
- Mandatory Access Control (AppArmor/SELinux)
- Kernel hardening
- Minimal attack surface
- Automated security updates
- Continuous security monitoring

---

## Troubleshooting Common Issues

### PAM Configuration Issues
```bash
# Test PAM configuration
sudo pamtester login username authenticate

# Check PAM logs
sudo journalctl -u systemd-logind
sudo tail -f /var/log/auth.log

# Reset PAM if locked out
# Boot into recovery mode
mount -o remount,rw /
nano /etc/pam.d/common-auth
# Comment out problematic lines
reboot
```

### Audit System Issues
```bash
# Check auditd status
sudo systemctl status auditd

# View audit errors
sudo ausearch -m DAEMON_START

# Restart auditd
sudo service auditd restart

# Check disk space
df -h /var/log/audit/

# Rotate audit logs
sudo service auditd rotate
```

### AppArmor/SELinux Issues
```bash
# AppArmor - Set to complain mode
sudo aa-complain /etc/apparmor.d/*

# View AppArmor denials
sudo journalctl -fx | grep apparmor

# SELinux - Set to permissive
sudo setenforce 0

# View SELinux denials
sudo ausearch -m avc -ts recent

# Generate policy
sudo audit2allow -a -M mypolicy
sudo semodule -i mypolicy.pp
```

### Encrypted Volume Issues
```bash
# Check LUKS header
sudo cryptsetup luksDump /dev/sdb1

# Repair filesystem
sudo fsck /dev/mapper/encrypted_volume

# Backup LUKS header
sudo cryptsetup luksHeaderBackup /dev/sdb1 --header-backup-file backup.img

# Emergency access
# Boot from live USB
sudo cryptsetup luksOpen /dev/sdb1 rescue
sudo mount /dev/mapper/rescue /mnt
```

---

## Best Practices

### User Security
1. Enforce strong password policies
2. Implement multi-factor authentication
3. Use key-based authentication for SSH
4. Apply principle of least privilege
5. Regular user access reviews
6. Disable unused accounts
7. Monitor privileged access
8. Implement session timeouts

### File System Security
1. Use appropriate file permissions
2. Regularly audit SUID/SGID files
3. Implement file integrity monitoring
4. Encrypt sensitive data
5. Use immutable attributes for critical files
6. Regular backup and verification
7. Secure mount options
8. Monitor file system changes

### System Auditing
1. Enable comprehensive audit rules
2. Centralize log collection
3. Implement log retention policies
4. Regular log review and analysis
5. Real-time alerting for critical events
6. Protect log files from tampering
7. Automated log rotation
8. Compliance reporting

### Security Scanning
1. Regular vulnerability assessments
2. Automated security scans
3. Rootkit detection
4. Malware scanning
5. Patch management
6. Configuration compliance checks
7. Penetration testing
8. Security metrics tracking

### Compliance
1. Follow industry standards (CIS, NIST)
2. Implement mandatory access controls
3. Kernel hardening
4. Disable unnecessary services
5. Network hardening
6. Regular security audits
7. Documentation and change management
8. Continuous improvement

---

## Additional Resources

### Documentation
- [CIS Benchmarks](https://www.cisecurity.org/cis-benchmarks/)
- [NIST Cybersecurity Framework](https://www.nist.gov/cyberframework)
- [Linux Security Guide](https://www.kernel.org/doc/html/latest/admin-guide/security.html)
- [AppArmor Documentation](https://gitlab.com/apparmor/apparmor/-/wikis/home)
- [SELinux Project](https://github.com/SELinuxProject)

### Tools
- [Lynis](https://cisofy.com/lynis/) - Security auditing tool
- [OpenSCAP](https://www.open-scap.org/) - Security compliance
- [AIDE](https://aide.github.io/) - File integrity monitoring
- [Auditd](https://linux.die.net/man/8/auditd) - Linux audit daemon
- [ClamAV](https://www.clamav.net/) - Antivirus engine

### Books
- "Linux Security Cookbook" by Daniel J. Barrett
- "Practical Linux Security" by Tajinder Kalsi
- "Mastering Linux Security and Hardening" by Donald A. Tevault
- "Linux Hardening in Hostile Networks" by Kyle Rankin

### Online Platforms
- [Linux Security](https://linuxsecurity.com/)
- [SANS Reading Room](https://www.sans.org/reading-room/)
- [OWASP](https://owasp.org/)

---

## Summary and Next Steps

After completing this Security Hardening module, you should be proficient in:

✓ Implementing strong authentication and access controls
✓ Securing file systems and data encryption
✓ Configuring comprehensive system auditing
✓ Using security scanning and vulnerability assessment tools
✓ Applying compliance frameworks and best practices
✓ Automating security tasks and monitoring
✓ Troubleshooting security-related issues
✓ Maintaining a hardened production environment

**Next Module**: Proceed to **Day 23+: Advanced Topics & Practical Labs** where you'll apply all learned skills in real-world scenarios and advanced security challenges.

**Certification Path**: Consider pursuing:
- CompTIA Security+
- Linux Professional Institute Security Essentials (LPIC-303)
- Certified Information Systems Security Professional (CISSP)
- Certified Ethical Hacker (CEH)
- GIAC Security Essentials (GSEC)

**Career Paths**:
- Linux Security Administrator
- Security Operations Center (SOC) Analyst
- Security Engineer
- Compliance Analyst
- Penetration Tester

Keep practicing these skills in isolated lab environments and gradually implement them in production systems with proper change management and testing!
