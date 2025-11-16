# Website Hacking - Complete Guide

## 📚 Overview

Website hacking encompasses the techniques, methodologies, and tools used to identify and exploit vulnerabilities in web applications. This module covers everything from OWASP Top 10 vulnerabilities to advanced attack techniques used by professional penetration testers and bug bounty hunters.

## 🎯 Learning Objectives

By the end of this module, you will be able to:

- Identify and exploit OWASP Top 10 vulnerabilities
- Perform comprehensive web application security assessments
- Use industry-standard tools for web penetration testing
- Understand modern web attack vectors and defense mechanisms
- Execute advanced attacks like SSRF, XXE, and deserialization exploits
- Analyze and exploit business logic flaws
- Conduct responsible vulnerability disclosure

## 📖 Module Contents

### 1. [OWASP Top 10 Attacks](./owasp-top10-attacks.md)
Comprehensive coverage of the OWASP Top 10 (2025 Edition):

- **A01:2025 - Broken Access Control**
  - IDOR (Insecure Direct Object References)
  - Path Traversal
  - Missing Function Level Access Control
  - Horizontal & Vertical Privilege Escalation
  
- **A02:2025 - Cryptographic Failures**
  - Weak Encryption
  - Sensitive Data Exposure
  - Insecure Storage
  
- **A03:2025 - Injection**
  - SQL Injection
  - Command Injection
  - XSS (Cross-Site Scripting)
  - LDAP, XPath, NoSQL Injection
  
- **A04:2025 - Insecure Design**
  - Rate Limiting Bypass
  - Business Logic Flaws
  - Missing Security Requirements
  
- **A05:2025 - Security Misconfiguration**
  - Default Credentials
  - Directory Listing
  - Missing Security Headers
  - CORS Misconfiguration
  
- **A06:2025 - Vulnerable Components**
  - Outdated Software Detection
  - Dependency Scanning
  - Known CVE Exploitation
  
- **A07:2025 - Authentication Failures**
  - Weak Password Policies
  - Credential Stuffing
  - Session Management Flaws
  - MFA Bypass
  
- **A08:2025 - Data Integrity Failures**
  - Insecure Deserialization
  - CI/CD Pipeline Vulnerabilities
  - Supply Chain Attacks
  
- **A09:2025 - Logging & Monitoring Failures**
  - Insufficient Logging
  - Log Injection
  - Missing Alerting
  
- **A10:2025 - Server-Side Request Forgery (SSRF)**
  - Internal Network Scanning
  - Cloud Metadata Exploitation
  - Protocol Smuggling

### 2. [Advanced Attack Techniques](./advanced-attacks.md)
Deep dive into sophisticated web exploitation methods:

- **Server-Side Request Forgery (SSRF)**
  - Cloud metadata exploitation (AWS, GCP, Azure)
  - Internal network pivoting
  - Bypass techniques
  
- **XML External Entity (XXE)**
  - File disclosure
  - SSRF via XXE
  - Blind XXE exploitation
  - XXE in file uploads (SVG, DOCX)
  
- **Server-Side Template Injection (SSTI)**
  - Jinja2 (Python/Flask)
  - Twig (PHP/Symfony)
  - Freemarker (Java)
  - Velocity (Java)
  
- **Insecure Deserialization**
  - PHP object injection
  - Java deserialization (ysoserial)
  - Python pickle exploits
  - .NET deserialization
  
- **Business Logic Vulnerabilities**
  - Price manipulation
  - Currency manipulation
  - Discount code abuse
  - Workflow bypass
  
- **Race Conditions**
  - Limit overrun
  - Double spending
  - File upload race conditions
  
- **HTTP Request Smuggling**
  - CL.TE attacks
  - TE.CL attacks
  - TE.TE obfuscation
  
- **WebSocket Attacks**
  - WebSocket hijacking
  - Message manipulation
  - WebSocket fuzzing

