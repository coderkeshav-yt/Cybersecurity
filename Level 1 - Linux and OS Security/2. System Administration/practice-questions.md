# Day 6-10: System Administration - Practice Questions & Answers

## Section 1: Package Management (10 Questions)

### Question 1
What command updates the package index on Debian/Ubuntu systems?

**Answer:**
```bash
sudo apt update
```

---

### Question 2
How do you install a package named "nginx" on a RHEL/CentOS system using yum?

**Answer:**
```bash
sudo yum install nginx
```

---

### Question 3
What's the difference between `apt remove` and `apt purge`?

**Answer:**
- `apt remove` - Removes the package but keeps configuration files
- `apt purge` - Removes the package AND all configuration files

---

### Question 4
How do you search for all packages containing the word "python" on Ubuntu?

**Answer:**
```bash
apt search python
```

---

### Question 5
What command removes unused dependencies on Debian/Ubuntu systems?

**Answer:**
```bash
sudo apt autoremove
```

---

### Question 6
How do you list all installed packages on a Debian/Ubuntu system?

**Answer:**
```bash
apt list --installed
# or
dpkg -l
```

---

### Question 7
What command shows detailed information about a package named "apache2"?

**Answer:**
```bash
apt show apache2
# or
dpkg -s apache2
```

---

### Question 8
How do you prevent a package from being updated (hold it) on Ubuntu?

**Answer:**
```bash
sudo apt-mark hold package_name
```

---

### Question 9
What command adds a PPA repository on Ubuntu?

**Answer:**
```bash
sudo add-apt-repository ppa:repository_name
```

---

### Question 10
How do you clean the package cache on Debian/Ubuntu systems?

**Answer:**
```bash
sudo apt clean
# or for partial cleanup
sudo apt autoclean
```

---

## Section 2: Process Management (10 Questions)

### Question 11
What command shows all running processes with detailed information?

**Answer:**
```bash
ps aux
```

---

### Question 12
How do you find the PID of a process named "nginx"?

**Answer:**
```bash
pgrep nginx
# or
pidof nginx
# or
ps aux | grep nginx
```

---

### Question 13
What signal number is used for a graceful termination (SIGTERM)?

**Answer:**
Signal 15 (SIGTERM)
```bash
kill -15 PID
# or simply
kill PID
```

---

### Question 14
How do you forcefully kill a process with PID 1234?

**Answer:**
```bash
kill -9 1234
# or
kill -SIGKILL 1234
```

---

### Question 15
What command kills all processes with the name "firefox"?

**Answer:**
```bash
killall firefox
# or
pkill firefox
```

---

### Question 16
How do you start a process with the lowest priority?

**Answer:**
```bash
nice -n 19 command
```

---

### Question 17
How do you change the priority of a running process with PID 5678 to -10?

**Answer:**
```bash
sudo renice -n -10 -p 5678
```

---

### Question 18
What command shows processes in a tree structure?

**Answer:**
```bash
pstree
# or with PIDs
pstree -p
```

---

### Question 19
How do you run a command in the background?

**Answer:**
```bash
command &
```

---

### Question 20
What command lists all background jobs in the current shell?

**Answer:**
```bash
jobs
```

---

## Section 3: System Monitoring (10 Questions)

### Question 21
What command shows memory usage in human-readable format?

**Answer:**
```bash
free -h
```

---

### Question 22
How do you display real-time CPU and memory usage by process?

**Answer:**
```bash
top
# or for better interface
htop
```

---

### Question 23
What command shows disk usage for all mounted filesystems in human-readable format?

**Answer:**
```bash
df -h
```

---

### Question 24
How do you find the size of a directory named "/var/log"?

**Answer:**
```bash
du -sh /var/log
```

---

### Question 25
What command shows the system uptime and load average?

**Answer:**
```bash
uptime
```

---

### Question 26
How do you display the top 10 largest directories in /home?

**Answer:**
```bash
du -h /home | sort -rh | head -10
```

---

### Question 27
What command shows all listening TCP ports with process information?

**Answer:**
```bash
sudo netstat -tlnp
# or using modern ss command
sudo ss -tlnp
```

---

### Question 28
How do you view kernel messages?

**Answer:**
```bash
dmesg
# or with human-readable timestamps
dmesg -T
```

---

### Question 29
What command shows I/O statistics for all disks?

**Answer:**
```bash
iostat
# or with extended statistics
iostat -x
```

