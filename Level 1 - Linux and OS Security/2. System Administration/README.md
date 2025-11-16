# Day 6-10: System Administration - Complete Guide

## Overview
System Administration is the backbone of managing Linux systems effectively. This comprehensive module covers package management, process control, system monitoring, service management, storage administration, and automation. By mastering these skills, you'll be able to maintain, troubleshoot, and optimize Linux systems in production environments.

**Duration**: 5 Days (Days 6-10)
**Prerequisites**: Completion of Linux Fundamentals (Days 1-5)
**Difficulty**: Intermediate

---

## Module 1: Package Management (Day 6)

### 1.1 Understanding Package Management Systems

#### What is a Package Manager?
A package manager is a tool that automates the process of installing, upgrading, configuring, and removing software packages. It handles dependencies, version conflicts, and maintains a database of installed software.

#### Major Package Management Systems

**1. APT (Advanced Package Tool) - Debian/Ubuntu**
- Frontend for dpkg
- Handles dependencies automatically
- Uses .deb package format
- Repositories defined in /etc/apt/sources.list

**2. YUM/DNF - RHEL/CentOS/Fedora**
- YUM (Yellowdog Updater Modified) - older systems
- DNF (Dandified YUM) - modern replacement
- Uses .rpm package format
- Repositories in /etc/yum.repos.d/

**3. Pacman - Arch Linux**
- Simple and fast package manager
- Uses .pkg.tar.xz format
- Configuration in /etc/pacman.conf

**4. Zypper - openSUSE**
- Command-line interface for ZYpp package manager
- Uses .rpm format
- Advanced dependency resolution

### 1.2 APT Package Management (Debian/Ubuntu)

#### Basic APT Commands
```bash
# Update package index
sudo apt update

# Upgrade all packages
sudo apt upgrade

# Full upgrade (handles dependencies better)
sudo apt full-upgrade

# Install a package
sudo apt install package_name

# Install multiple packages
sudo apt install package1 package2 package3

# Remove a package (keep configuration files)
sudo apt remove package_name

# Remove package and configuration files
sudo apt purge package_name

# Remove unnecessary dependencies
sudo apt autoremove

# Search for packages
apt search keyword

# Show package information
apt show package_name

# List installed packages
apt list --installed

# List upgradable packages
apt list --upgradable

# Download package without installing
apt download package_name

# Clean package cache
sudo apt clean
sudo apt autoclean
```

#### Managing Repositories
```bash
# Add a repository
sudo add-apt-repository ppa:repository_name

# Remove a repository
sudo add-apt-repository --remove ppa:repository_name

# Edit sources list
sudo nano /etc/apt/sources.list

# List repositories
grep -r --include '*.list' '^deb ' /etc/apt/

# Update GPG keys
sudo apt-key list
sudo apt-key add keyfile.gpg
```

#### Advanced APT Usage
```bash
# Install specific version
sudo apt install package_name=version

# Hold a package (prevent updates)
sudo apt-mark hold package_name

# Unhold a package
sudo apt-mark unhold package_name

# Show held packages
apt-mark showhold

# Simulate installation (dry run)
sudo apt install -s package_name

# Fix broken dependencies
sudo apt --fix-broken install

# Reconfigure a package
sudo dpkg-reconfigure package_name
```

### 1.3 YUM/DNF Package Management (RHEL/CentOS/Fedora)

#### Basic YUM Commands
```bash
# Update package index
sudo yum check-update

# Update all packages
sudo yum update

# Install a package
sudo yum install package_name

# Remove a package
sudo yum remove package_name

# Search for packages
yum search keyword

# Show package information
yum info package_name

# List installed packages
yum list installed

# List available packages
yum list available

# Clean cache
sudo yum clean all
```

#### Basic DNF Commands (Modern Replacement for YUM)
```bash
# Update system
sudo dnf upgrade

# Install package
sudo dnf install package_name

# Remove package
sudo dnf remove package_name

# Search packages
dnf search keyword

# Show package info
dnf info package_name

# List installed
dnf list installed

# Group install
sudo dnf groupinstall "Development Tools"

# Show history
dnf history

# Undo transaction
sudo dnf history undo <id>
```

