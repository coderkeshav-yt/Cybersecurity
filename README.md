# Cybersecurity Mastery Roadmap

A comprehensive, hands-on cybersecurity learning path from fundamentals to advanced exploitation techniques. This repository contains structured modules covering Linux security, attack methodologies, exploitation tools, and defensive security.

## 📚 Repository Structure

```
Cybersecurity/
├── Level 1 - Linux and OS Security/
│   ├── 1. Linux Fundamentals/
│   ├── 2. System Administration/
│   ├── 3. Networking and Services/
│   ├── 4. Security Hardening/
│   └── 5. Advanced Topics & Practical Labs/
│
├── Level 2 - Attack Methodologies/
│   ├── 1-Reconnaissance-OSINT/
│   ├── 2-Scanning-Enumeration/
│   ├── 3-Vulnerability-Analysis/
│   ├── 4-Exploitation-Fundamentals/
│   └── 5-Website-Hacking/
│
├── Level 3 - Tools and Exploitation/
│   ├── 1-Advanced-Metasploit/
│   ├── 2-Exploitation-Tools/
│   ├── 3-Password-Attacks/
│   ├── 4-Wireless-Attacks/
│   └── 5-Social-Engineering/
│
└── Level 4 - Defensive Security/
    ├── 1-Security-Monitoring/
    ├── 2-Incident-Response/
    ├── 3-Threat-Hunting/
    ├── 4-Security-Operations/
    └── 5-Blue-Team-Tools/
```

## 🎯 Learning Path Overview

### Level 1: Linux and OS Security
**Foundation for all cybersecurity work**

Master Linux fundamentals, system administration, networking, and security hardening. This level provides the essential foundation needed for penetration testing and security operations.

**Topics Covered:**
- Linux command line mastery
- File system navigation and permissions
- User and group management
- Package management and system updates
- Network configuration and services
- Firewall configuration (iptables, ufw)
- SSH hardening and key management
- Log analysis and monitoring
- Bash scripting and automation
- Security best practices

**Time Estimate:** 4-6 weeks

### Level 2: Attack Methodologies
**Understanding the attacker's perspective**

Learn the complete penetration testing methodology from reconnaissance to exploitation. Understand how attackers think and operate.

**Topics Covered:**
- **Reconnaissance & OSINT:** Information gathering, Google dorking, social media intelligence
- **Scanning & Enumeration:** Nmap, service detection, vulnerability scanning
- **Vulnerability Analysis:** CVE research, exploit databases, vulnerability assessment
- **Exploitation Fundamentals:** Metasploit, payload generation, post-exploitation
- **Website Hacking:** OWASP Top 10, SQL injection, XSS, CSRF, file upload vulnerabilities

**Time Estimate:** 6-8 weeks

### Level 3: Tools and Exploitation
**Advanced offensive security techniques**

Deep dive into exploitation tools, frameworks, and techniques. Master advanced attack vectors and tool development.

**Topics Covered:**
- **Advanced Metasploit:** Custom modules, automation, database integration, RPC
- **Exploitation Tools:** Netcat, Socat, Burp Suite, SQLMap, binary exploitation
- **Password Attacks:** Hash cracking, brute force, password spraying, credential harvesting
- **Wireless Attacks:** WiFi hacking, WEP/WPA/WPA2/WPA3, rogue access points
- **Social Engineering:** Phishing, pretexting, physical security, SET toolkit

**Time Estimate:** 8-10 weeks

### Level 4: Defensive Security
**Protecting systems and detecting threats**

Learn defensive security, incident response, threat hunting, and security operations. Understand how to defend against the attacks you've learned.

**Topics Covered:**
- Security monitoring and SIEM
- Incident response procedures
- Threat hunting techniques
- Security operations center (SOC)
- Blue team tools and methodologies
- Malware analysis basics
- Digital forensics
- Security automation

**Time Estimate:** 6-8 weeks

## 🚀 Getting Started

### Prerequisites

**Hardware Requirements:**
- Computer with at least 8GB RAM (16GB recommended)
- 100GB free disk space
- Virtualization support (Intel VT-x or AMD-V)

**Software Requirements:**
- VirtualBox or VMware Workstation
- Kali Linux (latest version)
- Metasploitable 2/3 (for practice)
- Windows 10 VM (for testing)

**Knowledge Prerequisites:**
- Basic computer literacy
- Understanding of networking concepts (helpful but not required)
- Willingness to learn and practice

### Installation Guide

**1. Install Virtualization Software:**
```bash
# VirtualBox (Free)
# Download from: https://www.virtualbox.org/

# VMware Workstation Player (Free for personal use)
# Download from: https://www.vmware.com/
```

