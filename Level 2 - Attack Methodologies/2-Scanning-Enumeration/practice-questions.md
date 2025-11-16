# Scanning & Enumeration - Practice Questions

## Section 1: Port Scanning Basics (15 Questions)

### Question 1
What is the default scan type used by Nmap when run with root privileges?

**Answer:**
TCP SYN scan (-sS)

---

### Question 2
What command performs a TCP connect scan on a target?

**Answer:**
```bash
nmap -sT 192.168.1.10
```

---

### Question 3
How do you scan all 65535 ports using Nmap?

**Answer:**
```bash
nmap -p- 192.168.1.10
```
or
```bash
nmap -p 1-65535 192.168.1.10
```

---

### Question 4
What Nmap command performs a UDP scan?

**Answer:**
```bash
sudo nmap -sU 192.168.1.10
```

---

### Question 5
What is the difference between -sS and -sT scans?

**Answer:**
- **-sS (SYN scan)**: Half-open scan, doesn't complete TCP handshake, requires root, stealthier
- **-sT (Connect scan)**: Full TCP connection, doesn't require root, more detectable

---

### Question 6
How do you perform a ping sweep on a network?

**Answer:**
```bash
nmap -sn 192.168.1.0/24
```

---

### Question 7
What Nmap timing template is the fastest?

**Answer:**
-T5 (Insane)
```bash
nmap -T5 192.168.1.10
```

---

### Question 8
How do you scan specific ports 80, 443, and 22?

**Answer:**
```bash
nmap -p 80,443,22 192.168.1.10
```

---

### Question 9
What command performs service version detection?

**Answer:**
```bash
nmap -sV 192.168.1.10
```

---

### Question 10
How do you perform OS detection with Nmap?

**Answer:**
```bash
sudo nmap -O 192.168.1.10
```

---

### Question 11
What does the -A flag do in Nmap?

**Answer:**
Aggressive scan - enables OS detection, version detection, script scanning, and traceroute
```bash
nmap -A 192.168.1.10
```

---

### Question 12
How do you save Nmap output in all formats?

**Answer:**
```bash
nmap -oA scan_results 192.168.1.10
```

---

### Question 13
What scan type sends packets with FIN, PSH, and URG flags set?

**Answer:**
Xmas scan (-sX)
```bash
sudo nmap -sX 192.168.1.10
```

---

### Question 14
How do you perform an ACK scan to detect firewall rules?

**Answer:**
```bash
sudo nmap -sA 192.168.1.10
```

---

### Question 15
What command scans the top 100 most common ports?

**Answer:**
```bash
nmap -F 192.168.1.10
```

---

## Section 2: Advanced Scanning (10 Questions)

### Question 16
How do you use decoy IPs to hide your scan source?

**Answer:**
```bash
nmap -D RND:10 192.168.1.10
```
or
```bash
nmap -D decoy1,decoy2,ME 192.168.1.10
```

---

### Question 17
What command fragments packets to evade IDS?

**Answer:**
```bash
nmap -f 192.168.1.10
```

---

### Question 18
How do you specify a custom source port for scanning?

**Answer:**
```bash
nmap --source-port 53 192.168.1.10
```

---

### Question 19
What command performs an idle scan using a zombie host?

**Answer:**
```bash
sudo nmap -sI zombie_host target_host
```

---

### Question 20
How do you set a minimum packet rate for faster scanning?

**Answer:**
```bash
nmap --min-rate 1000 192.168.1.10
```

---

### Question 21
What command scans without ping (assumes all hosts are up)?

**Answer:**
```bash
nmap -Pn 192.168.1.10
```

---

### Question 22
How do you perform a NULL scan?

**Answer:**
```bash
sudo nmap -sN 192.168.1.10
```

---

### Question 23
What command performs a FIN scan?

**Answer:**
```bash
sudo nmap -sF 192.168.1.10
```

---

### Question 24
How do you scan both TCP and UDP ports simultaneously?

**Answer:**
```bash
sudo nmap -sS -sU -p T:80,443,U:53,161 192.168.1.10
```

---

### Question 25
What command performs an IP protocol scan?

**Answer:**
```bash
sudo nmap -sO 192.168.1.10
```

---

## Section 3: Service Enumeration (15 Questions)

### Question 26
How do you enumerate HTTP directories using Nmap?

**Answer:**
```bash
nmap --script=http-enum 192.168.1.10
```

---

### Question 27
What command enumerates SMB shares?

**Answer:**
```bash
nmap --script=smb-enum-shares 192.168.1.10
```
or
```bash
enum4linux -S 192.168.1.10
```

---

### Question 28
How do you check for anonymous FTP access?

**Answer:**
```bash
nmap --script=ftp-anon 192.168.1.10
```

---

### Question 29
What command enumerates SMTP users?

**Answer:**
```bash
nmap --script=smtp-enum-users 192.168.1.10
```
or
```bash
smtp-user-enum -M VRFY -U users.txt -t 192.168.1.10
```