#### Managing Repositories (YUM/DNF)
```bash
# List repositories
yum repolist
dnf repolist

# Add repository
sudo yum-config-manager --add-repo repository_url
sudo dnf config-manager --add-repo repository_url

# Enable repository
sudo yum-config-manager --enable repo_name
sudo dnf config-manager --set-enabled repo_name

# Disable repository
sudo yum-config-manager --disable repo_name
sudo dnf config-manager --set-disabled repo_name

# Install from specific repository
sudo yum --enablerepo=repo_name install package_name
```

---

## Module 2: Process Management (Day 7)

### 2.1 Understanding Processes

#### What is a Process?
A process is an instance of a running program. Each process has:
- **PID (Process ID)**: Unique identifier
- **PPID (Parent Process ID)**: ID of the parent process
- **UID/GID**: User and group ownership
- **State**: Running, sleeping, stopped, zombie
- **Priority**: Scheduling priority
- **Memory**: Allocated memory space

#### Process States
- **R (Running)**: Currently executing or ready to run
- **S (Sleeping)**: Waiting for an event to complete
- **D (Uninterruptible Sleep)**: Usually waiting for I/O
- **T (Stopped)**: Stopped by job control signal
- **Z (Zombie)**: Terminated but not reaped by parent
- **I (Idle)**: Kernel thread

#### Process Hierarchy
```
systemd (PID 1)
├── sshd
│   └── sshd (user session)
│       └── bash
│           └── vim
├── cron
└── apache2
    ├── apache2 (worker)
    └── apache2 (worker)
```

### 2.2 Viewing Processes

#### ps Command (Process Status)
```bash
# Show processes for current user
ps

# Show all processes
ps aux

# Show process tree
ps auxf

# Show processes for specific user
ps -u username

# Show processes with specific format
ps -eo pid,ppid,cmd,%mem,%cpu --sort=-%mem

# Show threads
ps -eLf

# Show process by name
ps aux | grep process_name

# Show full command line
ps auxww
```

#### top Command (Real-time Process Monitoring)
```bash
# Start top
top

# Top with specific user
top -u username

# Sort by memory
# Press 'M' while in top

# Sort by CPU
# Press 'P' while in top

# Kill a process from top
# Press 'k' and enter PID

# Change refresh interval
# Press 'd' and enter seconds

# Filter by command
# Press 'o' then type COMMAND=name

# Show threads
# Press 'H'

# Batch mode (for scripts)
top -b -n 1
```

#### htop Command (Enhanced top)
```bash
# Install htop
sudo apt install htop    # Debian/Ubuntu
sudo yum install htop    # RHEL/CentOS

# Run htop
htop

# Features:
# - F2: Setup (customize display)
# - F3: Search for process
# - F4: Filter processes
# - F5: Tree view
# - F6: Sort by column
# - F9: Kill process
# - F10: Quit
```

#### pstree Command (Process Tree)
```bash
# Show process tree
pstree

# Show PIDs
pstree -p

# Show command line arguments
pstree -a

# Show tree for specific user
pstree username

# Show tree for specific PID
pstree -p 1234
```

#### pgrep Command (Process Grep)
```bash
# Find process by name
pgrep process_name

# Show full command
pgrep -a process_name

# Show PID and name
pgrep -l process_name

# Find by user
pgrep -u username

# Count processes
pgrep -c process_name
```

### 2.3 Process Control and Signals

#### Common Signals
```bash
# Signal list
kill -l

# Common signals:
# SIGHUP (1)    - Hangup, reload configuration
# SIGINT (2)    - Interrupt (Ctrl+C)
# SIGQUIT (3)   - Quit (Ctrl+\)
# SIGKILL (9)   - Force kill (cannot be caught)
# SIGTERM (15)  - Terminate gracefully (default)
# SIGSTOP (19)  - Stop process
# SIGCONT (18)  - Continue stopped process
```

#### kill Command
```bash
# Kill process by PID (graceful)
kill PID

# Force kill
kill -9 PID
kill -SIGKILL PID

# Send specific signal
kill -HUP PID
kill -1 PID

# Kill multiple processes
kill PID1 PID2 PID3
```

#### killall Command
```bash
# Kill all processes by name
killall process_name

# Force kill all
killall -9 process_name

# Interactive mode
killall -i process_name

# Kill by user
killall -u username

# Wait for processes to die
killall -w process_name
```

#### pkill Command
```bash
# Kill by process name
pkill process_name

# Kill by pattern
pkill -f "pattern"

# Kill by user
pkill -u username

# Kill oldest process
pkill -o process_name

# Kill newest process
pkill -n process_name
```

