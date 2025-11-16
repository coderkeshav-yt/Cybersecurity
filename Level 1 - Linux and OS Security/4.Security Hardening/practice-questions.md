# Day 18-22: Security Hardening - Practice Questions & Answers

## Section 1: User Security and Authentication (10 Questions)

### Question 1
How do you lock a user account?

**Answer:**
```bash
sudo usermod -L username
# or
sudo passwd -l username
```

---

### Question 2
What command sets password expiration to 90 days for a user?

**Answer:**
```bash
sudo chage -M 90 username
```

---

### Question 3
How do you force a user to change password on next login?

**Answer:**
```bash
sudo chage -d 0 username
```

---

### Question 4
What file contains password aging configuration?

**Answer:**
/etc/login.defs

---

### Question 5
How do you view password aging information for a user?

**Answer:**
```bash
sudo chage -l username
```

---

### Question 6
What command edits the sudoers file safely?

**Answer:**
```bash
sudo visudo
```

---

### Question 7
How do you configure password quality requirements on Debian/Ubuntu?

**Answer:**
```bash
# Install package
sudo apt install libpam-pwquality

# Edit configuration
sudo nano /etc/security/pwquality.conf

# Set requirements like:
minlen = 12
dcredit = -1
ucredit = -1
```

---

### Question 8
What command shows failed login attempts for a user?

**Answer:**
```bash
sudo faillock --user username
```

---

### Question 9
How do you reset failed login attempts for a user?

**Answer:**
```bash
sudo faillock --user username --reset
```

---

### Question 10
What is the SSH server configuration file location?

**Answer:**
/etc/ssh/sshd_config

---

## Section 2: File System Security (10 Questions)

### Question 11
How do you find all SUID files on the system?

**Answer:**
```bash
sudo find / -type f -perm -4000 2>/dev/null
```

---

### Question 12
What command makes a file immutable (cannot be modified or deleted)?

**Answer:**
```bash
sudo chattr +i filename
```

---

### Question 13
How do you view file attributes?

**Answer:**
```bash
lsattr filename
```

---

### Question 14
What command removes the immutable attribute from a file?

**Answer:**
```bash
sudo chattr -i filename
```

---

### Question 15
How do you find all world-writable files?

**Answer:**
```bash
find / -xdev -type f -perm -0002 2>/dev/null
```

---

### Question 16
What are the correct permissions for /etc/shadow?

**Answer:**
```bash
640 (rw-r-----)
sudo chmod 640 /etc/shadow
```

---

### Question 17
How do you create an encrypted partition with LUKS?

**Answer:**
```bash
sudo cryptsetup luksFormat /dev/sdb1
```

---

### Question 18
What command opens an encrypted LUKS partition?

**Answer:**
```bash
sudo cryptsetup luksOpen /dev/sdb1 encrypted_volume
```

---

### Question 19
How do you initialize AIDE database?

**Answer:**
```bash
sudo aideinit
# Then move the database
sudo mv /var/lib/aide/aide.db.new /var/lib/aide/aide.db
```

---

### Question 20
What command runs an AIDE integrity check?

**Answer:**
```bash
sudo aide --check
```

---

## Section 3: System Auditing and Logging (10 Questions)

### Question 21
How do you install auditd?

**Answer:**
```bash
sudo apt install auditd audispd-plugins
```

---

### Question 22
What command lists current audit rules?

**Answer:**
```bash
sudo auditctl -l
```

---

### Question 23
How do you add an audit rule to monitor /etc/passwd?

**Answer:**
```bash
sudo auditctl -w /etc/passwd -p wa -k passwd_changes
```

---

### Question 24
What command searches audit logs for a specific key?

**Answer:**
```bash
sudo ausearch -k passwd_changes
```

---

### Question 25
How do you generate an audit report?

**Answer:**
```bash
sudo aureport
# or for specific report
sudo aureport --auth
sudo aureport --failed
```

---

### Question 26
What is the main auditd configuration file?

**Answer:**
/etc/audit/auditd.conf

---

### Question 27
How do you make audit rules persistent across reboots?

**Answer:**
```bash
# Add rules to:
sudo nano /etc/audit/rules.d/audit.rules

# Then reload:
sudo augenrules --load
```

---

### Question 28
What command configures rsyslog to send logs to a remote server?

**Answer:**
```bash
# Edit /etc/rsyslog.conf and add:
*.* @192.168.1.100:514    # UDP
*.* @@192.168.1.100:514   # TCP
```

---

### Question 29
How do you manually rotate logs with logrotate?

**Answer:**
```bash
sudo logrotate -f /etc/logrotate.conf
```

---

### Question 30
What command monitors authentication logs in real-time?

**Answer:**
```bash
sudo tail -f /var/log/auth.log
# or
sudo journalctl -u sshd -f
```

---

## Section 4: Security Tools and Scanning (10 Questions)

### Question 31
How do you run a Lynis security audit?

**Answer:**
```bash
sudo lynis audit system
```

---

### Question 32
What command installs rkhunter?

**Answer:**
```bash
sudo apt install rkhunter
```

---

### Question 33
How do you update rkhunter database?

**Answer:**
```bash
sudo rkhunter --update
```

---

### Question 34
What command runs a rootkit check with rkhunter?

**Answer:**
```bash
sudo rkhunter --check
# or skip keypresses
sudo rkhunter --check --skip-keypress
```

---

### Question 35
How do you install ClamAV antivirus?

**Answer:**
```bash
sudo apt install clamav clamav-daemon
```