---

### Question 30
How do you enumerate MySQL databases?

**Answer:**
```bash
nmap --script=mysql-databases --script-args mysqluser=root,mysqlpass=password 192.168.1.10
```

---

### Question 31
What command checks for SMB vulnerabilities?

**Answer:**
```bash
nmap --script=smb-vuln* 192.168.1.10
```

---

### Question 32
How do you enumerate SSH algorithms?

**Answer:**
```bash
nmap --script=ssh2-enum-algos 192.168.1.10
```

---

### Question 33
What command checks for DNS zone transfer?

**Answer:**
```bash
nmap --script=dns-zone-transfer --script-args dns-zone-transfer.domain=example.com 192.168.1.10
```

---

### Question 34
How do you enumerate RDP encryption?

**Answer:**
```bash
nmap --script=rdp-enum-encryption 192.168.1.10
```

---

### Question 35
What command enumerates SMB users?

**Answer:**
```bash
nmap --script=smb-enum-users 192.168.1.10
```
or
```bash
enum4linux -U 192.168.1.10
```

---

### Question 36
How do you check HTTP methods allowed on a server?

**Answer:**
```bash
nmap --script=http-methods 192.168.1.10
```
or
```bash
curl -X OPTIONS http://192.168.1.10
```

---

### Question 37
What command performs comprehensive SMB enumeration?

**Answer:**
```bash
enum4linux -a 192.168.1.10
```

---

### Question 38
How do you check for SMTP open relay?

**Answer:**
```bash
nmap --script=smtp-open-relay 192.168.1.10
```

---

### Question 39
What command enumerates NFS shares?

**Answer:**
```bash
nmap --script=nfs-ls,nfs-showmount 192.168.1.10
```
or
```bash
showmount -e 192.168.1.10
```

---

### Question 40
How do you enumerate SNMP information?

**Answer:**
```bash
snmpwalk -v 2c -c public 192.168.1.10
```
or
```bash
nmap --script=snmp-info 192.168.1.10
```

---

## Section 4: NSE Scripts (10 Questions)

### Question 41
How do you run default Nmap scripts?

**Answer:**
```bash
nmap -sC 192.168.1.10
```
or
```bash
nmap --script=default 192.168.1.10
```

---

### Question 42
What command runs all vulnerability scripts?

**Answer:**
```bash
nmap --script=vuln 192.168.1.10
```

---

### Question 43
How do you run a specific Nmap script?

**Answer:**
```bash
nmap --script=script-name 192.168.1.10
```

---

### Question 44
What command lists all available Nmap scripts?

**Answer:**
```bash
nmap --script-help=all
```
or
```bash
ls /usr/share/nmap/scripts/
```

---

### Question 45
How do you run multiple specific scripts?

**Answer:**
```bash
nmap --script=http-title,http-headers 192.168.1.10
```

---

### Question 46
What command runs all HTTP scripts?

**Answer:**
```bash
nmap --script=http-* 192.168.1.10
```

---

### Question 47
How do you pass arguments to an Nmap script?

**Answer:**
```bash
nmap --script=script-name --script-args arg1=value1,arg2=value2 192.168.1.10
```

---

### Question 48
What command checks for Heartbleed vulnerability?

**Answer:**
```bash
nmap --script=ssl-heartbleed 192.168.1.10
```

---

### Question 49
How do you check for EternalBlue (MS17-010) vulnerability?

**Answer:**
```bash
nmap --script=smb-vuln-ms17-010 192.168.1.10
```

---

### Question 50
What command performs HTTP brute force?

**Answer:**
```bash
nmap --script=http-brute --script-args userdb=users.txt,passdb=pass.txt 192.168.1.10
```

---

## Section 5: Practical Scenarios (10 Questions)

### Question 51
You need to scan a network without being detected. What techniques would you use?

**Answer:**
1. Use slow timing: `nmap -T0 or -T1`
2. Fragment packets: `nmap -f`
3. Use decoys: `nmap -D RND:10`
4. Randomize scan order: `nmap --randomize-hosts`
5. Use specific source port: `nmap --source-port 53`
6. Spoof MAC address: `nmap --spoof-mac 0`

---

### Question 52
How would you perform a comprehensive scan of a web server?

**Answer:**
```bash
# Port scan with service detection
nmap -sS -sV -p- 192.168.1.10

# HTTP enumeration
nmap --script=http-* 192.168.1.10

# Web vulnerability scan
nikto -h http://192.168.1.10

# Directory brute force
gobuster dir -u http://192.168.1.10 -w wordlist.txt

# SSL/TLS testing
nmap --script=ssl-* 192.168.1.10
```

---

### Question 53
What steps would you take to enumerate a Windows domain controller?