### 2.4 Process Priority and Nice Values

#### Understanding Nice Values
- Range: -20 (highest priority) to 19 (lowest priority)
- Default: 0
- Only root can set negative values
- Lower nice value = higher priority

#### nice Command (Start with Priority)
```bash
# Start process with nice value
nice -n 10 command

# Start with high priority (requires root)
sudo nice -n -10 command

# Start with lowest priority
nice -n 19 command
```

#### renice Command (Change Priority)
```bash
# Change priority of running process
renice -n 5 -p PID

# Change priority for all processes of user
renice -n 10 -u username

# Change priority for process group
renice -n 5 -g PGID

# Set highest priority (requires root)
sudo renice -n -20 -p PID
```

### 2.5 Background and Foreground Processes

#### Job Control
```bash
# Run command in background
command &

# List background jobs
jobs

# Bring job to foreground
fg %job_number

# Send job to background
bg %job_number

# Suspend current process (Ctrl+Z)
# Then send to background
bg

# Run command immune to hangups
nohup command &

# Disown a job (remove from job table)
disown %job_number

# Run command detached from terminal
nohup command > output.log 2>&1 &
```

#### Screen and Tmux (Terminal Multiplexers)
```bash
# Screen
screen                    # Start new session
screen -S session_name    # Named session
screen -ls                # List sessions
screen -r session_name    # Reattach
# Ctrl+A, D to detach

# Tmux
tmux                      # Start new session
tmux new -s session_name  # Named session
tmux ls                   # List sessions
tmux attach -t session    # Reattach
# Ctrl+B, D to detach
```

---

## Module 3: System Monitoring and Performance (Day 8)

### 3.1 CPU Monitoring

#### uptime Command
```bash
# Show system uptime and load average
uptime

# Output: 10:30:15 up 5 days, 2:15, 3 users, load average: 0.50, 0.75, 0.80
# Load averages: 1 min, 5 min, 15 min
```

#### mpstat Command (Multiprocessor Statistics)
```bash
# Install sysstat package
sudo apt install sysstat

# Show CPU statistics
mpstat

# Show all CPUs
mpstat -P ALL

# Update every 2 seconds, 5 times
mpstat 2 5

# Show detailed statistics
mpstat -u 2 5
```

#### vmstat Command (Virtual Memory Statistics)
```bash
# Show system statistics
vmstat

# Update every 2 seconds
vmstat 2

# Show in MB
vmstat -S M 2

# Show disk statistics
vmstat -d

# Show active/inactive memory
vmstat -a 2
```

### 3.2 Memory Monitoring

#### free Command
```bash
# Show memory usage
free

# Human-readable format
free -h

# Show in MB
free -m

# Show in GB
free -g

# Update every 2 seconds
free -h -s 2

# Show total line
free -h -t
```

#### Understanding Memory Output
```
              total        used        free      shared  buff/cache   available
Mem:           15Gi       5.2Gi       2.1Gi       324Mi       8.0Gi       9.5Gi
Swap:         2.0Gi          0B       2.0Gi

- total: Total installed RAM
- used: Used by processes
- free: Completely unused
- shared: Used by tmpfs
- buff/cache: Used by kernel buffers and cache
- available: Available for new processes
```

#### /proc/meminfo
```bash
# Detailed memory information
cat /proc/meminfo

# Specific values
grep MemTotal /proc/meminfo
grep MemAvailable /proc/meminfo
grep SwapTotal /proc/meminfo
```

### 3.3 Disk I/O Monitoring

#### iostat Command
```bash
# Show I/O statistics
iostat

# Extended statistics
iostat -x

# Update every 2 seconds
iostat -x 2

# Show specific device
iostat -x sda 2

# Human-readable
iostat -h 2
```

#### iotop Command
```bash
# Install iotop
sudo apt install iotop

# Show I/O usage by process
sudo iotop

# Only show processes doing I/O
sudo iotop -o

# Batch mode
sudo iotop -b -n 3
```

#### df Command (Disk Free)
```bash
# Show disk usage
df

# Human-readable
df -h

# Show inode usage
df -i

# Show filesystem type
df -T

# Exclude specific filesystem types
df -h -x tmpfs -x devtmpfs
```