## 🛠️ Essential Tools

### Reconnaissance & Enumeration
```bash
# Technology detection
whatweb http://target.com
wappalyzer

# Subdomain enumeration
subfinder -d target.com
amass enum -d target.com

# Directory brute forcing
gobuster dir -u http://target.com -w wordlist.txt
ffuf -u http://target.com/FUZZ -w wordlist.txt
dirsearch -u http://target.com
```

### Vulnerability Scanning
```bash
# Automated scanners
nikto -h http://target.com
nuclei -u http://target.com
zap-cli quick-scan http://target.com

# CMS scanners
wpscan --url http://target.com  # WordPress
joomscan -u http://target.com   # Joomla
droopescan scan drupal -u http://target.com  # Drupal
```

### Exploitation Tools
```bash
# SQL Injection
sqlmap -u "http://target.com/page?id=1"

# XSS
dalfox url http://target.com/search?q=test

# Command Injection
commix --url="http://target.com/page?cmd=test"

# SSRF
ssrfmap -r request.txt

# Deserialization
ysoserial.jar  # Java
ysoserial.net  # .NET
```

### Proxy & Interception
```bash
# Burp Suite Professional/Community
# OWASP ZAP
# mitmproxy
```

## 🎓 Learning Path

### Beginner Level (Weeks 1-2)
1. Understand HTTP/HTTPS fundamentals
2. Learn basic HTML, JavaScript, and web technologies
3. Study OWASP Top 10 vulnerabilities
4. Practice on DVWA (Damn Vulnerable Web Application)
5. Learn to use Burp Suite basics

### Intermediate Level (Weeks 3-4)
1. Master SQL injection techniques
2. Understand XSS variants (Reflected, Stored, DOM)
3. Learn authentication and session management flaws
4. Practice on WebGoat and Juice Shop
5. Study real-world bug bounty reports

### Advanced Level (Weeks 5-8)
1. Master SSRF and XXE exploitation
2. Learn SSTI and deserialization attacks
3. Understand race conditions and request smuggling
4. Practice on HackTheBox and PortSwigger Academy
5. Participate in bug bounty programs (responsibly)

## 🧪 Practice Labs

### Intentionally Vulnerable Applications
- **DVWA** - Damn Vulnerable Web Application
- **WebGoat** - OWASP WebGoat
- **Juice Shop** - OWASP Juice Shop
- **bWAPP** - Buggy Web Application
- **Mutillidae** - OWASP Mutillidae
- **WebSecurity Dojo** - Training environment

### Online Platforms
- **PortSwigger Web Security Academy** - Free labs with excellent explanations
- **HackTheBox** - Web application challenges
- **TryHackMe** - Guided web security rooms
- **PentesterLab** - Hands-on exercises
- **Root-Me** - Web challenges
- **OverTheWire** - Natas wargame

## 📝 Testing Methodology

### 1. Information Gathering
```bash
# Passive reconnaissance
- Google dorking
- Shodan/Censys searches
- Certificate transparency logs
- Archive.org (Wayback Machine)
- GitHub/GitLab repository searches

# Active reconnaissance
- Port scanning (nmap)
- Service enumeration
- Technology fingerprinting
- Subdomain discovery
- Directory enumeration
```

### 2. Vulnerability Assessment
```bash
# Automated scanning
- Run vulnerability scanners
- Check for known CVEs
- Identify outdated components
- Test for common misconfigurations

# Manual testing
- Analyze application logic
- Test input validation
- Check authentication mechanisms
- Review session management
- Test authorization controls
```

### 3. Exploitation
```bash
# Proof of Concept
- Develop working exploits
- Document exploitation steps
- Capture screenshots/videos
- Assess impact and severity

# Post-exploitation
- Privilege escalation
- Lateral movement
- Data exfiltration
- Persistence mechanisms
```

