# Day 4: Security Hardening

## Module 1: System Hardening
### 1.1 Security Best Practices
- Principle of least privilege
- Regular system updates
- Disabling unnecessary services
- Secure SSH configuration
- PAM (Pluggable Authentication Modules)

### 1.2 File System Security
- File permissions and ownership
- Sticky bit, SUID, and SGID
- File attributes (chattr, lsattr)
- Filesystem encryption (LUKS)
- Audit file system changes (auditd)

## Module 2: User and Authentication Security
### 2.1 Password Policies
- Strong password requirements
- Password aging
- Failed login attempts
- PAM configuration
- Two-factor authentication

### 2.2 Privilege Escalation
- The sudo command
- Sudoers file configuration
- Restricting root access
- Monitoring privileged access
- Sudo logging

## Module 3: Network Security
### 3.1 Firewall Configuration
- UFW (Uncomplicated Firewall)
- Firewalld
- Common firewall rules
- Port security
- Rate limiting

### 3.2 Intrusion Detection and Prevention
- Fail2ban
- PortSentry
- Rootkit hunters (rkhunter, chkrootkit)
- File integrity checkers (AIDE, Tripwire)
- Security auditing (Lynis)

## Module 4: Security Tools
### 4.1 Vulnerability Scanning
- Nmap network scanning
- OpenVAS
- Nikto
- WPScan
- Lynis system auditing

### 4.2 Log Management
- Centralized logging with rsyslog
- Log rotation
- Log analysis tools
- SIEM basics
- Security event monitoring

## Practical Exercises
1. **System Hardening**
   - Review and harden SSH configuration
   - Set up automatic security updates
   - Configure and test PAM modules

2. **User Security**
   - Implement password policies
   - Configure sudo access
   - Set up basic two-factor authentication

3. **Network Security**
   - Configure UFW/firewalld
   - Set up Fail2ban
   - Perform a basic vulnerability scan

4. **Security Auditing**
   - Run a system audit with Lynis
   - Check for rootkits
   - Review and analyze system logs

## Daily Challenge
Harden a Linux server by:
1. Implementing strict firewall rules
2. Setting up intrusion detection
3. Configuring system auditing
4. Creating a security baseline
5. Documenting all changes made

## Additional Resources
- [CIS Benchmarks](https://www.cisecurity.org/cis-benchmarks/)
- [Linux Security Hardening Guide](https://github.com/trimstray/the-practical-linux-hardening-guide)
- [OpenSCAP Security Guide](https://www.open-scap.org/security-policies/)

## Next Steps
Tomorrow we'll explore advanced topics and practical labs to test your Linux security skills in real-world scenarios.