#### du Command (Disk Usage)
```bash
# Show directory size
du -sh directory/

# Show all files and directories
du -ah directory/

# Show top-level directories only
du -h --max-depth=1 directory/

# Sort by size
du -h directory/ | sort -h

# Show largest directories
du -h / 2>/dev/null | sort -rh | head -20

# Exclude directories
du -h --exclude="*.log" directory/
```

### 3.4 Network Monitoring

#### netstat Command (Network Statistics)
```bash
# Show all connections
netstat -a

# Show listening ports
netstat -l

# Show TCP connections
netstat -t

# Show UDP connections
netstat -u

# Show listening TCP ports with PID
netstat -tlnp

# Show routing table
netstat -r

# Show network statistics
netstat -s

# Continuous monitoring
netstat -c
```

#### ss Command (Socket Statistics - Modern Replacement)
```bash
# Show all sockets
ss -a

# Show listening sockets
ss -l

# Show TCP sockets
ss -t

# Show UDP sockets
ss -u

# Show process using socket
ss -p

# Show listening TCP with process
ss -tlnp

# Show established connections
ss -o state established

# Show socket memory usage
ss -m
```

#### iftop Command (Interface Top)
```bash
# Install iftop
sudo apt install iftop

# Monitor interface
sudo iftop -i eth0

# Show ports
sudo iftop -P

# No DNS resolution
sudo iftop -n
```

#### nethogs Command (Network Hogs)
```bash
# Install nethogs
sudo apt install nethogs

# Monitor network by process
sudo nethogs

# Monitor specific interface
sudo nethogs eth0

# Trace mode
sudo nethogs -t
```

### 3.5 System Logs

#### Important Log Files
```bash
# System logs
/var/log/syslog          # General system logs (Debian/Ubuntu)
/var/log/messages        # General system logs (RHEL/CentOS)
/var/log/auth.log        # Authentication logs (Debian/Ubuntu)
/var/log/secure          # Authentication logs (RHEL/CentOS)
/var/log/kern.log        # Kernel logs
/var/log/dmesg           # Boot messages
/var/log/boot.log        # Boot logs
/var/log/cron            # Cron job logs
/var/log/apache2/        # Apache logs
/var/log/nginx/          # Nginx logs
```

#### journalctl Command (systemd Journal)
```bash
# View all logs
journalctl

# Follow logs (like tail -f)
journalctl -f

# Show logs since boot
journalctl -b

# Show logs from previous boot
journalctl -b -1

# Show logs for specific service
journalctl -u ssh.service

# Show logs for time range
journalctl --since "2024-01-01" --until "2024-01-02"
journalctl --since "1 hour ago"
journalctl --since today

# Show kernel messages
journalctl -k

# Show priority errors and above
journalctl -p err

# Show last 50 lines
journalctl -n 50

# Show in reverse order
journalctl -r

# Show disk usage
journalctl --disk-usage

# Vacuum old logs
sudo journalctl --vacuum-time=7d
sudo journalctl --vacuum-size=500M
```

#### dmesg Command (Kernel Ring Buffer)
```bash
# Show kernel messages
dmesg

# Follow kernel messages
dmesg -w

# Human-readable timestamps
dmesg -T

# Show errors only
dmesg -l err

# Clear ring buffer
sudo dmesg -C
```

#### Log Rotation (logrotate)
```bash
# Configuration file
/etc/logrotate.conf

# Service-specific configs
/etc/logrotate.d/

# Test logrotate
sudo logrotate -d /etc/logrotate.conf

# Force rotation
sudo logrotate -f /etc/logrotate.conf

# Example logrotate config
/var/log/myapp/*.log {
    daily
    rotate 7
    compress
    delaycompress
    missingok
    notifempty
    create 0640 www-data www-data
}
```

---

## Module 4: Service Management with systemd (Day 9)

### 4.1 Understanding systemd

#### What is systemd?
- Modern init system and service manager
- Replaces SysV init
- Manages system services, sockets, devices, mounts
- Parallel service startup
- On-demand service activation

#### systemd Components
- **systemctl**: Control systemd and services
- **journalctl**: Query systemd journal
- **systemd-analyze**: Analyze boot performance
- **hostnamectl**: Control hostname
- **timedatectl**: Control time and date
- **localectl**: Control locale settings

### 4.2 systemctl Command