---

### Question 30
How do you follow the system log in real-time on systemd systems?

**Answer:**
```bash
journalctl -f
# or for specific service
journalctl -u service_name -f
```

---

## Section 4: Service Management with systemd (10 Questions)

### Question 31
What command starts a service named "nginx"?

**Answer:**
```bash
sudo systemctl start nginx
```

---

### Question 32
How do you enable a service to start automatically at boot?

**Answer:**
```bash
sudo systemctl enable service_name
```

---

### Question 33
What command shows the status of a service named "ssh"?

**Answer:**
```bash
systemctl status ssh
```

---

### Question 34
How do you restart a service named "apache2"?

**Answer:**
```bash
sudo systemctl restart apache2
```

---

### Question 35
What's the difference between `systemctl reload` and `systemctl restart`?

**Answer:**
- `reload` - Reloads configuration without stopping the service (if supported)
- `restart` - Stops and then starts the service completely

---

### Question 36
How do you list all running services?

**Answer:**
```bash
systemctl list-units --type=service --state=running
```

---

### Question 37
What command shows all failed services?

**Answer:**
```bash
systemctl list-units --state=failed
```

---

### Question 38
How do you prevent a service from being started (mask it)?

**Answer:**
```bash
sudo systemctl mask service_name
```

---

### Question 39
What command reloads systemd configuration after creating a new service file?

**Answer:**
```bash
sudo systemctl daemon-reload
```

---

### Question 40
How do you view logs for a specific service named "nginx"?

**Answer:**
```bash
journalctl -u nginx
# or to follow in real-time
journalctl -u nginx -f
```

---

## Section 5: Storage Management (10 Questions)

### Question 41
What command lists all block devices?

**Answer:**
```bash
lsblk
# or with filesystem information
lsblk -f
```

---

### Question 42
How do you create an ext4 filesystem on /dev/sdb1?

**Answer:**
```bash
sudo mkfs.ext4 /dev/sdb1
```

---

### Question 43
What command mounts /dev/sdb1 to /mnt/data?

**Answer:**
```bash
sudo mount /dev/sdb1 /mnt/data
```

---

### Question 44
Where is the configuration file for automatic filesystem mounting at boot?

**Answer:**
`/etc/fstab`

---

### Question 45
What command unmounts a filesystem mounted at /mnt/data?

**Answer:**
```bash
sudo umount /mnt/data
```

---

### Question 46
How do you create a 2GB swap file at /swapfile?

**Answer:**
```bash
sudo fallocate -l 2G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
```

---

### Question 47
What command shows current swap usage?

**Answer:**
```bash
swapon --show
# or
free -h
```

---

### Question 48
How do you create a physical volume for LVM on /dev/sdb1?

**Answer:**
```bash
sudo pvcreate /dev/sdb1
```

---

### Question 49
What command creates a volume group named "vg_data" from /dev/sdb1 and /dev/sdc1?

**Answer:**
```bash
sudo vgcreate vg_data /dev/sdb1 /dev/sdc1
```

---

### Question 50
How do you create a 10GB logical volume named "lv_home" in volume group "vg_data"?

**Answer:**
```bash
sudo lvcreate -L 10G -n lv_home vg_data
```

---

## Bonus Section: Advanced Scenarios (10 Questions)

### Question 51
How do you find which process is using port 80?

**Answer:**
```bash
sudo lsof -i :80
# or
sudo netstat -tlnp | grep :80
# or
sudo ss -tlnp | grep :80
```

---

### Question 52
What command shows the boot time of each service?

**Answer:**
```bash
systemd-analyze blame
```

---

### Question 53
How do you extend a logical volume by 5GB?

**Answer:**
```bash
sudo lvextend -L +5G /dev/vg_name/lv_name
# Then resize the filesystem
sudo resize2fs /dev/vg_name/lv_name  # for ext4
# or
sudo xfs_growfs /mount/point  # for XFS
```

---

### Question 54
What command shows all open files by a specific process with PID 1234?

**Answer:**
```bash
lsof -p 1234
```

---

### Question 55
How do you create an LVM snapshot of a logical volume?

**Answer:**
```bash
sudo lvcreate -L 2G -s -n lv_snapshot /dev/vg_name/lv_name
```

---

### Question 56
What command shows system logs from the last boot only?

**Answer:**
```bash
journalctl -b
# or for previous boot
journalctl -b -1
```

---

