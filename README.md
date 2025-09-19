# HTB & CTF Methodology Guide

A comprehensive guide for HackTheBox machines and CTF competitions.

## Table of Contents
- [Essential Tools & Commands](#essential-tools--commands)
- [Step-by-Step Box Methodology](#step-by-step-box-methodology)
- [Service-Specific Techniques](#service-specific-techniques)
- [CTF-Specific Tips](#ctf-specific-tips)

---

## Essential Tools & Commands

### Reconnaissance (Always Start Here)

```bash
# Quick TCP scan
nmap -sC -sV <target_ip>

# Full TCP port scan
nmap -p- <target_ip>

# Quick UDP scan
nmap -sU --top-ports 1000 <target_ip>

# Aggressive scan (use carefully)
nmap -A <target_ip>

# Vulnerability scan
nmap --script vuln <target_ip>

# Save results
nmap -sC -sV -oA <box_name> <target_ip>
```

### Web Enumeration

What it is: Systematically discovering hidden content, directories, files, and vulnerabilities on web servers.

Why it matters: Web applications often have:

Hidden admin panels (/admin, /wp-admin)

Backup files (backup.zip, config.bak)

Development files left behind

Directories not linked from the main site

```bash
# Directory enumeration
gobuster dir -u http://<target_ip> -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php,html,txt,js

# Fast directory scan
dirb http://<target_ip>

# Subdomain enumeration
gobuster vhost -u http://<domain> -w /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-5000.txt

# Nikto web vulnerability scan
nikto -h http://<target_ip>
```

### SMB Enumeration

What it is: SMB (Server Message Block) is a network protocol for file sharing, mainly used by Windows. Enumeration means checking what files/folders are shared on the network.

Why it matters:

Servers often have misconfigured file shares

You might find sensitive files, passwords, or configuration data

Sometimes you can upload files to shared folders

```bash
# List shares
smbclient -L //<target_ip>

# Anonymous access
smbclient //<target_ip>/share

# Enumerate with null session
enum4linux -a <target_ip>

# SMB vulnerability scan
nmap --script smb-vuln* <target_ip>
```

### Database Enumeration

What it is: When you find database services (like MySQL, MSSQL, PostgreSQL), you enumerate to discover:

What databases exist

What tables are in those databases

What data is stored

User accounts and permissions

Why it matters:

Databases often contain sensitive information (passwords, personal data, configuration)

Default credentials are common

Database servers sometimes allow command execution

```bash
# MySQL
mysql -h <target_ip> -u root -p

# MSSQL
impacket-mssqlclient <username>@<target_ip> -windows-auth
```
---

## Step-by-Step Box Methodology

### 1. Initial Reconnaissance (ALWAYS START HERE)

#### Quick Port Scan
```bash
nmap -sC -sV <target_ip>
```
**What you're looking for:**
- What ports are open?
- What services are running?
- What versions are those services?

**Example output interpretation:**
```
22/tcp   open  ssh     OpenSSH 7.6p1
80/tcp   open  http    Apache httpd 2.4.29
445/tcp  open  smb     Samba smbd 4.7.6
```
This tells you: SSH, Web server, and SMB file sharing are available.

### 2. Service Prioritization (Check in This Order)

#### A. Web Services FIRST (Ports 80, 443, 8080, 8000, etc.)
**Why first?** Web apps have the most attack surface and are often the easiest way in.

```bash
# Visit the website manually
firefox http://<target_ip>

# Directory enumeration
gobuster dir -u http://<target_ip> -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt

# Check common files
curl http://<target_ip>/robots.txt
curl http://<target_ip>/sitemap.xml
```

**Look for:**
- Login pages
- Admin panels (`/admin`, `/administrator`, `/wp-admin`)
- Upload functionality
- Forms that take user input
- Error messages that reveal information

#### B. File Sharing Services (SMB: 139/445, FTP: 21, NFS: 2049)
**Why second?** Often have anonymous access or weak credentials.

```bash
# SMB
smbclient -L //<target_ip>
smbclient //<target_ip>/sharename

# FTP
ftp <target_ip>
# Try: anonymous/anonymous, ftp/ftp
```

**Look for:**
- Anonymous access
- Readable/writable shares
- Interesting files
- Configuration files

#### C. Remote Access Services (SSH: 22, Telnet: 23, RDP: 3389)
**Why third?** Need credentials usually, but note versions for later.

```bash
# Check SSH version for vulnerabilities
ssh <target_ip>

# Telnet - try connecting
telnet <target_ip>
```

**Look for:**
- Version numbers (research later)
- Banner information
- Default credentials (telnet especially)

#### D. Databases (MySQL: 3306, MSSQL: 1433, PostgreSQL: 5432)
**Why fourth?** Usually need credentials but worth checking for defaults.

```bash
# MySQL
mysql -h <target_ip> -u root -p
# Try empty password first
```

### 3. Quick Wins to Check IMMEDIATELY

#### Default Credentials (Try These First)
- `admin:admin`
- `admin:password`
- `root:root`
- `administrator:administrator`
- `admin:` (empty password)
- Service-specific defaults (research each service)

#### Common Web Directories (If web service found)
```bash
# Check these URLs manually:
http://<target_ip>/admin
http://<target_ip>/administrator  
http://<target_ip>/login
http://<target_ip>/wp-admin
http://<target_ip>/phpmyadmin
```

### 4. Research Phase

#### Version Research
```bash
# Search for exploits
searchsploit <service_name> <version>
searchsploit Apache 2.4.29
searchsploit OpenSSH 7.6
```

#### Google Research
- Search: `"Apache 2.4.29" vulnerability exploit`
- Check CVE databases
- Look for recent security advisories

### 5. Deeper Enumeration (If Initial Checks Don't Work)

#### Full Port Scan
```bash
nmap -p- <target_ip>
```

#### UDP Scan
```bash
nmap -sU --top-ports 1000 <target_ip>
```

#### Vulnerability Scan
```bash
nmap --script vuln <target_ip>
```

### Example Workflow for a Box:

```bash
# 1. Quick scan
nmap -sC -sV 10.129.1.100

# Results show: 22 (SSH), 80 (HTTP), 445 (SMB)

# 2. Check web first
firefox http://10.129.1.100
# See a login page

# 3. Try default creds on web
admin:admin, admin:password

# 4. If web login fails, check SMB
smbclient -L //10.129.1.100
# See "backup" share

# 5. Access SMB share
smbclient //10.129.1.100/backup
# Find config file with passwords

# 6. Use found credentials on web/SSH
```

### Priority Checklist:

1. ✅ **Port scan completed?**
2. ✅ **Web services checked first?**
3. ✅ **Default credentials tried?**
4. ✅ **File shares enumerated?**
5. ✅ **Service versions researched?**
6. ✅ **Common directories checked?**

**Golden Rule:** Always enumerate thoroughly before trying complex exploits. Most HTB boxes (especially easier ones) have simple misconfigurations or default credentials rather than requiring advanced exploitation.

---

## Service-Specific Techniques

### FTP (Port 21)
```bash
# Anonymous login
ftp <target_ip>
# Username: anonymous, Password: anonymous

# Check for file upload
put test.txt

# Binary mode for executables
binary
```

### SSH (Port 22)
```bash
# Key-based authentication
ssh -i <private_key> <username>@<target_ip>

# Weak algorithms check
ssh -oKexAlgorithms=+diffie-hellman-group1-sha1 <target_ip>

# Port forwarding
ssh -L <local_port>:<target>:<target_port> <username>@<target_ip>
```

### Telnet (Port 23)
```bash
# Connect
telnet <target_ip>

# Common credentials
root:root, admin:admin, admin:password
```

### HTTP/HTTPS (Ports 80, 443)
```bash
# Technology fingerprinting
whatweb <target_ip>

# Check for admin panels
/admin, /administrator, /wp-admin, /phpmyadmin

# Common files
robots.txt, sitemap.xml, .htaccess, web.config

# Parameter fuzzing
wfuzz -c -w /usr/share/wordlists/seclists/Discovery/Web-Content/burp-parameter-names.txt --hh 0 http://<target_ip>/index.php?FUZZ=test
```

### SMB (Ports 139, 445)
```bash
# Null session
smbmap -H <target_ip>

# Authenticated access
smbmap -H <target_ip> -u <username> -p <password>

# Mount share
sudo mount -t cifs //<target_ip>/share /mnt/smb -o username=<username>,password=<password>
```

### SNMP (Port 161)
```bash
# Community string enumeration
snmp-check <target_ip>
onesixtyone -c /usr/share/seclists/Discovery/SNMP/snmp.txt <target_ip>

# Walk SNMP tree
snmpwalk -c public -v1 <target_ip>
```

---

## Tools Used
```

### Quick Reference Card
```markdown
# Quick Reference

## Common Ports
- 21: FTP
- 22: SSH
- 23: Telnet
- 25: SMTP
- 53: DNS
- 80: HTTP
- 110: POP3
- 139/445: SMB
- 143: IMAP
- 443: HTTPS
- 993: IMAPS
- 995: POP3S
- 1433: MSSQL
- 3306: MySQL
- 3389: RDP
- 5432: PostgreSQL
- 5985: WinRM

## Default Credentials
- admin:admin
- admin:password
- root:root
- root:toor
- administrator:administrator
- guest:guest
- anonymous:anonymous

## One-Liners
```bash
# Find SUID binaries
find / -perm -u=s -type f 2>/dev/null

# Find world-writable files
find / -writable -type f 2>/dev/null

# Check sudo privileges
sudo -l

# System information
uname -a && cat /etc/*release*

# Network information
ip addr && netstat -antup
```
```

---

## Useful Resources

### Wordlists (Kali Linux)
```bash
# Web directories
/usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
/usr/share/wordlists/seclists/Discovery/Web-Content/common.txt

# Passwords
/usr/share/wordlists/rockyou.txt
/usr/share/wordlists/seclists/Passwords/Common-Credentials/10k-most-common.txt

# Usernames
/usr/share/wordlists/seclists/Usernames/Names/names.txt
/usr/share/wordlists/metasploit/unix_users.txt
```

### Online Resources
- **Exploit Databases:**
  - https://www.exploit-db.com/
  - https://cve.mitre.org/
  - https://nvd.nist.gov/

- **Payloads & Shells:**
  - https://github.com/swisskyrepo/PayloadsAllTheThings
  - https://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet
  - https://www.revshells.com/

- **Privilege Escalation:**
  - https://github.com/carlospolop/PEASS-ng
  - https://github.com/rebootuser/LinEnum
  - https://github.com/PowerShellMafia/PowerSploit

### Essential Tools
- **nmap**: Port scanning and service detection
- **gobuster**: Directory/subdomain enumeration
- **burp suite**: Web application testing
- **searchsploit**: Local exploit database
- **smbclient**: SMB enumeration
- **hydra**: Credential brute forcing
- **netcat**: Manual service interaction
- **sqlmap**: SQL injection testing
- **john**: Password cracking
- **hashcat**: Password cracking

---

## CTF-Specific Tips

### Time Management
- **Reconnaissance first:** Always start with thorough enumeration
- **Low-hanging fruit:** Check for obvious vulnerabilities first
- **Document everything:** You might need to come back to something
- **Team communication:** Share findings immediately

### Common CTF Categories
1. **Web Exploitation**
   - SQL injection
   - XSS
   - File upload
   - IDOR
   - Command injection

2. **Cryptography**
   - Classical ciphers
   - Hash cracking
   - RSA attacks
   - Block cipher modes

3. **Binary Exploitation**
   - Buffer overflows
   - Format string bugs
   - ROP chains

4. **Forensics**
   - File analysis
   - Memory dumps
   - Network captures
   - Steganography

5. **Reverse Engineering**
   - Static analysis
   - Dynamic analysis
   - Packing/obfuscation

### CTF Methodology
1. **Read the challenge carefully**
2. **Identify the category**
3. **Apply category-specific techniques**
4. **Check for rabbit holes**
5. **Document your process**
6. **Ask for hints if stuck (team CTFs)**

### Quick Wins to Look For
- **Default credentials**
- **Directory traversal** (`../../../etc/passwd`)
- **Command injection** (`;id`, `|whoami`)
- **SQL injection** (`' OR 1=1--`)
- **Obvious files** (backup files, config files)
- **Version-specific exploits**

---