#### Service Management
```bash
# Start a service
sudo systemctl start service_name

# Stop a service
sudo systemctl stop service_name

# Restart a service
sudo systemctl restart service_name

# Reload configuration without restart
sudo systemctl reload service_name

# Reload or restart if reload not available
sudo systemctl reload-or-restart service_name

# Enable service at boot
sudo systemctl enable service_name

# Disable service at boot
sudo systemctl disable service_name

# Enable and start service
sudo systemctl enable --now service_name

# Check if service is enabled
systemctl is-enabled service_name

# Check if service is active
systemctl is-active service_name

# Check service status
systemctl status service_name

# Show service properties
systemctl show service_name

# Mask service (prevent from starting)
sudo systemctl mask service_name

# Unmask service
sudo systemctl unmask service_name
```

#### Listing Services
```bash
# List all units
systemctl list-units

# List all services
systemctl list-units --type=service

# List running services
systemctl list-units --type=service --state=running

# List failed services
systemctl list-units --state=failed

# List all unit files
systemctl list-unit-files

# List enabled services
systemctl list-unit-files --state=enabled

# List disabled services
systemctl list-unit-files --state=disabled
```

#### System State Management
```bash
# Reboot system
sudo systemctl reboot

# Power off system
sudo systemctl poweroff

# Suspend system
sudo systemctl suspend

# Hibernate system
sudo systemctl hibernate

# Reload systemd configuration
sudo systemctl daemon-reload

# Show system state
systemctl is-system-running
```

### 4.3 Creating Custom systemd Services

#### Service Unit File Structure
```bash
# Location: /etc/systemd/system/myservice.service

[Unit]
Description=My Custom Service
After=network.target

[Service]
Type=simple
User=myuser
WorkingDirectory=/opt/myapp
ExecStart=/usr/bin/python3 /opt/myapp/app.py
Restart=always
RestartSec=10

[Install]
WantedBy=multi-user.target
```

#### Service Types
- **simple**: Default, process is main process
- **forking**: Process forks and parent exits
- **oneshot**: Process exits before starting next service
- **dbus**: Service acquires D-Bus name
- **notify**: Service sends notification when ready
- **idle**: Delays execution until other jobs finish

#### Creating and Managing Custom Service
```bash
# Create service file
sudo nano /etc/systemd/system/myapp.service

# Reload systemd
sudo systemctl daemon-reload

# Enable service
sudo systemctl enable myapp.service

# Start service
sudo systemctl start myapp.service

# Check status
systemctl status myapp.service

# View logs
journalctl -u myapp.service -f
```

### 4.4 systemd Timers (Cron Alternative)

#### Creating a Timer
```bash
# Service file: /etc/systemd/system/backup.service
[Unit]
Description=Backup Service

[Service]
Type=oneshot
ExecStart=/usr/local/bin/backup.sh

# Timer file: /etc/systemd/system/backup.timer
[Unit]
Description=Backup Timer

[Timer]
OnCalendar=daily
Persistent=true

[Install]
WantedBy=timers.target
```

#### Managing Timers
```bash
# Enable timer
sudo systemctl enable backup.timer

# Start timer
sudo systemctl start backup.timer

# List timers
systemctl list-timers

# Check timer status
systemctl status backup.timer
```

---

## Module 5: Storage Management (Day 10)

### 5.1 Disk Partitioning

#### Viewing Disks and Partitions
```bash
# List block devices
lsblk

# List with filesystem info
lsblk -f

# Show disk information
sudo fdisk -l

# Show partition table
sudo parted -l

# Show disk usage by partition
df -h
```

#### fdisk Command (MBR Partitioning)
```bash
# Start fdisk for a disk
sudo fdisk /dev/sdb

# Commands within fdisk:
# m - help
# p - print partition table
# n - new partition
# d - delete partition
# t - change partition type
# w - write changes and exit
# q - quit without saving

# Example: Create new partition
sudo fdisk /dev/sdb
# Press 'n' for new partition
# Press 'p' for primary
# Enter partition number (1-4)
# Accept default first sector
# Enter last sector or size (+10G)
# Press 'w' to write
```

#### gdisk Command (GPT Partitioning)
```bash
# Start gdisk
sudo gdisk /dev/sdb

# Similar commands to fdisk
# n - new partition
# d - delete partition
# p - print partition table
# w - write and exit
# q - quit without saving
```

