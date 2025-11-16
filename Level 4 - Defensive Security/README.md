# Level 4: Defensive Security, Hardening, and Practice Labs

## Introduction
Transition from an attacker's mindset to a defender's perspective by learning security controls, monitoring, and incident response. This level focuses on protecting systems and responding to security incidents effectively.

## Core Topics

### 1. System Hardening

#### Linux Hardening
```bash
# Disable unnecessary services
sudo systemctl disable <service>

# Set proper file permissions
sudo chmod 600 /etc/ssh/sshd_config

# Configure firewall (UFW)
sudo ufw enable
sudo ufw default deny incoming
sudo ufw allow ssh

# Install and configure fail2ban
sudo apt install fail2ban
sudo systemctl enable fail2ban
```

#### Windows Hardening
- Enable Windows Defender
- Configure Windows Firewall
- Implement AppLocker
- Enable BitLocker for disk encryption
- Configure User Account Control (UAC)
- Disable unnecessary services

### 2. Network Security

#### Firewall Configuration
```bash
# iptables basic rules
sudo iptables -A INPUT -i lo -j ACCEPT
sudo iptables -A INPUT -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT
sudo iptables -A INPUT -j DROP

# Save iptables rules
sudo iptables-save > /etc/iptables/rules.v4
```

#### Intrusion Detection/Prevention
- **Snort Installation**
  ```bash
  sudo apt install snort
  sudo snort -T -c /etc/snort/snort.conf
  ```

- **Suricata Configuration**
  ```bash
  sudo apt install suricata
  sudo suricata-update
  sudo systemctl enable suricata
  ```

### 3. Security Monitoring

#### SIEM Setup (ELK Stack)
1. **Elasticsearch Installation**
   ```bash
   wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
   echo "deb https://artifacts.elastic.co/packages/7.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-7.x.list
   sudo apt update && sudo apt install elasticsearch
   ```

2. **Logstash Configuration**
   ```bash
   input {
     beats {
       port => 5044
     }
   }
   
   filter {
     if [type] == "syslog" {
       grok {
         match => { "message" => "%{SYSLOGTIMESTAMP:syslog_timestamp} %{SYSLOGHOST:syslog_hostname} %{DATA:syslog_program}(?:\[%{POSINT:syslog_pid}\])?: %{GREEDYDATA:syslog_message}" }
       }
     }
   }
   
   output {
     elasticsearch {
       hosts => ["localhost:9200"]
       index => "%{[@metadata][beat]}-%{+YYYY.MM.dd}"
     }
   }
   ```

3. **Kibana Dashboard**
   - Install Kibana
   - Configure to connect to Elasticsearch
   - Import pre-built security dashboards

### 4. Lab Environment Setup

#### Virtualization with VirtualBox
```bash
# Install VirtualBox
sudo apt install virtualbox

# Create a new VM
VBoxManage createvm --name "Kali" --ostype "Debian_64" --register
VBoxManage createhd --filename "Kali.vdi" --size 32768
VBoxManage storagectl "Kali" --name "SATA Controller" --add sata --controller IntelAhci
VBoxManage storageattach "Kali" --storagectl "SATA Controller" --port 0 --device 0 --type hdd --medium "Kali.vdi"
```

#### Vulnerable VMs
1. **Metasploitable 2/3**
   - Download and import OVA
   - Document all vulnerabilities
   - Practice securing the system

2. **OWASP Juice Shop**
   ```bash
   # Run with Docker
   docker run --rm -p 3000:3000 bkimminich/juice-shop
   ```

### 5. Incident Response

#### Preparation
- Develop an incident response plan
- Create an incident response team
- Establish communication protocols
- Prepare forensic tools

#### Detection and Analysis
- **Log Analysis**
  ```bash
  # Search for failed login attempts
  grep "Failed password" /var/log/auth.log
  
  # Check for suspicious processes
  ps aux | grep -i "backdoor\|shell\|reverse"
  
  # Check network connections
  netstat -tulnp
  ```

#### Containment and Eradication
- Isolate affected systems
- Preserve evidence
- Remove malware and backdoors
- Patch vulnerabilities

## Practical Exercises

### Exercise 1: System Hardening
1. Harden a Linux server following CIS benchmarks
2. Implement proper logging and monitoring
3. Test the security controls

### Exercise 2: SIEM Implementation
1. Set up the ELK Stack
2. Configure log collection from multiple sources
3. Create custom dashboards for security monitoring

### Exercise 3: Incident Response Simulation
1. Simulate a security incident
2. Follow the incident response process
3. Document findings and lessons learned

### Exercise 4: Secure Network Design
1. Design a secure network architecture
2. Implement network segmentation
3. Configure firewalls and IDS/IPS

## Additional Resources
- [CIS Benchmarks](https://www.cisecurity.org/cis-benchmarks/)
- [NIST Cybersecurity Framework](https://www.nist.gov/cyberframework)
- [SANS Incident Handler's Handbook](https://www.sans.org/security-resources/incident-handler-handbook/)
- [MITRE ATT&CK for Enterprise](https://attack.mitre.org/)

## Conclusion
After completing this level, you should be able to:
- Harden operating systems and networks
- Implement and manage security monitoring
- Respond to security incidents effectively
- Maintain a secure lab environment

## Next Steps
- Obtain relevant certifications (Security+, CySA+, CISSP)
- Participate in CTF competitions
- Contribute to open-source security projects
- Stay updated with the latest security trends and threats