**Answer:**
1. Port scan: `nmap -sS -sV 192.168.1.10`
2. SMB enumeration: `enum4linux -a 192.168.1.10`
3. LDAP enumeration: `nmap --script=ldap-* 192.168.1.10`
4. Kerberos enumeration: `nmap -p 88 --script=krb5-enum-users 192.168.1.10`
5. RPC enumeration: `rpcclient -U "" 192.168.1.10`
6. Check for vulnerabilities: `nmap --script=smb-vuln* 192.168.1.10`

---

### Question 54
How do you identify the operating system of a target without using -O flag?

**Answer:**
1. TTL values in ping responses
2. Service banners and versions
3. HTTP headers and server signatures
4. SMB version information
5. SSH banner
6. Open port patterns
7. TCP/IP stack fingerprinting

---

### Question 55
What information can you gather from a port scan?

**Answer:**
- Open, closed, and filtered ports
- Running services and versions
- Operating system
- Firewall presence
- Service banners
- Potential vulnerabilities
- Network topology
- Service configurations

---

### Question 56
How would you scan a network with 1000+ hosts efficiently?

**Answer:**
```bash
# Fast host discovery
nmap -sn -T4 --min-hostgroup 100 192.168.0.0/16 -oG live_hosts.txt

# Extract live hosts
grep "Up" live_hosts.txt | cut -d " " -f 2 > targets.txt

# Fast port scan on live hosts
nmap -sS -T4 --min-rate 1000 -iL targets.txt -oA port_scan

# Service detection on open ports
nmap -sV -iL targets.txt -oA service_scan
```

---

### Question 57
What are common UDP ports to scan and why?

**Answer:**
- **53 (DNS)**: DNS queries, zone transfers
- **67/68 (DHCP)**: Network configuration
- **69 (TFTP)**: File transfers, often misconfigured
- **123 (NTP)**: Time synchronization, DDoS amplification
- **161/162 (SNMP)**: Device management, information disclosure
- **500 (IKE)**: VPN configuration
- **1434 (MS-SQL)**: Database discovery
- **1900 (UPnP)**: Device discovery, vulnerabilities

---

### Question 58
How do you differentiate between filtered and closed ports?

**Answer:**
**Closed Port:**
- Responds with RST packet
- Port is accessible but no service listening
- Firewall allows traffic through

**Filtered Port:**
- No response or ICMP unreachable
- Firewall blocking traffic
- Cannot determine if service exists

**Commands:**
```bash
# SYN scan shows filtered vs closed
sudo nmap -sS 192.168.1.10

# ACK scan helps identify filtering
sudo nmap -sA 192.168.1.10
```

---

### Question 59
What Nmap output format is best for parsing with scripts?

**Answer:**
Grepable format (-oG) or XML format (-oX)

```bash
# Grepable output
nmap -oG scan.gnmap 192.168.1.10

# XML output
nmap -oX scan.xml 192.168.1.10

# Parse grepable output
grep "open" scan.gnmap

# Parse XML with tools
nmap-parse scan.xml
```

---

### Question 60
How would you create a complete network inventory?

**Answer:**
```bash
# 1. Host discovery
nmap -sn 192.168.1.0/24 -oG hosts.txt

# 2. Port scanning
nmap -sS -p- -iL hosts.txt -oA ports

# 3. Service detection
nmap -sV -iL hosts.txt -oA services

# 4. OS detection
sudo nmap -O -iL hosts.txt -oA os_detect

# 5. Vulnerability scanning
nmap --script=vuln -iL hosts.txt -oA vulns

# 6. Generate report
# Combine all outputs into comprehensive inventory
```

---

## Summary

**Total Questions: 60**

- Section 1: Port Scanning Basics (15 questions)
- Section 2: Advanced Scanning (10 questions)
- Section 3: Service Enumeration (15 questions)
- Section 4: NSE Scripts (10 questions)
- Section 5: Practical Scenarios (10 questions)

**Practice Tips:**
1. Set up a home lab with multiple VMs
2. Practice different scan types and compare results
3. Learn to read and interpret Nmap output
4. Experiment with timing and stealth options
5. Master NSE scripts for your target services
6. Document scan methodologies
7. Practice on legal targets (HTB, THM, VulnHub)

**Scoring Guide:**
- 54-60 correct: Expert! Ready for professional assessments
- 45-53 correct: Advanced! Strong scanning skills
- 36-44 correct: Intermediate. More practice needed
- 27-35 correct: Beginner. Review concepts
- Below 27: Study the material thoroughly

**Common Mistakes to Avoid:**
- Scanning without permission
- Using aggressive scans on production systems
- Not documenting scan parameters
- Ignoring filtered ports
- Relying on single scan type
- Not verifying results with multiple tools
- Scanning too fast and missing results

**Next Steps:**
- Complete all exercises in the main README
- Practice on Metasploitable 2/3
- Join CTF competitions
- Learn to write custom NSE scripts
- Move to Module 3: Vulnerability Analysis
