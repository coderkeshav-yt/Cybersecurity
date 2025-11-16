# Day 11-17: Networking and Services - Practice Questions & Answers

## Section 1: Network Fundamentals (10 Questions)

### Question 1
What command shows all network interfaces and their IP addresses using the modern ip command?

**Answer:**
```bash
ip addr show
# or
ip a
```

---

### Question 2
What is the CIDR notation for a subnet mask of 255.255.255.0?

**Answer:**
/24

---

### Question 3
How do you add a static IP address 192.168.1.100/24 to interface eth0?

**Answer:**
```bash
sudo ip addr add 192.168.1.100/24 dev eth0
```

---

### Question 4
What command shows the routing table?

**Answer:**
```bash
ip route show
# or
ip r
# or (legacy)
route -n
```

---

### Question 5
How do you add a default gateway at 192.168.1.1?

**Answer:**
```bash
sudo ip route add default via 192.168.1.1
```

---

### Question 6
What are the three private IP address ranges defined in RFC 1918?

**Answer:**
- 10.0.0.0/8 (10.0.0.0 - 10.255.255.255)
- 172.16.0.0/12 (172.16.0.0 - 172.31.255.255)
- 192.168.0.0/16 (192.168.0.0 - 192.168.255.255)

---

### Question 7
What command tests connectivity to a host with 4 ping packets?

**Answer:**
```bash
ping -c 4 hostname
```

---

### Question 8
How do you trace the route to a destination host?

**Answer:**
```bash
traceroute hostname
# or
tracepath hostname
```

---

### Question 9
What command captures network packets on interface eth0 and saves to a file?

**Answer:**
```bash
sudo tcpdump -i eth0 -w capture.pcap
```

---

### Question 10
How do you show all listening TCP ports with process information?

**Answer:**
```bash
sudo netstat -tlnp
# or (modern)
sudo ss -tlnp
```

---

## Section 2: DNS and Name Resolution (8 Questions)

### Question 11
What file contains local hostname to IP address mappings?

**Answer:**
/etc/hosts

---

### Question 12
What file contains DNS server configuration?

**Answer:**
/etc/resolv.conf

---

### Question 13
How do you perform a DNS lookup for google.com using dig?

**Answer:**
```bash
dig google.com
```

---

### Question 14
What command shows only the IP address result from a DNS query?

**Answer:**
```bash
dig google.com +short
```

---

### Question 15
How do you query the MX (mail exchange) records for a domain?

**Answer:**
```bash
dig google.com MX
# or
host -t MX google.com
# or
nslookup -type=MX google.com
```

---

### Question 16
What command performs a reverse DNS lookup for IP 8.8.8.8?

**Answer:**
```bash
dig -x 8.8.8.8
# or
host 8.8.8.8
# or
nslookup 8.8.8.8
```

---

### Question 17
How do you query a specific DNS server (8.8.8.8) for a domain?

**Answer:**
```bash
dig @8.8.8.8 google.com
# or
host google.com 8.8.8.8
```

---

### Question 18
What are the main DNS record types? (Name at least 5)

**Answer:**
- A - IPv4 address
- AAAA - IPv6 address
- CNAME - Canonical name (alias)
- MX - Mail exchange
- NS - Name server
- PTR - Pointer (reverse DNS)
- TXT - Text record
- SOA - Start of authority

---

## Section 3: SSH - Secure Shell (8 Questions)

### Question 19
What is the default SSH port?

**Answer:**
Port 22

---

### Question 20
How do you connect to a remote server via SSH?

**Answer:**
```bash
ssh username@hostname
# or
ssh username@192.168.1.100
```

---

### Question 21
How do you generate an RSA SSH key pair with 4096 bits?

**Answer:**
```bash
ssh-keygen -t rsa -b 4096
```

---

### Question 22
What command copies your public SSH key to a remote server?

**Answer:**
```bash
ssh-copy-id username@hostname
```

---

### Question 23
How do you connect to SSH using a specific private key?

**Answer:**
```bash
ssh -i ~/.ssh/mykey username@hostname
```

---

### Question 24
What is the SSH server configuration file location?

**Answer:**
/etc/ssh/sshd_config

---

### Question 25
What command creates an SSH local port forward from local port 8080 to remote server's port 80?

**Answer:**
```bash
ssh -L 8080:localhost:80 username@remote-server
```

---

### Question 26
How do you copy a file to a remote server using SCP?

**Answer:**
```bash
scp file.txt username@hostname:/remote/path/
```

---

## Section 4: Web Servers (8 Questions)

### Question 27
How do you install Apache web server on Debian/Ubuntu?

**Answer:**
```bash
sudo apt update
sudo apt install apache2
```

---

### Question 28
What command tests Apache configuration for errors?

**Answer:**
```bash
sudo apache2ctl configtest
# or
sudo apachectl configtest
```

---

### Question 29
How do you enable an Apache site configuration?

**Answer:**
```bash
sudo a2ensite sitename.conf
sudo systemctl reload apache2
```

---

### Question 30
How do you install Nginx on Debian/Ubuntu?

**Answer:**
```bash
sudo apt update
sudo apt install nginx
```

---

### Question 31
What command tests Nginx configuration?

**Answer:**
```bash
sudo nginx -t
```

---

### Question 32
How do you obtain a Let's Encrypt SSL certificate for Nginx?

