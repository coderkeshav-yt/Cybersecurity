# Module 2: Scanning & Enumeration

## Table of Contents
1. [Introduction to Scanning](#introduction-to-scanning)
2. [Port Scanning Techniques](#port-scanning-techniques)
3. [Service Enumeration](#service-enumeration)
4. [Network Mapping](#network-mapping)
5. [Vulnerability Scanning](#vulnerability-scanning)
6. [Practical Exercises](#practical-exercises)

---

## Introduction to Scanning

### What is Scanning?

**Definition:** The process of identifying live hosts, open ports, running services, and potential vulnerabilities on a network.

**Scanning vs. Enumeration:**
- **Scanning**: Identifying what's there (hosts, ports, services)
- **Enumeration**: Extracting detailed information (users, shares, configurations)

**Goals:**
- Identify live systems
- Discover open ports
- Determine service versions
- Detect operating systems
- Find potential vulnerabilities
- Map network topology

### Types of Scans

**Network Scanning:**
- Host discovery
- Port scanning
- Service detection
- OS fingerprinting

**Vulnerability Scanning:**
- Known vulnerability detection
- Configuration auditing
- Patch level assessment
- Compliance checking

**Web Application Scanning:**
- Directory enumeration
- Parameter discovery
- Technology detection
- Vulnerability identification

---

## Port Scanning Techniques

### TCP Scanning Methods

#### 1. TCP Connect Scan (-sT)

**How it works:**
- Completes full three-way handshake
- SYN → SYN/ACK → ACK
- Most reliable but easily detected
- Doesn't require root privileges

**Usage:**
```bash
# Basic TCP connect scan
nmap -sT 192.168.1.10

# Scan specific ports
nmap -sT -p 80,443,22 192.168.1.10

# Scan with service detection
nmap -sT -sV 192.168.1.10
```

**Advantages:**
- Works without root/admin privileges
- Most accurate results
- Works through most firewalls

**Disadvantages:**
- Easily logged and detected
- Slower than SYN scan
- Creates full connections

#### 2. TCP SYN Scan (-sS) - Stealth Scan

**How it works:**
- Sends SYN packet
- Receives SYN/ACK (port open) or RST (port closed)
- Sends RST instead of ACK (doesn't complete handshake)
- Called "half-open" scanning

**Usage:**
```bash
# SYN scan (requires root)
sudo nmap -sS 192.168.1.10

# Fast SYN scan
sudo nmap -sS -T4 192.168.1.10

# SYN scan with version detection
sudo nmap -sS -sV 192.168.1.10
```

**Advantages:**
- Faster than TCP connect
- Less likely to be logged
- Default Nmap scan type

**Disadvantages:**
- Requires root/admin privileges
- Can be detected by IDS/IPS
- May crash unstable services

#### 3. TCP ACK Scan (-sA)

**How it works:**
- Sends ACK packet to port
- Used to map firewall rules
- Determines if port is filtered

**Usage:**
```bash
# ACK scan for firewall detection
sudo nmap -sA 192.168.1.10

# ACK scan on specific ports
sudo nmap -sA -p 1-1000 192.168.1.10
```

**Response Analysis:**
- RST response: Port is unfiltered
- No response: Port is filtered
- ICMP unreachable: Port is filtered

#### 4. TCP FIN Scan (-sF)

**How it works:**
- Sends FIN packet
- Open port: No response
- Closed port: RST response
- Bypasses some firewalls

**Usage:**
```bash
# FIN scan
sudo nmap -sF 192.168.1.10

# FIN scan with timing
sudo nmap -sF -T2 192.168.1.10
```

#### 5. TCP NULL Scan (-sN)

**How it works:**
- Sends packet with no flags set
- Open port: No response
- Closed port: RST response

**Usage:**
```bash
# NULL scan
sudo nmap -sN 192.168.1.10
```

#### 6. TCP Xmas Scan (-sX)

**How it works:**
- Sends packet with FIN, PSH, and URG flags
- Named "Xmas" because flags light up like Christmas tree
- Open port: No response
- Closed port: RST response

**Usage:**
```bash
# Xmas scan
sudo nmap -sX 192.168.1.10
```

### UDP Scanning

#### UDP Scan (-sU)

**How it works:**
- Sends UDP packet to port
- Open port: No response or UDP response
- Closed port: ICMP port unreachable
- Very slow due to rate limiting

**Usage:**
```bash
# Basic UDP scan
sudo nmap -sU 192.168.1.10

# Scan common UDP ports
sudo nmap -sU -p 53,67,68,69,123,161,162 192.168.1.10

# Fast UDP scan (top 100 ports)
sudo nmap -sU -F 192.168.1.10

# UDP scan with version detection
sudo nmap -sU -sV 192.168.1.10

# Combined TCP and UDP scan
sudo nmap -sS -sU -p T:80,443,U:53,161 192.168.1.10
```

**Common UDP Services:**
- 53: DNS
- 67/68: DHCP
- 69: TFTP
- 123: NTP
- 161/162: SNMP
- 500: IKE (IPSec)
- 1434: MS-SQL Monitor
- 1900: UPnP

### Advanced Scanning Techniques

#### Idle Scan (-sI)

**How it works:**
- Uses zombie host to scan target
- Extremely stealthy
- Target sees packets from zombie, not attacker

**Usage:**
```bash
# Idle scan using zombie host
sudo nmap -sI zombie_host target_host

# Example
sudo nmap -sI 192.168.1.5 192.168.1.10
```

#### IP Protocol Scan (-sO)

**How it works:**
- Determines which IP protocols are supported
- Tests protocols like TCP, UDP, ICMP, IGMP

**Usage:**
```bash
# IP protocol scan
sudo nmap -sO 192.168.1.10
```

#### FTP Bounce Scan (-b)

**How it works:**
- Uses FTP server to scan other hosts
- Exploits FTP PORT command
- Rarely works on modern systems

**Usage:**
```bash
# FTP bounce scan
nmap -b ftp_server target_host
```

---

## Service Enumeration

### HTTP/HTTPS Enumeration (Port 80/443)

#### Nmap HTTP Scripts

```bash
# HTTP title
nmap --script=http-title 192.168.1.10

# HTTP headers
nmap --script=http-headers 192.168.1.10

# HTTP methods
nmap --script=http-methods 192.168.1.10

# HTTP enum (directories, files)
nmap --script=http-enum 192.168.1.10

# HTTP vulnerabilities
nmap --script=http-vuln* 192.168.1.10

# HTTP authentication
nmap --script=http-auth 192.168.1.10

# HTTP robots.txt
nmap --script=http-robots.txt 192.168.1.10

# HTTP sitemap
nmap --script=http-sitemap-generator 192.168.1.10

# All HTTP scripts
nmap --script=http-* 192.168.1.10
```

#### cURL Enumeration

```bash
# Basic request
curl http://example.com

# View headers
curl -I http://example.com

# Follow redirects
curl -L http://example.com

# Save output
curl -o output.html http://example.com

# Custom user agent
curl -A "Mozilla/5.0" http://example.com

# POST request
curl -X POST -d "param=value" http://example.com

# With authentication
curl -u username:password http://example.com

# Verbose output
curl -v http://example.com

# Test HTTP methods
curl -X OPTIONS http://example.com
curl -X PUT -d "data" http://example.com/file.txt
curl -X DELETE http://example.com/file.txt
```

### SMB Enumeration (Port 139/445)

#### Nmap SMB Scripts

```bash
# SMB OS discovery
nmap --script=smb-os-discovery 192.168.1.10

# SMB security mode
nmap --script=smb-security-mode 192.168.1.10

# SMB enumerate shares
nmap --script=smb-enum-shares 192.168.1.10

# SMB enumerate users
nmap --script=smb-enum-users 192.168.1.10

# SMB enumerate domains
nmap --script=smb-enum-domains 192.168.1.10

# SMB enumerate groups
nmap --script=smb-enum-groups 192.168.1.10

# SMB enumerate sessions
nmap --script=smb-enum-sessions 192.168.1.10

# SMB vulnerabilities
nmap --script=smb-vuln* 192.168.1.10

# All SMB scripts
nmap --script=smb-* 192.168.1.10
```

#### SMBClient

```bash
# List shares
smbclient -L //192.168.1.10 -N

# Connect to share
smbclient //192.168.1.10/share -U username

# Connect anonymously
smbclient //192.168.1.10/share -N

# Execute command
smbclient //192.168.1.10/share -U username -c "ls"
```

#### Enum4linux

```bash
# Basic enumeration
enum4linux 192.168.1.10

# All enumeration
enum4linux -a 192.168.1.10

# User enumeration
enum4linux -U 192.168.1.10

# Share enumeration
enum4linux -S 192.168.1.10

# Password policy
enum4linux -P 192.168.1.10

# Group enumeration
enum4linux -G 192.168.1.10

# With credentials
enum4linux -u username -p password 192.168.1.10
```

### SSH Enumeration (Port 22)

```bash
# SSH version detection
nmap -sV -p 22 192.168.1.10

# SSH supported algorithms
nmap --script=ssh2-enum-algos 192.168.1.10

# SSH host key
nmap --script=ssh-hostkey 192.168.1.10

# SSH authentication methods
nmap --script=ssh-auth-methods 192.168.1.10

# SSH brute force
nmap --script=ssh-brute --script-args userdb=users.txt,passdb=pass.txt 192.168.1.10

# Manual SSH banner grab
nc 192.168.1.10 22

# SSH audit
ssh-audit 192.168.1.10
```

### FTP Enumeration (Port 21)

```bash
# FTP version detection
nmap -sV -p 21 192.168.1.10

# FTP anonymous login
nmap --script=ftp-anon 192.168.1.10

# FTP bounce check
nmap --script=ftp-bounce 192.168.1.10

# FTP brute force
nmap --script=ftp-brute --script-args userdb=users.txt,passdb=pass.txt 192.168.1.10

# Manual FTP connection
ftp 192.168.1.10

# Anonymous FTP login
ftp 192.168.1.10
# Username: anonymous
# Password: anonymous@example.com
```

### SMTP Enumeration (Port 25)

```bash
# SMTP version detection
nmap -sV -p 25 192.168.1.10

# SMTP commands
nmap --script=smtp-commands 192.168.1.10

# SMTP open relay
nmap --script=smtp-open-relay 192.168.1.10

# SMTP user enumeration
nmap --script=smtp-enum-users 192.168.1.10

# SMTP vulnerabilities
nmap --script=smtp-vuln* 192.168.1.10

# Manual SMTP enumeration
nc 192.168.1.10 25
# HELO attacker.com
# VRFY root
# EXPN root
```

### DNS Enumeration (Port 53)

```bash
# DNS version detection
nmap -sV -p 53 192.168.1.10

# DNS brute force
nmap --script=dns-brute example.com

# DNS zone transfer
nmap --script=dns-zone-transfer --script-args dns-zone-transfer.domain=example.com 192.168.1.10

# DNS recursion
nmap --script=dns-recursion 192.168.1.10

# DNS cache snoop
nmap --script=dns-cache-snoop --script-args dns-cache-snoop.domains={google.com,facebook.com} 192.168.1.10
```

### MySQL Enumeration (Port 3306)

```bash
# MySQL version detection
nmap -sV -p 3306 192.168.1.10

# MySQL info
nmap --script=mysql-info 192.168.1.10

# MySQL empty password
nmap --script=mysql-empty-password 192.168.1.10

# MySQL users
nmap --script=mysql-users --script-args mysqluser=root,mysqlpass=password 192.168.1.10

# MySQL databases
nmap --script=mysql-databases --script-args mysqluser=root,mysqlpass=password 192.168.1.10

# MySQL brute force
nmap --script=mysql-brute 192.168.1.10

# MySQL audit
nmap --script=mysql-audit --script-args mysql-audit.username=root,mysql-audit.password=password 192.168.1.10
```

### RDP Enumeration (Port 3389)

```bash
# RDP version detection
nmap -sV -p 3389 192.168.1.10

# RDP encryption
nmap --script=rdp-enum-encryption 192.168.1.10

# RDP NLA detection
nmap --script=rdp-ntlm-info 192.168.1.10

# RDP vulnerabilities
nmap --script=rdp-vuln* 192.168.1.10
```

---

## Network Mapping

### Host Discovery

#### Ping Sweep

```bash
# ICMP ping sweep
nmap -sn 192.168.1.0/24

# TCP SYN ping
nmap -sn -PS 192.168.1.0/24

# TCP ACK ping
nmap -sn -PA 192.168.1.0/24

# UDP ping
nmap -sn -PU 192.168.1.0/24

# ARP ping (local network)
nmap -sn -PR 192.168.1.0/24

# No ping (assume all hosts up)
nmap -Pn 192.168.1.0/24
```

#### Netdiscover

```bash
# Passive mode
sudo netdiscover -p

# Active mode on specific range
sudo netdiscover -r 192.168.1.0/24

# Fast mode
sudo netdiscover -r 192.168.1.0/24 -f
```

### OS Detection

```bash
# Basic OS detection
sudo nmap -O 192.168.1.10

# Aggressive OS detection
sudo nmap -O --osscan-guess 192.168.1.10

# OS detection with version
sudo nmap -O -sV 192.168.1.10

# Limit OS detection attempts
sudo nmap -O --max-os-tries 1 192.168.1.10
```

### Traceroute

```bash
# Nmap traceroute
nmap --traceroute 192.168.1.10

# System traceroute
traceroute 192.168.1.10

# TCP traceroute
sudo tcptraceroute 192.168.1.10

# MTR (My Traceroute)
mtr 192.168.1.10
```

---

## Vulnerability Scanning

### Nmap Vulnerability Scripts

```bash
# All vulnerability scripts
nmap --script=vuln 192.168.1.10

# Specific vulnerability
nmap --script=smb-vuln-ms17-010 192.168.1.10

# HTTP vulnerabilities
nmap --script=http-vuln* 192.168.1.10

# SMB vulnerabilities
nmap --script=smb-vuln* 192.168.1.10

# SSL vulnerabilities
nmap --script=ssl-heartbleed,ssl-poodle 192.168.1.10
```

### OpenVAS

**Purpose:** Comprehensive vulnerability scanner

**Installation:**
```bash
# Install OpenVAS
sudo apt install openvas

# Setup OpenVAS
sudo gvm-setup

# Start OpenVAS
sudo gvm-start

# Access web interface
# https://localhost:9392
```

**Usage:**
1. Create target
2. Create task
3. Start scan
4. Review results
5. Generate report

### Nessus

**Purpose:** Professional vulnerability scanner

**Features:**
- 100,000+ vulnerability checks
- Compliance auditing
- Web application scanning
- Malware detection
- Configuration auditing

**Basic Workflow:**
1. Install Nessus
2. Create scan policy
3. Define targets
4. Run scan
5. Analyze results
6. Export report

### Nikto

```bash
# Basic web vulnerability scan
nikto -h http://192.168.1.10

# Scan with specific port
nikto -h http://192.168.1.10:8080

# SSL scan
nikto -h https://192.168.1.10 -ssl

# Tune scan (specific tests)
nikto -h http://192.168.1.10 -Tuning 123456789

# Output to file
nikto -h http://192.168.1.10 -o nikto_results.html -Format html

# Use proxy
nikto -h http://192.168.1.10 -useproxy http://proxy:8080
```

---

## Practical Exercises

### Exercise 1: Port Scanning Lab

**Setup:** Use Metasploitable 2 VM

**Tasks:**
1. Perform host discovery on network
2. Scan all TCP ports on target
3. Scan common UDP ports
4. Perform service version detection
5. Attempt OS detection
6. Compare results from different scan types
7. Document timing differences

**Deliverables:**
- Complete port scan results
- Service versions identified
- OS detection results
- Timing comparison chart

### Exercise 2: Service Enumeration Challenge

**Setup:** Use DVWA or HackTheBox machine

**Tasks:**
1. Enumerate HTTP service
2. Enumerate SMB shares
3. Enumerate SSH configuration
4. Enumerate FTP access
5. Enumerate SMTP users
6. Document all findings

**Deliverables:**
- HTTP directories and files
- SMB shares and permissions
- SSH algorithms and keys
- FTP anonymous access status
- SMTP valid users

### Exercise 3: Network Mapping Project

**Setup:** Home or lab network

**Tasks:**
1. Discover all live hosts
2. Identify all open ports
3. Detect operating systems
4. Map network topology
5. Create network diagram
6. Document all services

**Deliverables:**
- Network diagram
- Host inventory
- Service matrix
- OS distribution chart

### Exercise 4: Vulnerability Assessment

**Setup:** Intentionally vulnerable VM

**Tasks:**
1. Perform comprehensive port scan
2. Run Nmap vulnerability scripts
3. Scan with Nikto
4. Identify all vulnerabilities
5. Prioritize by severity
6. Create remediation plan

**Deliverables:**
- Vulnerability report
- Risk assessment
- Remediation recommendations
- Executive summary

---

## Scanning Best Practices

### Stealth Techniques

**Timing:**
```bash
# Slow scan (IDS evasion)
nmap -T0 192.168.1.10

# Sneaky scan
nmap -T1 192.168.1.10

# Polite scan
nmap -T2 192.168.1.10
```

**Fragmentation:**
```bash
# Fragment packets
nmap -f 192.168.1.10

# Custom MTU
nmap --mtu 16 192.168.1.10
```

**Decoys:**
```bash
# Use decoy IPs
nmap -D RND:10 192.168.1.10

# Specific decoys
nmap -D decoy1,decoy2,ME 192.168.1.10
```

**Source Port:**
```bash
# Use specific source port
nmap --source-port 53 192.168.1.10
```

### Performance Optimization

```bash
# Parallel host scanning
nmap --min-hostgroup 50 192.168.1.0/24

# Parallel port scanning
nmap --min-parallelism 100 192.168.1.10

# Minimum packet rate
nmap --min-rate 1000 192.168.1.10

# Maximum retries
nmap --max-retries 1 192.168.1.10

# Host timeout
nmap --host-timeout 5m 192.168.1.10
```

### Documentation

**Always document:**
- Scan parameters used
- Timestamp of scan
- Results and findings
- Anomalies observed
- Follow-up actions needed

**Report Format:**
```
Scan Report
-----------
Target: 192.168.1.10
Date: 2024-01-15
Scanner: Nmap 7.94
Command: nmap -sS -sV -O -A 192.168.1.10

Results:
- Open Ports: 22, 80, 443, 3306
- Services: SSH, HTTP, HTTPS, MySQL
- OS: Linux 3.x
- Vulnerabilities: [list]

Recommendations:
- [action items]
```

---

## Additional Resources

**Tools:**
- Nmap: https://nmap.org/
- Masscan: Fast port scanner
- Unicornscan: Distributed port scanner
- Angry IP Scanner: GUI scanner
- Advanced Port Scanner: Windows scanner

**Nmap Resources:**
- Nmap Book: https://nmap.org/book/
- NSE Scripts: https://nmap.org/nsedoc/
- Nmap Cheat Sheet: https://www.stationx.net/nmap-cheat-sheet/

**Practice:**
- Metasploitable 2/3
- DVWA
- HackTheBox
- TryHackMe

---

## Summary

Master these scanning techniques:

**Port Scanning:**
- TCP Connect, SYN, ACK, FIN, NULL, Xmas
- UDP scanning
- Idle and advanced scans

**Service Enumeration:**
- HTTP/HTTPS, SMB, SSH, FTP
- SMTP, DNS, MySQL, RDP
- Custom service enumeration

**Network Mapping:**
- Host discovery
- OS detection
- Network topology mapping

**Vulnerability Scanning:**
- Nmap scripts
- OpenVAS/Nessus
- Web vulnerability scanning

**Next Steps:**
Proceed to [Module 3: Vulnerability Analysis](../3-Vulnerability-Analysis/README.md) to learn vulnerability assessment and exploitation.