#### parted Command (Both MBR and GPT)
```bash
# Interactive mode
sudo parted /dev/sdb

# Create GPT partition table
sudo parted /dev/sdb mklabel gpt

# Create MBR partition table
sudo parted /dev/sdb mklabel msdos

# Create partition
sudo parted /dev/sdb mkpart primary ext4 0% 50%

# Delete partition
sudo parted /dev/sdb rm 1

# Resize partition
sudo parted /dev/sdb resizepart 1 100%

# Print partition table
sudo parted /dev/sdb print
```

### 5.2 Filesystem Management

#### Creating Filesystems
```bash
# ext4 filesystem
sudo mkfs.ext4 /dev/sdb1

# ext4 with label
sudo mkfs.ext4 -L mydata /dev/sdb1

# XFS filesystem
sudo mkfs.xfs /dev/sdb1

# Btrfs filesystem
sudo mkfs.btrfs /dev/sdb1

# FAT32 filesystem
sudo mkfs.vfat -F 32 /dev/sdb1

# NTFS filesystem
sudo mkfs.ntfs /dev/sdb1
```

#### Mounting Filesystems
```bash
# Create mount point
sudo mkdir /mnt/mydata

# Mount filesystem
sudo mount /dev/sdb1 /mnt/mydata

# Mount with specific options
sudo mount -o rw,noexec /dev/sdb1 /mnt/mydata

# Mount by UUID
sudo mount UUID=xxxx-xxxx /mnt/mydata

# Mount by label
sudo mount LABEL=mydata /mnt/mydata

# Unmount
sudo umount /mnt/mydata

# Force unmount
sudo umount -f /mnt/mydata

# Lazy unmount
sudo umount -l /mnt/mydata

# Show mounted filesystems
mount | grep /dev/sdb1
```

#### /etc/fstab Configuration
```bash
# Edit fstab
sudo nano /etc/fstab

# Format:
# <device> <mount_point> <type> <options> <dump> <pass>

# Examples:
UUID=xxxx-xxxx  /mnt/mydata  ext4  defaults  0  2
/dev/sdb1       /mnt/backup  ext4  defaults,noatime  0  2
LABEL=mydata    /mnt/data    ext4  defaults  0  2

# Mount all entries in fstab
sudo mount -a

# Test fstab
sudo findmnt --verify
```

#### Common Mount Options
```
defaults    - rw, suid, dev, exec, auto, nouser, async
rw          - Read-write
ro          - Read-only
noexec      - Prevent execution of binaries
nosuid      - Ignore SUID/SGID bits
nodev       - Ignore device files
noatime     - Don't update access times (performance)
user        - Allow normal users to mount
auto        - Mount at boot
```

### 5.3 Swap Space Management

#### Creating Swap
```bash
# Create swap partition
sudo mkswap /dev/sdb2

# Enable swap
sudo swapon /dev/sdb2

# Create swap file
sudo fallocate -l 2G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile

# Add to fstab for persistence
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab

# Show swap usage
swapon --show
free -h

# Disable swap
sudo swapoff /dev/sdb2
sudo swapoff /swapfile

# Set swappiness (0-100, lower = less swap)
sudo sysctl vm.swappiness=10
echo 'vm.swappiness=10' | sudo tee -a /etc/sysctl.conf
```

### 5.4 Logical Volume Management (LVM)

#### LVM Concepts
- **PV (Physical Volume)**: Physical disk or partition
- **VG (Volume Group)**: Pool of physical volumes
- **LV (Logical Volume)**: Virtual partition from volume group
- **PE (Physical Extent)**: Smallest unit of storage

#### Creating LVM
```bash
# Install LVM tools
sudo apt install lvm2

# Create physical volume
sudo pvcreate /dev/sdb1
sudo pvcreate /dev/sdc1

# Display physical volumes
sudo pvdisplay
sudo pvs

# Create volume group
sudo vgcreate myvg /dev/sdb1 /dev/sdc1

# Display volume groups
sudo vgdisplay
sudo vgs

# Create logical volume (10GB)
sudo lvcreate -L 10G -n mylv myvg

# Create logical volume (50% of VG)
sudo lvcreate -l 50%VG -n mylv myvg

# Display logical volumes
sudo lvdisplay
sudo lvs

# Create filesystem on LV
sudo mkfs.ext4 /dev/myvg/mylv

# Mount logical volume
sudo mkdir /mnt/mylv
sudo mount /dev/myvg/mylv /mnt/mylv
```

