# Day 3: Networking and Services

## Module 1: Linux Networking Fundamentals
### 1.1 Network Configuration
- Network interfaces and configuration files
- IP addressing and subnetting
- Network configuration tools (ip, ifconfig, nmcli)
- Setting static and dynamic IP addresses
- Network troubleshooting (ping, traceroute, mtr)

### 1.2 DNS and Hostname Resolution
- Understanding DNS
- /etc/hosts file
- /etc/resolv.conf
- dig and nslookup
- Testing DNS resolution

## Module 2: Network Services
### 2.1 SSH (Secure Shell)
- SSH protocol overview
- SSH server configuration (/etc/ssh/sshd_config)
- Key-based authentication
- SSH port forwarding
- SCP and SFTP

### 2.2 Web Servers
- Apache vs Nginx
- Installing and configuring Apache/Nginx
- Virtual hosts
- SSL/TLS configuration (Let's Encrypt)
- Basic security hardening

### 2.3 File Sharing Services
- Samba (Windows file sharing)
- NFS (Network File System)
- FTP vs SFTP
- Setting up a basic file server

## Module 3: Network Security
### 3.1 Firewall Configuration
- Understanding iptables
- Basic firewall rules
- Common firewall configurations
- Saving and restoring rules

### 3.2 Network Monitoring
- Netstat and ss commands
- Network traffic analysis (tcpdump, Wireshark)
- Network security monitoring
- Intrusion detection basics

## Practical Exercises
1. **Network Configuration**
   - Configure a static IP address
   - Set up a basic network bridge
   - Troubleshoot network connectivity

2. **SSH Server**
   - Set up an SSH server
   - Configure key-based authentication
   - Set up SSH tunnels

3. **Web Server**
   - Install and configure Apache/Nginx
   - Set up a virtual host
   - Secure with Let's Encrypt

4. **Firewall**
   - Configure basic firewall rules
   - Allow/block specific services
   - Set up port forwarding

## Daily Challenge
Create a secure file sharing solution that:
1. Uses SFTP for secure file transfers
2. Implements user authentication
3. Restricts users to their home directories
4. Logs all access attempts

## Additional Resources
- [Linux Network Administrator's Guide](https://tldp.org/LDP/nag2/)
- [SSH Mastery](https://www.networkworld.com/article/3026667/ssh-mastery.html)
- [Nginx Documentation](https://nginx.org/en/docs/)

## Next Steps
Tomorrow we'll dive into system security hardening, including user privileges, file system security, and security best practices.
