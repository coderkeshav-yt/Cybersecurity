# Module 1: Reconnaissance & OSINT

## Table of Contents
1. [Introduction to Reconnaissance](#introduction-to-reconnaissance)
2. [Passive Reconnaissance](#passive-reconnaissance)
3. [Active Reconnaissance](#active-reconnaissance)
4. [OSINT Framework](#osint-framework)
5. [Practical Exercises](#practical-exercises)

---

## Introduction to Reconnaissance

### What is Reconnaissance?

**Definition:** The process of gathering information about a target system, network, or organization before launching an attack.

**Types:**
- **Passive Reconnaissance**: Gathering information without directly interacting with the target
- **Active Reconnaissance**: Directly interacting with the target system to gather information

**Goals:**
- Identify potential entry points
- Map the attack surface
- Discover vulnerabilities
- Understand the target's infrastructure
- Gather credentials and sensitive information

### Legal and Ethical Considerations

**CRITICAL: Always obtain written permission before conducting reconnaissance on any system you don't own!**

**Legal Frameworks:**
- Computer Fraud and Abuse Act (CFAA) - USA
- Computer Misuse Act - UK
- General Data Protection Regulation (GDPR) - EU
- Local cybercrime laws

**Ethical Guidelines:**
- Only test systems you have explicit permission to test
- Respect scope limitations
- Report findings responsibly
- Protect discovered data
- Follow rules of engagement

---

## Passive Reconnaissance

### 1. WHOIS Lookups

**Purpose:** Gather domain registration information

**Tools and Commands:**
```bash
# Basic WHOIS lookup
whois example.com

# WHOIS with specific server
whois -h whois.verisign-grs.com example.com

# Reverse WHOIS (find domains by registrant)
# Use online tools like ViewDNS.info or DomainTools
```

**Information Gathered:**
- Registrant name and organization
- Registration and expiration dates
- Name servers
- Registrar information
- Contact emails and phone numbers
- Administrative and technical contacts

**Example Output Analysis:**
```
Domain Name: EXAMPLE.COM
Registrar: Example Registrar, Inc.
Creation Date: 1995-08-14
Expiration Date: 2025-08-13
Name Servers: ns1.example.com, ns2.example.com
Registrant Organization: Example Corporation
Registrant Email: admin@example.com
```

### 2. DNS Enumeration

**Purpose:** Discover subdomains, mail servers, and DNS records

**DNS Record Types:**
- **A**: IPv4 address
- **AAAA**: IPv6 address
- **MX**: Mail exchange servers
- **NS**: Name servers
- **TXT**: Text records (SPF, DKIM, DMARC)
- **CNAME**: Canonical name (alias)
- **SOA**: Start of authority
- **PTR**: Pointer (reverse DNS)

**Tools and Commands:**
```bash
# Basic DNS lookup
nslookup example.com
dig example.com

# Query specific record types
dig example.com A
dig example.com MX
dig example.com NS
dig example.com TXT
dig example.com ANY

# Reverse DNS lookup
dig -x 192.168.1.1

# DNS zone transfer (often blocked)
dig axfr @ns1.example.com example.com

# Using host command
host example.com
host -t MX example.com
host -t NS example.com
```

**DNSRecon - Automated DNS Enumeration:**
```bash
# Basic enumeration
dnsrecon -d example.com

# Zone transfer attempt
dnsrecon -d example.com -t axfr

# Subdomain brute force
dnsrecon -d example.com -t brt -D /usr/share/wordlists/subdomains.txt

# Reverse lookup on IP range
dnsrecon -r 192.168.1.0/24

# Cache snooping
dnsrecon -d example.com -t snoop
```

### 3. Subdomain Enumeration

**Purpose:** Discover hidden subdomains that may have vulnerabilities

**Sublist3r - Subdomain Discovery:**
```bash
# Basic subdomain enumeration
sublist3r -d example.com

# Save results to file
sublist3r -d example.com -o subdomains.txt

# Enable brute force
sublist3r -d example.com -b

# Use specific search engines
sublist3r -d example.com -e google,bing,yahoo

# Verbose output
sublist3r -d example.com -v
```

**Amass - Advanced Subdomain Enumeration:**
```bash
# Basic enumeration
amass enum -d example.com

# Passive mode only
amass enum -passive -d example.com

# Active enumeration with brute force
amass enum -active -d example.com -brute

# Output to file
amass enum -d example.com -o amass_results.txt

# Use all data sources
amass enum -d example.com -src
```

**Other Subdomain Tools:**
```bash
# Assetfinder
assetfinder --subs-only example.com

# Findomain
findomain -t example.com

# Subfinder
subfinder -d example.com

# Knock
knockpy example.com
```

### 4. Email Harvesting

**Purpose:** Collect email addresses for phishing campaigns or password spraying

**theHarvester - Email and Subdomain Harvesting:**
```bash
# Basic search
theharvester -d example.com -b google

# Multiple sources
theharvester -d example.com -b google,bing,linkedin,twitter

# Limit results
theharvester -d example.com -l 500 -b all

# Save to HTML report
theharvester -d example.com -b all -f report

# DNS brute force
theharvester -d example.com -b google -c

# Virtual host verification
theharvester -d example.com -b google -v
```

**Data Sources:**
- google: Google search
- bing: Bing search
- linkedin: LinkedIn profiles
- twitter: Twitter mentions
- hunter: Hunter.io API
- shodan: Shodan search
- censys: Censys search

**Email Pattern Analysis:**
```
Common patterns discovered:
- firstname.lastname@example.com
- firstnamelastname@example.com
- f.lastname@example.com
- firstname@example.com
```

### 5. Google Dorking (Google Hacking)

**Purpose:** Use advanced Google search operators to find sensitive information

**Basic Operators:**
```
site:        Search within specific site
filetype:    Search for specific file types
intitle:     Search in page title
inurl:       Search in URL
intext:      Search in page text
cache:       View Google's cached version
link:        Find pages linking to URL
related:     Find related websites
```

**Security-Focused Dorks:**
```bash
# Find login pages
site:example.com inurl:login
site:example.com intitle:"login"

# Find admin panels
site:example.com inurl:admin
site:example.com intitle:"admin panel"

# Find exposed files
site:example.com filetype:pdf
site:example.com filetype:xls
site:example.com filetype:doc
site:example.com filetype:sql
site:example.com filetype:log

# Find configuration files
site:example.com filetype:env
site:example.com filetype:config
site:example.com inurl:wp-config.php

# Find database dumps
site:example.com filetype:sql "INSERT INTO"
site:example.com filetype:sql "CREATE TABLE"

# Find exposed directories
site:example.com intitle:"index of"
site:example.com intitle:"directory listing"

# Find error messages
site:example.com intext:"sql syntax error"
site:example.com intext:"warning: mysql"

# Find vulnerable parameters
site:example.com inurl:id=
site:example.com inurl:page=
site:example.com inurl:file=
```

**Google Hacking Database (GHDB):**
- Visit: https://www.exploit-db.com/google-hacking-database
- Categories: Footholds, Files containing usernames, Sensitive directories, Web server detection, Vulnerable files, etc.

### 6. Social Media Intelligence (SOCMINT)

**Purpose:** Gather information from social media platforms

**LinkedIn Reconnaissance:**
```
Information to gather:
- Employee names and positions
- Organizational structure
- Technologies used
- Recent projects
- Contact information
- Professional connections
```

**Tools:**
- **LinkedIn Sales Navigator**: Advanced search
- **theHarvester**: LinkedIn module
- **Maltego**: Visual link analysis
- **Social-Searcher**: Multi-platform search

**Twitter/X Intelligence:**
```bash
# Search operators
from:username          # Tweets from user
to:username           # Tweets to user
@username             # Mentions of user
#hashtag              # Hashtag search
since:2024-01-01      # Tweets since date
until:2024-12-31      # Tweets until date
filter:links          # Tweets with links
filter:images         # Tweets with images
```

**Facebook/Instagram:**
- Profile information
- Check-ins and locations
- Photo metadata
- Friend/follower lists
- Group memberships

### 7. Website Reconnaissance

**Wayback Machine (Archive.org):**
```
Purpose: View historical versions of websites
URL: https://web.archive.org/

Use cases:
- Find old vulnerabilities
- Discover removed pages
- View previous configurations
- Find old employee information
- Locate deprecated APIs
```

**Built With - Technology Profiler:**
```
Purpose: Identify technologies used by website
URL: https://builtwith.com/

Information gathered:
- CMS (WordPress, Drupal, Joomla)
- Web servers (Apache, Nginx, IIS)
- Programming languages
- Analytics tools
- CDN providers
- SSL certificates
```

**Wappalyzer - Technology Detection:**
```bash
# Browser extension or CLI tool
wappalyzer https://example.com

Technologies detected:
- Web frameworks
- JavaScript libraries
- Payment processors
- Marketing tools
- Security tools
```

### 8. Metadata Extraction

**Purpose:** Extract hidden information from documents and images

**ExifTool - Metadata Extraction:**
```bash
# Extract metadata from image
exiftool image.jpg

# Extract from PDF
exiftool document.pdf

# Extract from multiple files
exiftool *.jpg

# Remove metadata
exiftool -all= image.jpg

# Extract GPS coordinates
exiftool -gps:all image.jpg
```

**FOCA - Metadata Analysis Tool:**
```
Purpose: Extract metadata from documents found on websites
Platform: Windows

Features:
- Automatic document download
- Metadata extraction
- User enumeration
- Software version detection
- Network information
```

**Information in Metadata:**
- Author names
- Software versions
- Creation/modification dates
- GPS coordinates
- Camera information
- Internal file paths
- Usernames
- Organization names

### 9. Shodan - The Search Engine for Internet-Connected Devices

**Purpose:** Find exposed devices, services, and vulnerabilities

**Basic Shodan Searches:**
```bash
# Search by IP
shodan host 8.8.8.8

# Search by organization
org:"Example Corp"

# Search by hostname
hostname:example.com

# Search by port
port:22
port:3389

# Search by service
apache
nginx
mysql

# Search by country
country:US
country:GB

# Search by city
city:"New York"

# Search by OS
os:"Windows 10"
os:"Ubuntu"
```

**Advanced Shodan Filters:**
```bash
# Find webcams
webcam country:US

# Find industrial control systems
scada

# Find databases
mongodb
mysql
postgresql

# Find vulnerable systems
vuln:CVE-2021-44228  # Log4Shell

# Find default credentials
default password

# Combine filters
apache country:US port:443 ssl.cert.subject.cn:example.com
```

### 10. Certificate Transparency Logs

**Purpose:** Discover subdomains through SSL certificate logs

**crt.sh - Certificate Search:**
```
URL: https://crt.sh/
Search: %.example.com

Returns:
- All issued certificates
- Subdomains
- Certificate authorities
- Expiration dates
```

**Command Line Tools:**
```bash
# Using curl and jq
curl -s "https://crt.sh/?q=%.example.com&output=json" | jq -r '.[].name_value' | sort -u

# Using certspotter
certspotter example.com

# Using ctfr
ctfr -d example.com
```

---

## Active Reconnaissance

### 1. Network Scanning with Nmap

**Purpose:** Discover live hosts, open ports, and running services

**Basic Nmap Scans:**
```bash
# Ping sweep (host discovery)
nmap -sn 192.168.1.0/24

# Basic port scan
nmap 192.168.1.10

# Scan specific ports
nmap -p 80,443,22 192.168.1.10

# Scan port range
nmap -p 1-1000 192.168.1.10

# Scan all ports
nmap -p- 192.168.1.10

# Fast scan (top 100 ports)
nmap -F 192.168.1.10
```

**Service and Version Detection:**
```bash
# Service version detection
nmap -sV 192.168.1.10

# OS detection
nmap -O 192.168.1.10

# Aggressive scan (OS, version, scripts, traceroute)
nmap -A 192.168.1.10

# Default scripts and version detection
nmap -sC -sV 192.168.1.10
```

**Scan Types:**
```bash
# TCP SYN scan (stealth scan) - default
nmap -sS 192.168.1.10

# TCP connect scan
nmap -sT 192.168.1.10

# UDP scan
nmap -sU 192.168.1.10

# ACK scan (firewall detection)
nmap -sA 192.168.1.10

# Window scan
nmap -sW 192.168.1.10

# Null scan
nmap -sN 192.168.1.10

# FIN scan
nmap -sF 192.168.1.10

# Xmas scan
nmap -sX 192.168.1.10
```

**Timing and Performance:**
```bash
# Timing templates (0-5)
nmap -T0 192.168.1.10  # Paranoid (IDS evasion)
nmap -T1 192.168.1.10  # Sneaky
nmap -T2 192.168.1.10  # Polite
nmap -T3 192.168.1.10  # Normal (default)
nmap -T4 192.168.1.10  # Aggressive
nmap -T5 192.168.1.10  # Insane

# Custom timing
nmap --min-rate 1000 192.168.1.10
nmap --max-rate 5000 192.168.1.10
```

**Output Formats:**
```bash
# Normal output
nmap -oN scan.txt 192.168.1.10

# XML output
nmap -oX scan.xml 192.168.1.10

# Grepable output
nmap -oG scan.gnmap 192.168.1.10

# All formats
nmap -oA scan 192.168.1.10

# Script kiddie output
nmap -oS scan.txt 192.168.1.10
```

**NSE Scripts:**
```bash
# Run default scripts
nmap -sC 192.168.1.10

# Run specific script
nmap --script=http-title 192.168.1.10

# Run script category
nmap --script=vuln 192.168.1.10

# Multiple scripts
nmap --script=http-enum,http-headers 192.168.1.10

# Script with arguments
nmap --script=http-brute --script-args userdb=users.txt,passdb=pass.txt 192.168.1.10

# List available scripts
nmap --script-help=all
```

### 2. Web Application Reconnaissance

**Nikto - Web Server Scanner:**
```bash
# Basic scan
nikto -h http://example.com

# Scan with specific port
nikto -h http://example.com:8080

# SSL scan
nikto -h https://example.com -ssl

# Save output
nikto -h http://example.com -o nikto_results.txt

# Tune scan (specific tests)
nikto -h http://example.com -Tuning 123456789

# Use proxy
nikto -h http://example.com -useproxy http://proxy:8080
```

**WhatWeb - Website Fingerprinting:**
```bash
# Basic scan
whatweb example.com

# Aggressive scan
whatweb -a 3 example.com

# Verbose output
whatweb -v example.com

# Scan multiple URLs
whatweb -i urls.txt

# Output to JSON
whatweb --log-json=results.json example.com
```

**Directory Brute Forcing:**
```bash
# Gobuster - Directory/file brute forcing
gobuster dir -u http://example.com -w /usr/share/wordlists/dirb/common.txt

# With extensions
gobuster dir -u http://example.com -w wordlist.txt -x php,html,txt

# With status codes
gobuster dir -u http://example.com -w wordlist.txt -s 200,204,301,302,307,401,403

# Dirb - Alternative tool
dirb http://example.com /usr/share/wordlists/dirb/common.txt

# Dirsearch - Python-based
dirsearch -u http://example.com -e php,html,js

# Feroxbuster - Rust-based (fast)
feroxbuster -u http://example.com -w wordlist.txt
```

### 3. DNS Interrogation

**DNSEnum - DNS Enumeration:**
```bash
# Basic enumeration
dnsenum example.com

# With specific DNS server
dnsenum --dnsserver 8.8.8.8 example.com

# Zone transfer attempt
dnsenum --enum example.com

# Brute force subdomains
dnsenum --subfile subdomains.txt example.com

# Reverse lookup
dnsenum --reverse example.com
```

**Fierce - DNS Scanner:**
```bash
# Basic scan
fierce --domain example.com

# With specific DNS server
fierce --domain example.com --dns-servers 8.8.8.8

# Subdomain brute force
fierce --domain example.com --subdomain-file subdomains.txt

# Wide scan
fierce --domain example.com --wide
```

### 4. SMTP Enumeration

**Purpose:** Enumerate email users and gather mail server information

**SMTP Commands:**
```bash
# Connect to SMTP server
telnet mail.example.com 25

# SMTP commands
HELO attacker.com
VRFY root
VRFY admin
EXPN root
MAIL FROM: test@example.com
RCPT TO: user@example.com
```

**smtp-user-enum - Automated SMTP Enumeration:**
```bash
# VRFY method
smtp-user-enum -M VRFY -U users.txt -t mail.example.com

# EXPN method
smtp-user-enum -M EXPN -U users.txt -t mail.example.com

# RCPT method
smtp-user-enum -M RCPT -U users.txt -t mail.example.com

# With specific port
smtp-user-enum -M VRFY -U users.txt -t mail.example.com -p 25
```

### 5. SNMP Enumeration

**Purpose:** Gather information from SNMP-enabled devices

**SNMPWalk - SNMP Enumeration:**
```bash
# Basic snmpwalk
snmpwalk -v 2c -c public 192.168.1.1

# Enumerate system information
snmpwalk -v 2c -c public 192.168.1.1 system

# Enumerate network interfaces
snmpwalk -v 2c -c public 192.168.1.1 interfaces

# Enumerate running processes
snmpwalk -v 2c -c public 192.168.1.1 hrSWRunName

# Enumerate installed software
snmpwalk -v 2c -c public 192.168.1.1 hrSWInstalledName
```

**Onesixtyone - SNMP Scanner:**
```bash
# Scan single host
onesixtyone 192.168.1.1 public

# Scan multiple hosts
onesixtyone -c community.txt -i hosts.txt

# Output to file
onesixtyone -c community.txt -i hosts.txt -o results.txt
```

---

## OSINT Framework

### Maltego - Visual Intelligence Tool

**Purpose:** Visual link analysis and data mining

**Transforms:**
- Domain to IP
- IP to Location
- Email to Person
- Person to Social Media
- Company to Employees
- Website to Technologies

**Use Cases:**
- Map organizational structure
- Discover relationships
- Track digital footprint
- Investigate infrastructure

### Recon-ng - Reconnaissance Framework

**Purpose:** Modular reconnaissance framework

**Basic Commands:**
```bash
# Start recon-ng
recon-ng

# Create workspace
workspaces create example_recon

# List modules
marketplace search

# Install module
marketplace install recon/domains-hosts/google_site_web

# Load module
modules load recon/domains-hosts/google_site_web

# Set options
options set SOURCE example.com

# Run module
run

# Show results
show hosts
show contacts
show domains
```

**Popular Modules:**
- recon/domains-hosts/google_site_web
- recon/domains-hosts/bing_domain_web
- recon/hosts-hosts/resolve
- recon/contacts-contacts/pgp_search
- recon/companies-contacts/linkedin_auth

### SpiderFoot - OSINT Automation

**Purpose:** Automated OSINT collection

**Features:**
- 200+ modules
- Web interface
- Correlation engine
- Visualization
- Export capabilities

**Installation and Usage:**
```bash
# Install
pip3 install spiderfoot

# Run web interface
spiderfoot -l 127.0.0.1:5001

# CLI scan
spiderfoot -s example.com -t IP_ADDRESS,DOMAIN_NAME
```

---

## Practical Exercises

### Exercise 1: Passive Reconnaissance Challenge
**Target:** Choose a bug bounty program from HackerOne or BugCrowd

**Tasks:**
1. Perform WHOIS lookup and document findings
2. Enumerate DNS records (A, MX, NS, TXT)
3. Discover at least 10 subdomains using multiple tools
4. Harvest email addresses and identify email patterns
5. Use Google dorking to find interesting files
6. Check Shodan for exposed services
7. Search certificate transparency logs
8. Create a comprehensive report

**Deliverables:**
- List of subdomains
- Email addresses and patterns
- Interesting files found
- Exposed services
- Technology stack
- Potential attack surface

### Exercise 2: Active Reconnaissance Lab
**Setup:** Use Metasploitable 2 or DVWA in a VM

**Tasks:**
1. Perform host discovery on the network
2. Scan all ports and identify services
3. Enumerate web directories
4. Perform SMTP user enumeration
5. Check for SNMP community strings
6. Run vulnerability scripts
7. Document all findings

**Deliverables:**
- Network diagram
- Port scan results
- Service versions
- Discovered directories
- Enumerated users
- Potential vulnerabilities

### Exercise 3: OSINT Investigation
**Scenario:** Investigate a fictional company

**Tasks:**
1. Find employee names and positions (LinkedIn)
2. Discover email addresses
3. Map organizational structure
4. Identify technologies used
5. Find social media presence
6. Locate physical addresses
7. Discover business relationships
8. Create visual map using Maltego

**Deliverables:**
- Employee list with positions
- Email addresses
- Organizational chart
- Technology inventory
- Social media profiles
- Maltego graph

### Exercise 4: Subdomain Takeover Hunt
**Target:** Bug bounty program

**Tasks:**
1. Enumerate all subdomains
2. Check DNS records for each subdomain
3. Identify dangling DNS records
4. Test for subdomain takeover
5. Document vulnerable subdomains

**Tools:**
- Sublist3r, Amass, Assetfinder
- dig, nslookup
- subjack, SubOver

**Deliverables:**
- Complete subdomain list
- Dangling DNS records
- Takeover-vulnerable subdomains
- Proof of concept

---

## Additional Resources

**Books:**
- "Open Source Intelligence Techniques" by Michael Bazzell
- "The Art of Invisibility" by Kevin Mitnick
- "OSINT Handbook" by i-intelligence

**Websites:**
- OSINT Framework: https://osintframework.com/
- IntelTechniques: https://inteltechniques.com/
- Bellingcat: https://www.bellingcat.com/

**Tools Collections:**
- Kali Linux: Pre-installed reconnaissance tools
- OSINT VM: https://www.tracelabs.org/initiatives/osint-vm
- Buscador VM: OSINT-focused Linux distribution

**Practice Platforms:**
- TryHackMe: Reconnaissance rooms
- HackTheBox: OSINT challenges
- Trace Labs: OSINT CTF events

---

## Summary

Reconnaissance is the foundation of any security assessment. Master these techniques:

**Passive Reconnaissance:**
- WHOIS and DNS enumeration
- Subdomain discovery
- Email harvesting
- Google dorking
- Social media intelligence
- Metadata extraction
- Shodan searches

**Active Reconnaissance:**
- Network scanning with Nmap
- Web application scanning
- Directory brute forcing
- Service enumeration
- SMTP/SNMP enumeration

**Key Principles:**
- Always get permission first
- Document everything
- Use multiple tools for verification
- Respect privacy and laws
- Think like an attacker

**Next Steps:**
Proceed to [Module 2: Scanning & Enumeration](../2-Scanning-Enumeration/README.md) to learn advanced scanning techniques.