### 4. Reporting
```bash
# Report structure
- Executive summary
- Vulnerability details
- Steps to reproduce
- Proof of concept
- Impact assessment
- Remediation recommendations
- References (CVE, CWE)
```

## 🛡️ Defense Strategies

### Secure Development Practices
```bash
# Input validation
- Whitelist approach
- Sanitize all user input
- Use parameterized queries
- Validate file uploads
- Implement proper encoding

# Authentication & Authorization
- Strong password policies
- Multi-factor authentication
- Secure session management
- Principle of least privilege
- Regular access reviews

# Encryption
- Use TLS 1.2+ only
- Strong cipher suites
- HSTS implementation
- Encrypt sensitive data at rest
- Secure key management
```

### Security Headers
```http
# Essential headers
Strict-Transport-Security: max-age=31536000; includeSubDomains
X-Frame-Options: DENY
X-Content-Type-Options: nosniff
X-XSS-Protection: 1; mode=block
Content-Security-Policy: default-src 'self'
Referrer-Policy: no-referrer
Permissions-Policy: geolocation=(), microphone=(), camera=()
```

### Web Application Firewall (WAF)
```bash
# Popular WAFs
- ModSecurity (Open source)
- Cloudflare WAF
- AWS WAF
- Azure WAF
- Imperva
- F5 Advanced WAF
```

## 🔍 Real-World Case Studies

### Notable Web Vulnerabilities

**1. Equifax Breach (2017)**
- Vulnerability: Apache Struts RCE (CVE-2017-5638)
- Impact: 147 million records compromised
- Lesson: Keep components updated

**2. Capital One Breach (2019)**
- Vulnerability: SSRF in AWS metadata
- Impact: 100 million customer records
- Lesson: Secure cloud configurations

**3. MOVEit Breach (2023)**
- Vulnerability: SQL injection
- Impact: 2,000+ organizations affected
- Lesson: Secure file transfer applications

**4. Log4Shell (2021)**
- Vulnerability: Log4j RCE (CVE-2021-44228)
- Impact: Millions of systems worldwide
- Lesson: Dependency management is critical

## 📚 Additional Resources

### Books
- "The Web Application Hacker's Handbook" by Dafydd Stuttard
- "Real-World Bug Hunting" by Peter Yaworski
- "Web Security Testing Cookbook" by Paco Hope
- "Mastering Modern Web Penetration Testing" by Prakhar Prasad

### Online Courses
- PortSwigger Web Security Academy (Free)
- PentesterAcademy Web Application Penetration Testing
- Offensive Security Web Expert (OSWE)
- eLearnSecurity Web Application Penetration Tester (eWPT)

### Blogs & Resources
- PortSwigger Research Blog
- HackerOne Hacktivity
- Bugcrowd Vulnerability Disclosure
- OWASP Testing Guide
- PayloadsAllTheThings (GitHub)
- HackTricks

### Bug Bounty Platforms
- HackerOne
- Bugcrowd
- Intigriti
- YesWeHack
- Synack

## ⚖️ Legal & Ethical Considerations

### Always Remember:
1. **Get Written Permission** - Never test without authorization
2. **Respect Scope** - Stay within defined boundaries
3. **Responsible Disclosure** - Report vulnerabilities properly
4. **No Harm** - Don't damage systems or data
5. **Privacy** - Respect user privacy and data
6. **Legal Compliance** - Follow local laws and regulations

### Bug Bounty Best Practices
```bash
# Before testing
- Read the program policy carefully
- Understand scope (in-scope vs out-of-scope)
- Check for duplicate reports
- Set up proper testing environment

# During testing
- Use dedicated testing accounts
- Don't access other users' data
- Avoid DoS attacks
- Don't spam the application
- Document everything

# After finding a vulnerability
- Report immediately
- Provide clear reproduction steps
- Include proof of concept
- Suggest remediation
- Wait for response before disclosure
```

## 🎯 Practice Challenges