### Question 57
How do you check filesystem errors on /dev/sdb1 (must be unmounted)?

**Answer:**
```bash
sudo umount /dev/sdb1
sudo fsck /dev/sdb1
# or to automatically fix errors
sudo fsck -y /dev/sdb1
```

---

### Question 58
What command shows which files a running process has open?

**Answer:**
```bash
lsof -p PID
# or by process name
lsof -c process_name
```

---

### Question 59
How do you set the system swappiness value to 10?

**Answer:**
```bash
sudo sysctl vm.swappiness=10
# To make it persistent
echo 'vm.swappiness=10' | sudo tee -a /etc/sysctl.conf
```

---

### Question 60
What command shows the dependency tree of a service?

**Answer:**
```bash
systemctl list-dependencies service_name
```

---

## Practical Scenarios (10 Questions)

### Scenario 1
Your system is running slow. What commands would you use to identify the problem?

**Answer:**
```bash
# Check load average
uptime

# Check CPU usage
top
# or
htop

# Check memory usage
free -h

# Check disk I/O
iostat -x 2

# Check disk space
df -h

# Find resource-hungry processes
ps aux --sort=-%cpu | head -10
ps aux --sort=-%mem | head -10
```

---

### Scenario 2
A service keeps failing to start. How do you troubleshoot it?

**Answer:**
```bash
# Check service status
systemctl status service_name

# View detailed logs
journalctl -xe -u service_name

# Check service configuration
systemctl cat service_name

# Check dependencies
systemctl list-dependencies service_name

# Try starting manually to see errors
sudo /path/to/service/binary
```

---

### Scenario 3
You need to free up disk space. What steps would you take?

**Answer:**
```bash
# Find large files
sudo find / -type f -size +100M -exec ls -lh {} \;

# Check directory sizes
du -h / | sort -rh | head -20

# Clean package cache
sudo apt clean  # Debian/Ubuntu
sudo yum clean all  # RHEL/CentOS

# Remove old logs
sudo journalctl --vacuum-time=7d

# Find and remove old kernels (Ubuntu)
sudo apt autoremove

# Check for large log files
du -sh /var/log/*
```

---

### Scenario 4
How do you create a systemd service for a custom application?

**Answer:**
```bash
# Create service file
sudo nano /etc/systemd/system/myapp.service

# Add content:
[Unit]
Description=My Application
After=network.target

[Service]
Type=simple
User=myuser
WorkingDirectory=/opt/myapp
ExecStart=/usr/bin/python3 /opt/myapp/app.py
Restart=always

[Install]
WantedBy=multi-user.target

# Reload systemd
sudo systemctl daemon-reload

# Enable and start
sudo systemctl enable --now myapp.service

# Check status
systemctl status myapp.service
```

---

### Scenario 5
You need to add a new 50GB disk and make it available as /data. What are the steps?

**Answer:**
```bash
# 1. Identify the new disk
lsblk

# 2. Create partition (assuming /dev/sdb)
sudo fdisk /dev/sdb
# Press 'n' for new, 'p' for primary, accept defaults, 'w' to write

# 3. Create filesystem
sudo mkfs.ext4 /dev/sdb1

# 4. Create mount point
sudo mkdir /data

# 5. Mount temporarily
sudo mount /dev/sdb1 /data

# 6. Get UUID
sudo blkid /dev/sdb1

# 7. Add to /etc/fstab for persistence
echo 'UUID=xxxx-xxxx /data ext4 defaults 0 2' | sudo tee -a /etc/fstab

# 8. Test fstab
sudo mount -a
```

---

### Scenario 6
A process is consuming 100% CPU. How do you identify and handle it?

**Answer:**
```bash
# 1. Identify the process
top
# Press 'P' to sort by CPU

# or
ps aux --sort=-%cpu | head -5

# 2. Get more information
ps -p PID -f
lsof -p PID

# 3. Check what it's doing
strace -p PID

# 4. Try graceful termination
kill PID

# 5. If it doesn't stop, force kill
kill -9 PID

# 6. Check logs to understand why
journalctl -xe
```

---

### Scenario 7
How do you set up automatic log rotation for a custom application?