#### Extending LVM
```bash
# Extend volume group
sudo vgextend myvg /dev/sdd1

# Extend logical volume
sudo lvextend -L +5G /dev/myvg/mylv

# Extend to use all free space
sudo lvextend -l +100%FREE /dev/myvg/mylv

# Resize filesystem (ext4)
sudo resize2fs /dev/myvg/mylv

# Resize filesystem (XFS)
sudo xfs_growfs /mnt/mylv

# Extend LV and filesystem in one command
sudo lvextend -r -L +5G /dev/myvg/mylv
```

#### Reducing LVM (ext4 only)
```bash
# Unmount filesystem
sudo umount /mnt/mylv

# Check filesystem
sudo e2fsck -f /dev/myvg/mylv

# Resize filesystem first
sudo resize2fs /dev/myvg/mylv 8G

# Reduce logical volume
sudo lvreduce -L 8G /dev/myvg/mylv

# Remount
sudo mount /dev/myvg/mylv /mnt/mylv
```

#### LVM Snapshots
```bash
# Create snapshot (2GB size)
sudo lvcreate -L 2G -s -n mylv_snapshot /dev/myvg/mylv

# Mount snapshot
sudo mkdir /mnt/snapshot
sudo mount /dev/myvg/mylv_snapshot /mnt/snapshot

# Remove snapshot
sudo umount /mnt/snapshot
sudo lvremove /dev/myvg/mylv_snapshot
```

#### Removing LVM
```bash
# Unmount logical volume
sudo umount /mnt/mylv

# Remove logical volume
sudo lvremove /dev/myvg/mylv

# Remove volume group
sudo vgremove myvg

# Remove physical volume
sudo pvremove /dev/sdb1
```

---

## Practical Exercises

### Exercise 1: Package Management Mastery
1. Update your system packages
2. Install nginx, htop, and net-tools
3. Search for all packages related to "python"
4. Show information about the nginx package
5. Remove net-tools and clean the cache
6. Add a PPA repository (Ubuntu) or EPEL (RHEL)

### Exercise 2: Process Management Challenge
1. Start a long-running process in the background
2. Find its PID using multiple methods (ps, pgrep, top)
3. Change its priority using renice
4. Suspend the process and resume it
5. Create a script that monitors and kills processes using >90% CPU

### Exercise 3: System Monitoring Dashboard
Create a monitoring script that displays:
1. System uptime and load average
2. CPU usage per core
3. Memory usage (used, free, available)
4. Top 5 processes by CPU usage
5. Top 5 processes by memory usage
6. Disk usage for all mounted filesystems
7. Network connections count
8. Recent system errors from logs

### Exercise 4: Custom systemd Service
1. Create a Python/Bash script that logs system stats every minute
2. Create a systemd service for this script
3. Enable and start the service
4. Verify it's running and check logs
5. Create a timer to run a backup script daily at 2 AM

### Exercise 5: Storage Management Lab
1. Create a new 20GB virtual disk
2. Partition it into two 10GB partitions
3. Create ext4 filesystem on first partition
4. Mount it and add to /etc/fstab
5. Create LVM on second partition
6. Create two logical volumes (5GB each)
7. Create filesystems and mount them
8. Test extending one logical volume
9. Create an LVM snapshot
10. Create and configure 2GB swap space

---

## Daily Challenges

### Day 6 Challenge: Package Management
Write a script that:
- Checks for available updates
- Lists packages that will be updated
- Creates a backup list of currently installed packages
- Performs the update
- Logs all actions with timestamps

### Day 7 Challenge: Process Monitor
Create a process monitoring tool that:
- Monitors specific processes by name
- Alerts if process is not running
- Restarts the process automatically
- Logs all actions
- Sends email notifications (optional)

### Day 8 Challenge: System Health Check
Build a comprehensive system health check script:
- CPU usage threshold alert
- Memory usage threshold alert
- Disk space threshold alert
- High load average detection
- Failed services detection
- Generate HTML report

### Day 9 Challenge: Service Deployment
Deploy a web application as a systemd service:
- Create the service unit file
- Configure automatic restart on failure
- Set up logging
- Create a timer for periodic tasks
- Implement graceful shutdown

### Day 10 Challenge: Storage Automation
Create an automated storage management system:
- Monitor disk usage
- Automatically extend LVM when threshold reached
- Clean old log files
- Create daily snapshots
- Rotate snapshots (keep last 7 days)