**2. Set Up Kali Linux:**
```bash
# Download Kali Linux VM:
# https://www.kali.org/get-kali/#kali-virtual-machines

# Import into VirtualBox/VMware
# Default credentials: kali/kali

# Update system:
sudo apt update && sudo apt upgrade -y

# Install essential tools:
sudo apt install -y metasploit-framework burpsuite sqlmap nmap wireshark
```

**3. Set Up Practice Environments:**
```bash
# Metasploitable 2:
# Download: https://sourceforge.net/projects/metasploitable/

# Metasploitable 3:
# https://github.com/rapid7/metasploitable3

# DVWA (Damn Vulnerable Web Application):
# https://github.com/digininja/DVWA

# VulnHub machines:
# https://www.vulnhub.com/
```

### Network Configuration

**Recommended Setup:**
```bash
# Network 1: NAT (Internet access)
- For updates and downloads

# Network 2: Host-Only (Isolated lab)
- Kali Linux: 192.168.56.100
- Metasploitable: 192.168.56.101
- Windows VM: 192.168.56.102

# Network 3: Internal (Pivoting practice)
- For advanced scenarios
```

## 📖 How to Use This Repository

### Study Approach

**1. Sequential Learning:**
- Start with Level 1 (Linux fundamentals)
- Progress through each level in order
- Complete practice questions before moving forward
- Hands-on practice is essential

**2. Each Module Contains:**
- **README.md:** Comprehensive guide with theory and examples
- **practice-questions.md:** 50-60 questions with answers
- Code examples and command references
- Practical exercises
- Additional resources

**3. Recommended Study Schedule:**
```bash
# Daily commitment: 2-3 hours
# Weekly commitment: 14-21 hours

Week 1-4:   Level 1 - Linux Fundamentals
Week 5-12:  Level 2 - Attack Methodologies
Week 13-22: Level 3 - Tools and Exploitation
Week 23-30: Level 4 - Defensive Security

Total: 30 weeks (7-8 months)
```

### Practice Labs

**Beginner-Friendly:**
- Metasploitable 2/3
- DVWA
- WebGoat
- OverTheWire: Bandit

**Intermediate:**
- HackTheBox (Easy boxes)
- TryHackMe rooms
- VulnHub machines
- PentesterLab

**Advanced:**
- HackTheBox (Medium/Hard boxes)
- Offensive Security Proving Grounds
- Real-world bug bounty programs
- Custom lab environments

## 🎓 Certifications Path

After completing this roadmap, you'll be prepared for:

**Entry Level:**
- CompTIA Security+
- CompTIA Network+
- CEH (Certified Ethical Hacker)

**Intermediate:**
- OSCP (Offensive Security Certified Professional)
- eCPPT (eLearnSecurity Certified Professional Penetration Tester)
- GPEN (GIAC Penetration Tester)

**Advanced:**
- OSEP (Offensive Security Experienced Penetration Tester)
- OSCE (Offensive Security Certified Expert)
- GXPN (GIAC Exploit Researcher and Advanced Penetration Tester)

**Defensive:**
- GCIH (GIAC Certified Incident Handler)
- GCFA (GIAC Certified Forensic Analyst)
- GCIA (GIAC Certified Intrusion Analyst)

## 🛠️ Essential Tools Reference

### Reconnaissance
```bash
nmap, masscan, shodan, theHarvester, recon-ng, maltego
```

### Exploitation
```bash
metasploit, sqlmap, burp suite, nikto, wpscan, searchsploit
```

### Password Attacks
```bash
john, hashcat, hydra, medusa, crunch, cewl
```

### Wireless
```bash
aircrack-ng, wifite, reaver, bully, kismet
```

### Post-Exploitation
```bash
mimikatz, bloodhound, powersploit, empire, covenant
```

### Defensive
```bash
wireshark, snort, suricata, osquery, elk stack, splunk
```

## 📝 Practice Questions Summary

Each module includes comprehensive practice questions:

- **Level 1:** 250+ questions covering Linux fundamentals
- **Level 2:** 300+ questions on attack methodologies
- **Level 3:** 300+ questions on tools and exploitation
- **Level 4:** 250+ questions on defensive security

**Total: 1,100+ practice questions with detailed answers**

## 🔒 Legal and Ethical Guidelines

### ⚠️ IMPORTANT DISCLAIMER

**This repository is for educational purposes only.**

### Legal Requirements

1. **Always obtain written permission** before testing any system
2. **Never attack systems you don't own** or have explicit authorization to test
3. **Follow all applicable laws** in your jurisdiction
4. **Respect privacy** and data protection regulations
5. **Use knowledge responsibly** for defensive purposes

### Ethical Guidelines

1. **Do No Harm:** Don't damage systems or data
2. **Respect Privacy:** Don't access personal information without authorization
3. **Stay in Scope:** Only test authorized systems within agreed boundaries
4. **Responsible Disclosure:** Report vulnerabilities properly
5. **Continuous Learning:** Stay updated on techniques and defenses
6. **Give Back:** Share knowledge and help others learn