**Answer:**
```bash
# Create logrotate configuration
sudo nano /etc/logrotate.d/myapp

# Add content:
/var/log/myapp/*.log {
    daily
    rotate 7
    compress
    delaycompress
    missingok
    notifempty
    create 0640 myuser mygroup
    sharedscripts
    postrotate
        systemctl reload myapp > /dev/null 2>&1 || true
    endscript
}

# Test configuration
sudo logrotate -d /etc/logrotate.d/myapp

# Force rotation (for testing)
sudo logrotate -f /etc/logrotate.d/myapp
```

---

### Scenario 8
Your system won't boot due to /etc/fstab errors. How do you fix it?

**Answer:**
```bash
# 1. Boot into recovery mode or live USB

# 2. Mount root filesystem
sudo mount /dev/sda1 /mnt

# 3. Edit fstab
sudo nano /mnt/etc/fstab

# 4. Comment out problematic line with #
# UUID=xxxx /data ext4 defaults 0 2

# 5. Save and reboot
sudo reboot

# 6. After boot, fix the issue properly
sudo blkid  # Get correct UUID
sudo nano /etc/fstab  # Fix the entry
sudo mount -a  # Test
```

---

### Scenario 9
How do you migrate data from a regular partition to LVM?

**Answer:**
```bash
# 1. Backup data
sudo rsync -av /old/mount/ /backup/

# 2. Unmount old partition
sudo umount /old/mount

# 3. Create physical volume
sudo pvcreate /dev/sdb1

# 4. Create volume group
sudo vgcreate vg_data /dev/sdb1

# 5. Create logical volume
sudo lvcreate -L 20G -n lv_data vg_data

# 6. Create filesystem
sudo mkfs.ext4 /dev/vg_data/lv_data

# 7. Mount new LV
sudo mount /dev/vg_data/lv_data /new/mount

# 8. Restore data
sudo rsync -av /backup/ /new/mount/

# 9. Update /etc/fstab
/dev/vg_data/lv_data /new/mount ext4 defaults 0 2
```

---

### Scenario 10
How do you monitor and limit resource usage for a specific service?

**Answer:**
```bash
# Edit service file
sudo systemctl edit service_name

# Add resource limits:
[Service]
CPUQuota=50%
MemoryLimit=512M
TasksMax=100

# Reload and restart
sudo systemctl daemon-reload
sudo systemctl restart service_name

# Monitor resource usage
systemd-cgtop

# Check specific service
systemctl status service_name
```

---

## Summary

**Total Questions: 60**

- Section 1: Package Management (10 questions)
- Section 2: Process Management (10 questions)
- Section 3: System Monitoring (10 questions)
- Section 4: Service Management with systemd (10 questions)
- Section 5: Storage Management (10 questions)
- Bonus Section: Advanced Scenarios (10 questions)
- Practical Scenarios (10 questions)

**Practice Tips:**
1. Set up a virtual machine for safe practice
2. Try each command multiple times with different options
3. Break things intentionally and learn to fix them
4. Create your own scenarios based on real-world needs
5. Use `man command` to explore all options
6. Practice troubleshooting without looking at answers first
7. Time yourself on practical scenarios
8. Document your own solutions and create a personal reference

**Scoring Guide:**
- 55-60 correct: Expert! Ready for advanced topics
- 45-54 correct: Very Good! Minor review needed
- 35-44 correct: Good! Practice more scenarios
- 25-34 correct: Fair. Review concepts and practice
- Below 25: Review the material thoroughly and retry

**Study Schedule:**
- **Day 6**: Package Management (Questions 1-10)
- **Day 7**: Process Management (Questions 11-20)
- **Day 8**: System Monitoring (Questions 21-30)
- **Day 9**: Service Management (Questions 31-40)
- **Day 10**: Storage Management (Questions 41-50)
- **Review Day**: Advanced & Practical Scenarios (Questions 51-60)

**Next Steps:**
- Complete all 60 questions
- Practice each command in your lab environment
- Create your own practice scenarios
- Move on to Day 11-15: Networking and Services
- Consider setting up a home lab with multiple VMs
- Practice disaster recovery scenarios
- Explore automation with shell scripts

**Certification Preparation:**
These questions align with:
- LPIC-1 (Linux Professional Institute Certification)
- RHCSA (Red Hat Certified System Administrator)
- CompTIA Linux+
- Ubuntu Certified Professional

**Additional Practice Resources:**
- Set up a multi-VM environment
- Practice on different distributions (Ubuntu, CentOS, Arch)
- Join online communities (r/linuxadmin, r/sysadmin)
- Contribute to open-source projects
- Build a personal server project

Good luck with your System Administration journey!