---

## Troubleshooting Common Issues

### Package Management Issues
```bash
# Fix broken packages (Debian/Ubuntu)
sudo apt --fix-broken install
sudo dpkg --configure -a

# Clear package cache
sudo apt clean
sudo apt autoclean

# Fix locked database
sudo rm /var/lib/apt/lists/lock
sudo rm /var/cache/apt/archives/lock
sudo rm /var/lib/dpkg/lock*

# Reconfigure packages
sudo dpkg-reconfigure -a
```

### Process Issues
```bash
# Find zombie processes
ps aux | grep 'Z'

# Kill all processes of a user
sudo pkill -u username

# Find process using a file
lsof /path/to/file

# Find process using a port
sudo lsof -i :80
sudo netstat -tlnp | grep :80

# Trace system calls
strace -p PID
```

### Service Issues
```bash
# Check service dependencies
systemctl list-dependencies service_name

# Check why service failed
systemctl status service_name
journalctl -xe -u service_name

# Reset failed state
sudo systemctl reset-failed

# Check service startup time
systemd-analyze blame
```

### Storage Issues
```bash
# Check filesystem errors
sudo fsck /dev/sdb1

# Repair filesystem
sudo fsck -y /dev/sdb1

# Check disk health
sudo smartctl -a /dev/sda

# Find large files
sudo find / -type f -size +100M -exec ls -lh {} \;

# Check inode usage
df -i

# Force unmount busy filesystem
sudo fuser -km /mnt/point
sudo umount -f /mnt/point
```

---

## Best Practices

### Package Management
1. Always update package lists before installing
2. Use package manager instead of compiling from source
3. Keep system regularly updated
4. Review updates before applying in production
5. Maintain list of installed packages for disaster recovery

### Process Management
1. Use appropriate signals (SIGTERM before SIGKILL)
2. Monitor resource usage regularly
3. Set appropriate nice values for background tasks
4. Use process managers (systemd, supervisor) for services
5. Implement proper logging for all services

### System Monitoring
1. Set up automated monitoring and alerting
2. Establish baseline metrics
3. Monitor trends, not just current values
4. Keep historical data for analysis
5. Document normal behavior patterns

### Service Management
1. Use systemd for service management
2. Enable services explicitly, don't assume defaults
3. Configure proper restart policies
4. Implement health checks
5. Use timers instead of cron when possible

### Storage Management
1. Always backup data before partitioning
2. Use LVM for flexibility
3. Monitor disk space proactively
4. Implement regular backup strategy
5. Test restore procedures regularly
6. Use appropriate filesystem for use case
7. Set up monitoring for disk health (SMART)

---

## Additional Resources

### Documentation
- [systemd Documentation](https://www.freedesktop.org/wiki/Software/systemd/)
- [LVM HOWTO](https://tldp.org/HOWTO/LVM-HOWTO/)
- [Linux System Administrator's Guide](https://tldp.org/LDP/sag/html/)

### Tools
- [htop](https://htop.dev/) - Interactive process viewer
- [glances](https://nicolargo.github.io/glances/) - System monitoring tool
- [ncdu](https://dev.yorhel.nl/ncdu) - Disk usage analyzer

### Books
- "UNIX and Linux System Administration Handbook"
- "The Linux Command Line" by William Shotts
- "Linux Performance" by Brendan Gregg

### Online Platforms
- [Linux Journey](https://linuxjourney.com/)
- [OverTheWire: Bandit](https://overthewire.org/wargames/bandit/)
- [Ops School Curriculum](https://www.opsschool.org/)

---

## Summary and Next Steps

After completing this System Administration module, you should be proficient in:

✓ Managing packages across different Linux distributions
✓ Controlling and monitoring processes effectively
✓ Analyzing system performance and resource usage
✓ Managing services with systemd
✓ Configuring and managing storage (partitions, LVM, filesystems)
✓ Troubleshooting common system administration issues
✓ Implementing automation and best practices

**Next Module**: Proceed to **Day 11-15: Networking and Services** where you'll learn about network configuration, DNS, firewalls, and essential network services.

**Certification Path**: Consider pursuing:
- Linux Professional Institute Certification (LPIC-1)
- Red Hat Certified System Administrator (RHCSA)
- CompTIA Linux+

Keep practicing these skills in your lab environment and gradually increase complexity as you gain confidence!
