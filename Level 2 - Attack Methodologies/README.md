# Level 2: Attack Methodologies & Vulnerability Analysis

## Introduction
Understanding attack methodologies is essential for both offensive security professionals and defenders. This level covers the systematic approach to identifying and exploiting vulnerabilities, providing a solid foundation in offensive security concepts.

## Core Topics

### 1. Cyber Kill Chain & MITRE ATT&CK Framework

#### Cyber Kill Chain
1. **Reconnaissance**
   - Passive information gathering
   - Active scanning and enumeration
   - Social engineering reconnaissance

2. **Weaponization**
   - Creating malware payloads
   - Exploit development basics
   - Obfuscation techniques

3. **Delivery**
   - Phishing emails
   - Watering hole attacks
   - USB drops

4. **Exploitation**
   - Vulnerability exploitation
   - Privilege escalation
   - Post-exploitation techniques

5. **Installation**
   - Backdoors
   - Persistence mechanisms
   - Rootkits

6. **Command and Control (C2)**
   - C2 frameworks
   - Covert channels
   - Domain generation algorithms

7. **Actions on Objectives**
   - Data exfiltration
   - Data destruction
   - Ransomware deployment

#### MITRE ATT&CK Framework
- **Tactics, Techniques, and Procedures (TTPs)**
- Enterprise matrix
- Mobile matrix
- Pre-ATT&CK matrix

### 2. Reconnaissance and Information Gathering

#### Passive Reconnaissance
- **Tools and Techniques**
  ```bash
  # WHOIS lookups
  whois example.com
  
  # DNS enumeration
  dnsrecon -d example.com
  
  # Subdomain enumeration
  sublist3r -d example.com
  
  # Email harvesting
  theharvester -d example.com -l 500 -b google
  ```

#### Active Reconnaissance
- **Port Scanning with Nmap**
  ```bash
  # Basic scan
  nmap -sV -sC -oA scan example.com
  
  # Aggressive scan
  nmap -A -T4 example.com
  
  # UDP scan
  nmap -sU -p 53,67,68,69,123,161,162,445,500,1434,1900,4500,49152 example.com
  ```

### 3. Scanning and Enumeration

#### Network Scanning
- **Port Scanning Techniques**
  - TCP Connect Scan
  - SYN Scan (Stealth Scan)
  - UDP Scan
  - ACK Scan
  - Window Scan
  - Maimon Scan

#### Service Enumeration
- **Common Services to Enumerate**
  - HTTP/HTTPS (80/443)
  - SMB (139/445)
  - SSH (22)
  - FTP (21)
  - SMTP (25)
  - DNS (53)
  - RDP (3389)

### 4. Common Attack Vectors

#### Web Application Attacks (OWASP Top 10)
1. Injection (SQLi, Command Injection)
2. Broken Authentication
3. Sensitive Data Exposure
4. XML External Entities (XXE)
5. Broken Access Control
6. Security Misconfigurations
7. Cross-Site Scripting (XSS)
8. Insecure Deserialization
9. Using Components with Known Vulnerabilities
10. Insufficient Logging & Monitoring

#### Network Attacks
- **Man-in-the-Middle (MITM) Attacks**
  - ARP Spoofing
  - DNS Spoofing
  - SSL Stripping
  - DHCP Spoofing

- **Wireless Attacks**
  - WPA2/3 Cracking
  - Evil Twin
  - Karma Attacks
  - WPS Attacks

### 5. Basic Exploit Development

#### Buffer Overflow Basics
```c
#include <stdio.h>
#include <string.h>

void vulnerable_function(char *input) {
    char buffer[100];
    strcpy(buffer, input); // Vulnerability here
    printf("Buffer: %s\n", buffer);
}

int main(int argc, char *argv[]) {
    if (argc > 1) {
        vulnerable_function(argv[1]);
    } else {
        printf("Usage: %s <input>\n", argv[0]);
    }
    return 0;
}
```

#### Exploit Development Process
1. Fuzzing the application
2. Finding the offset
3. Controlling EIP
4. Finding bad characters
5. Finding return address (JMP ESP)
6. Generating shellcode
7. Gaining shell access

## Practical Exercises

### Exercise 1: Information Gathering
1. Perform passive reconnaissance on a target domain
2. Document all findings (subdomains, IPs, emails, etc.)
3. Create a network map of the target

### Exercise 2: Vulnerability Scanning
1. Use Nmap to scan a target machine
2. Identify open ports and services
3. Research potential vulnerabilities for the found services

### Exercise 3: Web Application Testing
1. Set up OWASP Juice Shop
2. Identify and exploit at least 5 different vulnerabilities
3. Document each finding with proof of concept

### Exercise 4: Basic Exploit Development
1. Download and compile the vulnerable program above
2. Crash the program with a buffer overflow
3. Develop a working exploit to gain shell access

## Additional Resources
- [MITRE ATT&CK Framework](https://attack.mitre.org/)
- [OWASP Testing Guide](https://owasp.org/www-project-web-security-testing-guide/)
- [Hack The Box Academy](https://academy.hackthebox.com/)
- [VulnHub](https://www.vulnhub.com/)

## Next Steps
After completing this level, you should understand:
- The cyber kill chain and attack lifecycle
- Information gathering techniques
- Vulnerability assessment methods
- Basic exploit development concepts

Proceed to [Level 3: Tools and Exploitation](../Level%203%20-%20Tools%20and%20Exploitation/README.md) when ready.