### Authorized Practice Environments

**Legal places to practice:**
- Your own systems and networks
- Authorized lab environments (Metasploitable, DVWA)
- HackTheBox, TryHackMe, VulnHub
- Bug bounty programs (within scope)
- Capture The Flag (CTF) competitions
- Authorized penetration testing engagements

**Illegal activities:**
- Unauthorized access to computer systems
- Unauthorized network scanning
- Deploying malware without permission
- Stealing or accessing data without authorization
- Denial of service attacks
- Any testing without explicit written permission

## 🤝 Contributing

Contributions are welcome! Please follow these guidelines:

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test thoroughly
5. Submit a pull request

**Areas for contribution:**
- Additional practice questions
- New modules or topics
- Code examples and scripts
- Lab setup guides
- Bug fixes and improvements
- Documentation enhancements

## 📚 Additional Resources

### Books
- "The Web Application Hacker's Handbook" by Dafydd Stuttard
- "Metasploit: The Penetration Tester's Guide" by David Kennedy
- "The Hacker Playbook 3" by Peter Kim
- "Penetration Testing" by Georgia Weidman
- "Black Hat Python" by Justin Seitz

### Online Platforms
- [HackTheBox](https://www.hackthebox.eu/)
- [TryHackMe](https://tryhackme.com/)
- [VulnHub](https://www.vulnhub.com/)
- [PentesterLab](https://pentesterlab.com/)
- [OverTheWire](https://overthewire.org/)

### Video Courses
- Offensive Security (OSCP, OSEP)
- eLearnSecurity (eCPPT, eCPTX)
- Cybrary
- Udemy cybersecurity courses
- YouTube: IppSec, LiveOverflow, John Hammond

### Communities
- Reddit: r/netsec, r/AskNetsec, r/HowToHack
- Discord: HackTheBox, TryHackMe communities
- Twitter: InfoSec community
- Local security meetups and conferences

### Blogs and News
- Krebs on Security
- Schneier on Security
- The Hacker News
- Bleeping Computer
- Dark Reading

## 📊 Progress Tracking

### Level 1 Checklist
- [ ] Complete Linux Fundamentals
- [ ] Complete System Administration
- [ ] Complete Networking and Services
- [ ] Complete Security Hardening
- [ ] Complete Advanced Topics & Labs
- [ ] Pass all practice questions (80%+)

### Level 2 Checklist
- [ ] Complete Reconnaissance & OSINT
- [ ] Complete Scanning & Enumeration
- [ ] Complete Vulnerability Analysis
- [ ] Complete Exploitation Fundamentals
- [ ] Complete Website Hacking
- [ ] Pass all practice questions (80%+)

### Level 3 Checklist
- [ ] Complete Advanced Metasploit
- [ ] Complete Exploitation Tools
- [ ] Complete Password Attacks
- [ ] Complete Wireless Attacks
- [ ] Complete Social Engineering
- [ ] Pass all practice questions (80%+)

### Level 4 Checklist
- [ ] Complete Security Monitoring
- [ ] Complete Incident Response
- [ ] Complete Threat Hunting
- [ ] Complete Security Operations
- [ ] Complete Blue Team Tools
- [ ] Pass all practice questions (80%+)

## 🎯 Learning Objectives

By completing this roadmap, you will be able to:

**Technical Skills:**
- Master Linux command line and system administration
- Perform comprehensive penetration testing
- Exploit common vulnerabilities
- Use industry-standard security tools
- Develop custom exploitation scripts
- Conduct wireless security assessments
- Perform social engineering assessments
- Implement security monitoring and detection
- Respond to security incidents
- Hunt for threats in enterprise environments

**Professional Skills:**
- Think like both an attacker and defender
- Document findings professionally
- Communicate security risks effectively
- Follow ethical guidelines
- Work in security teams
- Continuous learning mindset

## 📞 Support and Contact

**Questions or Issues?**
- Open an issue in this repository
- Check existing issues for solutions
- Review the FAQ section
- Join community discussions

**Stay Updated:**
- Watch this repository for updates
- Follow cybersecurity news
- Join security communities
- Attend conferences and meetups

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgments

- Offensive Security for OSCP methodology
- HackTheBox and TryHackMe communities
- Open source security tool developers
- Cybersecurity educators and content creators
- All contributors to this repository

---

## 🚀 Start Your Journey

Ready to begin? Start with [Level 1 - Linux Fundamentals](Level%201%20-%20Linux%20and%20OS%20Security/1.%20Linux%20Fundamentals/README.md)

**Remember:** 
- Practice in authorized environments only
- Learn responsibly and ethically
- Help others on their journey
- Never stop learning

**Good luck on your cybersecurity journey! 🔐**

---

*Last Updated: November 2024*
*Repository maintained for educational purposes*
