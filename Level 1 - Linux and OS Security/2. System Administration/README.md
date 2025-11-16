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