---

### Question 36
What command updates ClamAV virus definitions?

**Answer:**
```bash
sudo freshclam
```

---

### Question 37
How do you scan a directory with ClamAV?

**Answer:**
```bash
sudo clamscan -r /home
# or with removal
sudo clamscan -r --remove /home
```

---

### Question 38
What command runs chkrootkit?

**Answer:**
```bash
sudo chkrootkit
```

---

### Question 39
How do you scan a web server with Nikto?

**Answer:**
```bash
nikto -h http://target.com
```

---

### Question 40
What command shows Lynis suggestions from the last audit?

**Answer:**
```bash
sudo cat /var/log/lynis.log | grep Suggestion
```

---

## Section 5: Compliance and Best Practices (10 Questions)

### Question 41
What does CIS stand for?

**Answer:**
Center for Internet Security

---

### Question 42
How do you check AppArmor status?

**Answer:**
```bash
sudo aa-status
```

---

### Question 43
What command sets an AppArmor profile to enforce mode?

**Answer:**
```bash
sudo aa-enforce /etc/apparmor.d/usr.sbin.nginx
```

---

### Question 44
How do you set an AppArmor profile to complain mode?

**Answer:**
```bash
sudo aa-complain /etc/apparmor.d/usr.sbin.nginx
```

---

### Question 45
What command checks SELinux status?

**Answer:**
```bash
sestatus
# or
getenforce
```

---

### Question 46
How do you set SELinux to permissive mode temporarily?

**Answer:**
```bash
sudo setenforce 0
```

---

### Question 47
What file contains kernel parameter configurations?

**Answer:**
/etc/sysctl.conf
or
/etc/sysctl.d/*.conf

---

### Question 48
How do you apply sysctl changes without rebooting?

**Answer:**
```bash
sudo sysctl -p
# or for specific file
sudo sysctl -p /etc/sysctl.d/99-security.conf
```

---

### Question 49
What command lists loaded kernel modules?

**Answer:**
```bash
lsmod
```

---

### Question 50
How do you blacklist a kernel module?

**Answer:**
```bash
# Create/edit blacklist file
sudo nano /etc/modprobe.d/blacklist.conf

# Add line:
blacklist module_name
# or
install module_name /bin/true

# Update initramfs
sudo update-initramfs -u
```

---

## Summary

**Total Questions: 50**

- Section 1: User Security and Authentication (10 questions)
- Section 2: File System Security (10 questions)
- Section 3: System Auditing and Logging (10 questions)
- Section 4: Security Tools and Scanning (10 questions)
- Section 5: Compliance and Best Practices (10 questions)

**Practice Tips:**
1. Set up an isolated lab environment for security testing
2. Never test security tools on production systems
3. Practice each hardening technique step-by-step
4. Document your security configurations
5. Use `man command` to explore all security options
6. Create security checklists for different scenarios
7. Practice incident response procedures
8. Review security logs regularly

**Scoring Guide:**
- 45-50 correct: Expert! Ready for security certifications
- 38-44 correct: Very Good! Minor review needed
- 30-37 correct: Good! Practice more hands-on labs
- 22-29 correct: Fair. Review concepts and practice
- Below 22: Review the material thoroughly and retry

**Study Schedule:**
- **Day 18**: User Security and Authentication (Questions 1-10)
- **Day 19**: File System Security (Questions 11-20)
- **Day 20**: System Auditing and Logging (Questions 21-30)
- **Day 21**: Security Tools and Scanning (Questions 31-40)
- **Day 22**: Compliance and Best Practices (Questions 41-50)

**Next Steps:**
- Complete all 50 questions
- Practice each command in your lab environment
- Implement a complete security hardening plan
- Move on to Advanced Topics & Practical Labs
- Build a security monitoring dashboard
- Create automated security scripts
- Explore advanced security topics

**Certification Preparation:**
These questions align with:
- CompTIA Security+
- LPIC-303 Security
- RHCSA (Red Hat Certified System Administrator)
- CEH (Certified Ethical Hacker)
- CISSP (Certified Information Systems Security Professional)

**Additional Practice Resources:**
- Set up vulnerable VMs (Metasploitable, DVWA)
- Practice on different distributions
- Implement CIS Benchmarks
- Configure mandatory access controls
- Build security automation scripts
- Join security communities (r/netsec, r/linuxsecurity)

**Hands-on Lab Ideas:**
1. Build a fully hardened web server
2. Implement centralized security monitoring
3. Create automated vulnerability scanning
4. Set up intrusion detection system
5. Configure mandatory access control (AppArmor/SELinux)
6. Build security incident response procedures
7. Implement compliance automation
8. Create security baseline templates

**Security Best Practices Checklist:**
- [ ] Strong password policies implemented
- [ ] Multi-factor authentication configured
- [ ] SSH hardened with key-based auth
- [ ] File permissions audited and secured
- [ ] Encryption enabled for sensitive data
- [ ] File integrity monitoring active
- [ ] Comprehensive audit rules configured
- [ ] Centralized logging implemented
- [ ] Regular vulnerability scans scheduled
- [ ] Rootkit detection automated
- [ ] Malware scanning configured
- [ ] Firewall rules optimized
- [ ] Unnecessary services disabled
- [ ] Kernel hardening applied
- [ ] Compliance framework implemented
- [ ] Security monitoring dashboard created
- [ ] Incident response plan documented
- [ ] Regular security audits scheduled

Good luck with your Security Hardening journey!