### Challenge 1: SQL Injection
```bash
# Setup: DVWA on low security
# Task: Extract all usernames and passwords from the database
# Bonus: Get a shell on the server
```

### Challenge 2: XSS to Account Takeover
```bash
# Setup: Juice Shop
# Task: Steal admin session cookie using stored XSS
# Bonus: Perform CSRF to change admin password
```

### Challenge 3: SSRF to Cloud Metadata
```bash
# Setup: Custom vulnerable app
# Task: Access AWS metadata service
# Bonus: Extract IAM credentials
```

### Challenge 4: Insecure Deserialization
```bash
# Setup: Java application with serialization
# Task: Achieve RCE using ysoserial
# Bonus: Establish reverse shell
```

### Challenge 5: Business Logic Flaw
```bash
# Setup: E-commerce application
# Task: Purchase items for negative price
# Bonus: Exploit race condition for unlimited credits
```

## 📊 Skill Assessment

### Beginner Checklist
- [ ] Understand HTTP request/response structure
- [ ] Can use Burp Suite to intercept traffic
- [ ] Identify and exploit basic SQL injection
- [ ] Find and exploit reflected XSS
- [ ] Understand CSRF and how to exploit it
- [ ] Can enumerate directories and files
- [ ] Understand authentication bypass techniques

### Intermediate Checklist
- [ ] Master advanced SQL injection (blind, time-based)
- [ ] Exploit stored and DOM-based XSS
- [ ] Identify and exploit IDOR vulnerabilities
- [ ] Understand and exploit XXE
- [ ] Can bypass WAF filters
- [ ] Exploit file upload vulnerabilities
- [ ] Understand SSRF and basic exploitation

### Advanced Checklist
- [ ] Exploit SSTI in multiple template engines
- [ ] Master insecure deserialization attacks
- [ ] Identify and exploit race conditions
- [ ] Understand HTTP request smuggling
- [ ] Can chain multiple vulnerabilities
- [ ] Exploit complex business logic flaws
- [ ] Write custom exploitation scripts

## 🚀 Next Steps

After completing this module, you should:

1. **Practice Regularly** - Use vulnerable applications daily
2. **Read Bug Bounty Reports** - Learn from others' findings
3. **Participate in CTFs** - Sharpen your skills competitively
4. **Join Bug Bounty Programs** - Start with VDP (Vulnerability Disclosure Programs)
5. **Stay Updated** - Follow security researchers and blogs
6. **Build Your Lab** - Set up your own testing environment
7. **Document Your Journey** - Keep notes and write blog posts
8. **Network** - Join security communities and forums

## 📞 Community & Support

### Forums & Communities
- Reddit: r/netsec, r/websecurity, r/bugbounty
- Discord: Bug Bounty Hunters, HackTheBox, TryHackMe
- Twitter: Follow security researchers
- LinkedIn: Join web security groups

### Getting Help
- Stack Overflow (security tag)
- Information Security Stack Exchange
- OWASP Slack
- Bug bounty platform forums

---

## ⚠️ Disclaimer

**This material is for educational purposes only.** 

Unauthorized access to computer systems is illegal and unethical. Always:
- Obtain written permission before testing
- Follow responsible disclosure practices
- Respect privacy and data protection laws
- Use knowledge for defensive purposes
- Help make the internet more secure

**Remember:** With great power comes great responsibility. Use your skills ethically and legally.

---

## 📈 Progress Tracking

Track your progress through this module:

- [ ] Read OWASP Top 10 documentation
- [ ] Complete DVWA challenges
- [ ] Finish PortSwigger Academy labs
- [ ] Read Advanced Attacks documentation
- [ ] Complete Juice Shop challenges
- [ ] Solve 10 HackTheBox web challenges
- [ ] Find your first bug bounty vulnerability
- [ ] Write a blog post about your learning

---

**Happy Hacking! 🎉**

*Remember: Always hack ethically and legally. The goal is to make the web more secure, not to cause harm.*