**Answer:**
```bash
sudo apt install certbot python3-certbot-nginx
sudo certbot --nginx -d example.com -d www.example.com
```

---

### Question 33
How do you reload Nginx configuration without stopping the service?

**Answer:**
```bash
sudo systemctl reload nginx
# or
sudo nginx -s reload
```

---

### Question 34
What HTTP status code indicates a successful request?

**Answer:**
200 OK

---

## Section 5: File Sharing Services (6 Questions)

### Question 35
How do you install Samba on Debian/Ubuntu?

**Answer:**
```bash
sudo apt install samba samba-common-bin
```

---

### Question 36
What is the Samba configuration file location?

**Answer:**
/etc/samba/smb.conf

---

### Question 37
How do you install NFS server on Debian/Ubuntu?

**Answer:**
```bash
sudo apt install nfs-kernel-server
```

---

### Question 38
What file contains NFS export configurations?

**Answer:**
/etc/exports

---

### Question 39
How do you export NFS shares after modifying /etc/exports?

**Answer:**
```bash
sudo exportfs -ra
```

---

### Question 40
How do you mount an NFS share?

**Answer:**
```bash
sudo mount nfs-server:/srv/nfs/shared /mnt/nfs
```

---

## Section 6: Firewall Configuration (6 Questions)

### Question 41
How do you list all iptables rules?

**Answer:**
```bash
sudo iptables -L
# or with more details
sudo iptables -L -v -n
```

---

### Question 42
What command allows SSH traffic through iptables?

**Answer:**
```bash
sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT
```

---

### Question 43
How do you enable UFW firewall?

**Answer:**
```bash
sudo ufw enable
```

---

### Question 44
What command allows HTTP and HTTPS through UFW?

**Answer:**
```bash
sudo ufw allow http
sudo ufw allow https
# or
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
```

---

### Question 45
How do you check UFW status with rule numbers?

**Answer:**
```bash
sudo ufw status numbered
```

---

### Question 46
How do you allow connections from a specific IP address using UFW?

**Answer:**
```bash
sudo ufw allow from 192.168.1.100
```

---

## Section 7: Network Security and Monitoring (4 Questions)

### Question 47
How do you scan a host for open ports using Nmap?

**Answer:**
```bash
nmap 192.168.1.100
```

---

### Question 48
How do you perform service version detection with Nmap?

**Answer:**
```bash
nmap -sV 192.168.1.100
```

---

### Question 49
How do you install Fail2ban?

**Answer:**
```bash
sudo apt install fail2ban
```

---

### Question 50
How do you check Fail2ban status for SSH jail?

**Answer:**
```bash
sudo fail2ban-client status sshd
```

---

## Summary

**Total Questions: 50**

- Section 1: Network Fundamentals (10 questions)
- Section 2: DNS and Name Resolution (8 questions)
- Section 3: SSH - Secure Shell (8 questions)
- Section 4: Web Servers (8 questions)
- Section 5: File Sharing Services (6 questions)
- Section 6: Firewall Configuration (6 questions)
- Section 7: Network Security and Monitoring (4 questions)

**Practice Tips:**
1. Set up a virtual lab environment with multiple VMs
2. Practice each command multiple times
3. Try configuring services from scratch
4. Break configurations intentionally and fix them
5. Use `man command` to explore all options
6. Document your configurations for reference
7. Practice troubleshooting without looking at answers
8. Create network diagrams for complex setups

**Scoring Guide:**
- 45-50 correct: Expert! Ready for advanced networking
- 38-44 correct: Very Good! Minor review needed
- 30-37 correct: Good! Practice more hands-on labs
- 22-29 correct: Fair. Review concepts and practice
- Below 22: Review the material thoroughly and retry

**Study Schedule:**
- **Day 11**: Network Fundamentals (Questions 1-10)
- **Day 12**: DNS and Name Resolution (Questions 11-18)
- **Day 13**: SSH - Secure Shell (Questions 19-26)
- **Day 14**: Web Servers (Questions 27-34)
- **Day 15**: File Sharing Services (Questions 35-40)
- **Day 16**: Firewall Configuration (Questions 41-46)
- **Day 17**: Network Security and Monitoring (Questions 47-50)

**Next Steps:**
- Complete all 50 questions
- Practice each command in your lab environment
- Set up complete network services from scratch
- Move on to Day 18+: Security Hardening
- Build a home lab with multiple networked services
- Practice real-world scenarios
- Explore advanced networking topics

**Certification Preparation:**
These questions align with:
- CompTIA Network+
- LPIC-1 (Linux Professional Institute Certification)
- RHCSA (Red Hat Certified System Administrator)
- CompTIA Security+

**Additional Practice Resources:**
- Set up a multi-VM network lab
- Practice on different distributions (Ubuntu, CentOS, Debian)
- Configure DMZ and network segmentation
- Implement VPN solutions
- Set up monitoring and logging systems
- Join online communities (r/networking, r/linuxadmin)

**Hands-on Lab Ideas:**
1. Build a complete LAMP/LEMP stack
2. Set up a secure SSH bastion host
3. Configure a reverse proxy with load balancing
4. Implement network segmentation with VLANs
5. Create a centralized file server (Samba + NFS)
6. Build a network monitoring dashboard
7. Set up automated SSL certificate management
8. Configure intrusion detection system

Good luck with your Networking and Services journey!
