# Reconnaissance & OSINT - Practice Questions

## Section 1: Passive Reconnaissance (15 Questions)

### Question 1
What command performs a WHOIS lookup on a domain?

**Answer:**
```bash
whois example.com
```

---

### Question 2
How do you query all DNS records for a domain using dig?

**Answer:**
```bash
dig example.com ANY
```

---

### Question 3
What command attempts a DNS zone transfer?

**Answer:**
```bash
dig axfr @ns1.example.com example.com
```

---

### Question 4
How do you use theHarvester to search for emails using multiple sources?

**Answer:**
```bash
theharvester -d example.com -b google,bing,linkedin
```

---

### Question 5
What Google dork finds PDF files on a specific domain?

**Answer:**
```
site:example.com filetype:pdf
```

---

### Question 6
How do you enumerate subdomains using Sublist3r?

**Answer:**
```bash
sublist3r -d example.com
```

---

### Question 7
What command extracts metadata from an image using ExifTool?

**Answer:**
```bash
exiftool image.jpg
```

---

### Question 8
How do you search Shodan for all Apache servers in the US?

**Answer:**
```
apache country:US
```

---

### Question 9
What website allows you to search certificate transparency logs?

**Answer:**
crt.sh (https://crt.sh/)

---

### Question 10
How do you use DNSRecon for subdomain brute forcing?

**Answer:**
```bash
dnsrecon -d example.com -t brt -D /usr/share/wordlists/subdomains.txt
```

---

### Question 11
What Google dork finds login pages on a specific site?

**Answer:**
```
site:example.com inurl:login
```

---

### Question 12
How do you use Amass in passive mode only?

**Answer:**
```bash
amass enum -passive -d example.com
```

---

### Question 13
What command queries MX records for a domain?

**Answer:**
```bash
dig example.com MX
```
or
```bash
nslookup -type=MX example.com
```

---

### Question 14
How do you remove metadata from an image using ExifTool?

**Answer:**
```bash
exiftool -all= image.jpg
```

---

### Question 15
What Shodan filter finds devices vulnerable to a specific CVE?

**Answer:**
```
vuln:CVE-2021-44228
```

---

## Section 2: Active Reconnaissance (15 Questions)

### Question 16
What Nmap command performs a ping sweep on a network?

**Answer:**
```bash
nmap -sn 192.168.1.0/24
```

---

### Question 17
How do you scan all ports on a target using Nmap?

**Answer:**
```bash
nmap -p- 192.168.1.10
```

---

### Question 18
What Nmap scan type is considered "stealth"?

**Answer:**
TCP SYN scan (-sS)
```bash
nmap -sS 192.168.1.10
```

---

### Question 19
How do you perform service version detection with Nmap?

**Answer:**
```bash
nmap -sV 192.168.1.10
```

---

### Question 20
What command runs an aggressive Nmap scan?

**Answer:**
```bash
nmap -A 192.168.1.10
```

---

### Question 21
How do you use Gobuster to find directories with specific extensions?

**Answer:**
```bash
gobuster dir -u http://example.com -w wordlist.txt -x php,html,txt
```

---

### Question 22
What Nmap timing template is fastest?

**Answer:**
T5 (Insane)
```bash
nmap -T5 192.168.1.10
```

---

### Question 23
How do you scan UDP ports with Nmap?

**Answer:**
```bash
nmap -sU 192.168.1.10
```

---

### Question 24
What command runs Nmap's default scripts?

**Answer:**
```bash
nmap -sC 192.168.1.10
```
or
```bash
nmap --script=default 192.168.1.10
```

---

### Question 25
How do you use Nikto to scan a web server?

**Answer:**
```bash
nikto -h http://example.com
```

---

### Question 26
What Nmap command saves output in all formats?

**Answer:**
```bash
nmap -oA scan_results 192.168.1.10
```

---

### Question 27
How do you perform OS detection with Nmap?

**Answer:**
```bash
nmap -O 192.168.1.10
```

---

### Question 28
What command uses Dirb for directory brute forcing?

**Answer:**
```bash
dirb http://example.com /usr/share/wordlists/dirb/common.txt
```

---

### Question 29
How do you run Nmap vulnerability scripts?

**Answer:**
```bash
nmap --script=vuln 192.168.1.10
```

---

### Question 30
What WhatWeb command performs an aggressive scan?

**Answer:**
```bash
whatweb -a 3 example.com
```

---

## Section 3: Service Enumeration (10 Questions)

### Question 31
How do you enumerate SMTP users using smtp-user-enum with VRFY method?

**Answer:**
```bash
smtp-user-enum -M VRFY -U users.txt -t mail.example.com
```

---

### Question 32
What command performs basic SNMP enumeration?

**Answer:**
```bash
snmpwalk -v 2c -c public 192.168.1.1
```

---

### Question 33
How do you connect to an SMTP server using telnet?

**Answer:**
```bash
telnet mail.example.com 25
```

---

### Question 34
What SMTP command verifies if a user exists?

**Answer:**
```
VRFY username
```

---

### Question 35
How do you enumerate running processes via SNMP?

**Answer:**
```bash
snmpwalk -v 2c -c public 192.168.1.1 hrSWRunName
```

---

### Question 36
What tool scans for SNMP community strings?

**Answer:**
onesixtyone
```bash
onesixtyone -c community.txt -i hosts.txt
```

---

### Question 37
How do you use DNSEnum for DNS enumeration?

**Answer:**
```bash
dnsenum example.com
```

---

### Question 38
What Nmap script enumerates SMB shares?

**Answer:**
```bash
nmap --script=smb-enum-shares 192.168.1.10
```

---

### Question 39
How do you enumerate HTTP methods using Nmap?

**Answer:**
```bash
nmap --script=http-methods 192.168.1.10
```

---

### Question 40
What command uses Fierce for DNS scanning?

**Answer:**
```bash
fierce --domain example.com
```

---

## Section 4: OSINT Tools & Frameworks (10 Questions)

### Question 41
What command starts Recon-ng?

**Answer:**
```bash
recon-ng
```

---

### Question 42
How do you create a new workspace in Recon-ng?

**Answer:**
```bash
workspaces create workspace_name
```

---

### Question 43
What command installs a module in Recon-ng?

**Answer:**
```bash
marketplace install module_name
```

---

### Question 44
How do you load a module in Recon-ng?

**Answer:**
```bash
modules load module_path
```

---

### Question 45
What tool provides visual link analysis for OSINT?

**Answer:**
Maltego

---

### Question 46
How do you start SpiderFoot web interface?

**Answer:**
```bash
spiderfoot -l 127.0.0.1:5001
```

---

### Question 47
What website provides a comprehensive OSINT framework?

**Answer:**
https://osintframework.com/

---

### Question 48
How do you search for certificates on crt.sh using curl?

**Answer:**
```bash
curl -s "https://crt.sh/?q=%.example.com&output=json"
```

---

### Question 49
What tool is used for automated subdomain takeover detection?

**Answer:**
subjack or SubOver
```bash
subjack -w subdomains.txt -t 100 -timeout 30 -o results.txt
```

---

### Question 50
How do you use Assetfinder to find subdomains?

**Answer:**
```bash
assetfinder --subs-only example.com
```

---

## Section 5: Practical Scenarios (10 Questions)

### Question 51
You want to find all subdomains of example.com. List 3 tools you would use.

**Answer:**
1. Sublist3r: `sublist3r -d example.com`
2. Amass: `amass enum -d example.com`
3. Assetfinder: `assetfinder --subs-only example.com`

---

### Question 52
How would you check if a website is vulnerable to subdomain takeover?

**Answer:**
1. Enumerate all subdomains
2. Check DNS records: `dig subdomain.example.com`
3. Look for CNAME records pointing to external services
4. Check if the external service is unclaimed
5. Use automated tools: `subjack -w subdomains.txt`

---

### Question 53
What steps would you take to enumerate a web application?

**Answer:**
1. Identify web server and technologies: `whatweb example.com`
2. Scan for vulnerabilities: `nikto -h http://example.com`
3. Brute force directories: `gobuster dir -u http://example.com -w wordlist.txt`
4. Check robots.txt and sitemap.xml
5. Run Nmap scripts: `nmap --script=http-enum example.com`

---

### Question 54
How do you find email addresses for a target organization?

**Answer:**
1. Use theHarvester: `theharvester -d example.com -b all`
2. Search LinkedIn for employees
3. Check company website and contact pages
4. Use Hunter.io
5. Search GitHub for commits
6. Check WHOIS records

---

### Question 55
What information can you gather from DNS records?

**Answer:**
- A records: IP addresses
- MX records: Mail servers
- NS records: Name servers
- TXT records: SPF, DKIM, DMARC policies
- CNAME records: Aliases and subdomains
- SOA records: Zone authority information

---

### Question 56
How would you perform reconnaissance on a network range?

**Answer:**
1. Host discovery: `nmap -sn 192.168.1.0/24`
2. Port scanning: `nmap -p- 192.168.1.0/24`
3. Service detection: `nmap -sV 192.168.1.0/24`
4. OS detection: `nmap -O 192.168.1.0/24`
5. Vulnerability scanning: `nmap --script=vuln 192.168.1.0/24`

---

### Question 57
What Google dorks would you use to find sensitive information?

**Answer:**
- Login pages: `site:example.com inurl:login`
- Admin panels: `site:example.com inurl:admin`
- Config files: `site:example.com filetype:env`
- Database dumps: `site:example.com filetype:sql`
- Directory listings: `site:example.com intitle:"index of"`
- Error messages: `site:example.com intext:"sql syntax error"`

---

### Question 58
How do you identify technologies used by a website?

**Answer:**
1. Wappalyzer browser extension
2. WhatWeb: `whatweb example.com`
3. BuiltWith: https://builtwith.com/
4. Nmap scripts: `nmap --script=http-headers example.com`
5. Manual inspection of HTTP headers and source code
6. Check meta tags and comments in HTML

---

### Question 59
What are the key differences between passive and active reconnaissance?

**Answer:**
**Passive Reconnaissance:**
- No direct interaction with target
- Uses public information sources
- Lower risk of detection
- Examples: WHOIS, Google dorking, social media

**Active Reconnaissance:**
- Direct interaction with target
- Sends packets to target systems
- Higher risk of detection
- Examples: Port scanning, directory brute forcing, service enumeration

---

### Question 60
How would you create a comprehensive reconnaissance report?

**Answer:**
**Report Structure:**
1. Executive Summary
2. Scope and Methodology
3. Findings:
   - Domains and subdomains
   - IP addresses and network ranges
   - Open ports and services
   - Email addresses and users
   - Technologies identified
   - Potential vulnerabilities
4. Attack Surface Analysis
5. Recommendations
6. Appendices (raw data, tool outputs)

---

## Summary

**Total Questions: 60**

- Section 1: Passive Reconnaissance (15 questions)
- Section 2: Active Reconnaissance (15 questions)
- Section 3: Service Enumeration (10 questions)
- Section 4: OSINT Tools & Frameworks (10 questions)
- Section 5: Practical Scenarios (10 questions)

**Practice Tips:**
1. Set up a home lab with vulnerable VMs
2. Practice on legal targets (HackTheBox, TryHackMe)
3. Always get permission before testing
4. Document your methodology
5. Use multiple tools to verify findings
6. Create your own wordlists
7. Automate repetitive tasks with scripts

**Scoring Guide:**
- 54-60 correct: Excellent! Master level
- 45-53 correct: Very Good! Ready for real assessments
- 36-44 correct: Good! More practice needed
- 27-35 correct: Fair. Review weak areas
- Below 27: Study the material and retry

**Legal Reminder:**
- Only test systems you own or have written permission to test
- Unauthorized reconnaissance can be illegal
- Respect privacy and data protection laws
- Follow responsible disclosure practices

**Next Steps:**
- Complete all exercises in the main README
- Practice on bug bounty programs
- Join OSINT CTF competitions
- Move to Module 2: Scanning & Enumeration
