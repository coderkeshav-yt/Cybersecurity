# Level 1: Linux and Foundational Operating System Security

## Introduction
Mastering Linux and operating system fundamentals is crucial for any cybersecurity professional. This level establishes the core knowledge required for both offensive and defensive security operations.

## Core Topics

### Linux File System Hierarchy (FHS)
- **/bin**: Essential user command binaries
- **/etc**: Host-specific system configuration
- **/home**: User home directories
- **/var**: Variable data files
- **/tmp**: Temporary files
- **/usr**: Secondary hierarchy for read-only user data
- **/opt**: Add-on application software packages

### User and Group Management
#### User Management
```bash
# Add a new user
sudo useradd username

# Set password
sudo passwd username

# Modify user
sudo usermod [options] username

# Delete user
sudo userdel username
```

#### Group Management
```bash
# Create a group
sudo groupadd groupname

# Add user to group
sudo usermod -aG groupname username

# View group members
getent group groupname
```

### File Permissions
#### Understanding Permissions
```
rwx r-x r--
│││  │││  │││
│││  │││  ││└── Others: Read only
│││  │││  │└─── Group: Read and execute
│││  │││  └──── Owner: Read, write, execute
│││  ││└────── Special: Sticky bit
│││  │└─────── Special: SetGID
│└┴──┴─────── Special: SetUID
```

#### Changing Permissions
```bash
# Symbolic notation
chmod u=rwx,g=rx,o=r file

# Numeric notation
chmod 754 file
```

### Essential Networking
#### Basic Commands
```bash
# Network interfaces
ip addr show

# Network connections
ss -tuln

# DNS lookups
dig example.com
nslookup example.com

# Network troubleshooting
ping 8.8.8.8
traceroute google.com
mtr google.com
```

### Bash Scripting Basics
#### Example Script
```bash
#!/bin/bash

# Simple system monitor script

# Get system information
HOSTNAME=$(hostname)
MEMORY=$(free -m | awk '/Mem:/ {print $3"MB / "$2"MB"}')
DISK=$(df -h / | awk 'NR==2 {print $3" / "$2}')
UPTIME=$(uptime -p)

# Display information
echo "===== System Monitor ====="
echo "Hostname: $HOSTNAME"
echo "Memory Usage: $MEMORY"
echo "Disk Usage: $DISK"
echo "Uptime: $UPTIME"
echo "========================="
```

### Process Management
#### Common Commands
```bash
# List processes
ps aux

# Interactive process viewer
top
htop

# Kill processes
kill <PID>
pkill <process_name>
killall <process_name>

# View system logs
journalctl -xe
dmesg | tail
```

## Practical Exercises

### Exercise 1: Linux VM Setup
1. Download and install VirtualBox
2. Create a new Ubuntu Server VM
3. Complete the installation process
4. Log in and update the system

### Exercise 2: User and Permission Management
1. Create three users: admin, developer, and guest
2. Create a shared directory with specific permissions:
   - admin: full access
   - developer: read/write access
   - guest: read-only access
3. Test access with each user

### Exercise 3: Network Configuration
1. Configure a static IP address
2. Set up SSH access
3. Test network connectivity
4. Capture network traffic with tcpdump

### Exercise 4: System Monitoring Script
1. Create a bash script that monitors:
   - CPU usage
   - Memory usage
   - Disk space
   - Running services
2. Schedule it to run every 5 minutes using cron

## Additional Resources
- [Linux Journey](https://linuxjourney.com/)
- [The Linux Command Line](https://linuxcommand.org/tlcl.php)
- [Bash Scripting Tutorial](https://ryanstutorials.net/bash-scripting-tutorial/)
- [Linux System Administrator's Guide](https://tldp.org/LDP/sag/html/)

## Next Steps
After completing this level, you should be comfortable with:
- Navigating and managing the Linux filesystem
- User and permission management
- Basic networking and troubleshooting
- Writing simple bash scripts
- Process and system monitoring

Proceed to [Level 2: Attack Methodologies](../Level%202%20-%20Attack%20Methodologies/README.md) when ready.
