# HTB & CTF Acronyms Cheatsheet

Essential acronyms and terminology for HackTheBox machines and CTF competitions.

## Table of Contents
- [Network Protocols & Services](#network-protocols--services)
- [Attack Techniques](#attack-techniques)
- [Security Concepts](#security-concepts)
- [File Types & Extensions](#file-types--extensions)
- [Tools & Frameworks](#tools--frameworks)
- [Common File Locations](#common-file-locations)
- [CTF Categories](#ctf-categories)

---

## Network Protocols & Services

**FTP** - File Transfer Protocol (Port 21)
- File sharing, often allows anonymous access

**SSH** - Secure Shell (Port 22)
- Encrypted remote access to systems

**DNS** - Domain Name System (Port 53)
- Translates domain names to IP addresses, can leak info

**HTTP/HTTPS** - HyperText Transfer Protocol (Ports 80/443)
- Web traffic, your main attack surface

**POP3/IMAP** - Email protocols (Ports 110/143)
- Email servers, might contain credentials

**NTP** - Network Time Protocol (Port 123)
- Time synchronization, can be used for info gathering

**RPC** - Remote Procedure Call (Port 135)
- Windows service communication

**NetBIOS** - Network Basic Input/Output System (Port 137-139)
- Windows networking, pairs with SMB

**SMB** - Server Message Block (Ports 139/445)
- File sharing protocol, often misconfigured

**SNMP** - Simple Network Management Protocol (Port 161)
- Network device monitoring, often has default community strings

**LDAP** - Lightweight Directory Access Protocol (Port 389)
- Directory services, user/computer info

**RDP** - Remote Desktop Protocol (Port 3389)
- Windows remote desktop access

**WinRM** - Windows Remote Management (Port 5985/5986)
- Windows PowerShell remote access

---

## Attack Techniques

**XSS** - Cross-Site Scripting
- Inject malicious JavaScript into web pages

**CSRF** - Cross-Site Request Forgery
- Trick users into performing unwanted actions

**IDOR** - Insecure Direct Object Reference
- Access other users' data by changing parameters

**LFI/RFI** - Local/Remote File Inclusion
- Include files to execute code or read sensitive data

**SQLi** - SQL Injection
- Inject malicious SQL code into database queries

**XXE** - XML External Entity
- Attack XML parsers to read files or perform SSRF

**SSRF** - Server-Side Request Forgery
- Make the server perform requests on your behalf

**RCE** - Remote Code Execution
- Execute commands on the target system

**LPE** - Local Privilege Escalation
- Gain higher privileges once you have initial access

**UAC** - User Account Control
- Windows security feature that can be bypassed

**SUID/SGID** - Set User/Group ID
- Special file permissions that can lead to privilege escalation

---

## Security Concepts

**ACL** - Access Control List
- Permissions on files, directories, or network resources

**GPO** - Group Policy Object
- Windows domain policies that can be misconfigured

**AD** - Active Directory
- Windows domain controller and user management

**DC** - Domain Controller
- Server that manages Windows domain authentication

**NTLM** - NT LAN Manager
- Windows authentication protocol, often crackable

**Kerberos** - Network authentication protocol
- More secure than NTLM but still attackable

**TGT/TGS** - Ticket Granting Ticket/Service
- Kerberos authentication tickets

**AMSI** - Antimalware Scan Interface
- Windows security feature that can be bypassed

**AV/EDR** - Antivirus/Endpoint Detection Response
- Security software you need to evade

---

## File Types & Extensions

**PE** - Portable Executable
- Windows executable file format

**ELF** - Executable and Linkable Format
- Linux executable file format

**DLL** - Dynamic Link Library
- Windows shared library files

**MSI** - Microsoft Installer
- Windows installation packages

**VBS/PS1** - Visual Basic Script/PowerShell
- Common scripting languages for Windows attacks

---

## Tools & Frameworks

**MSF** - Metasploit Framework
- Popular exploitation framework

**C2** - Command and Control
- Infrastructure for managing compromised systems

**RAT** - Remote Access Tool/Trojan
- Software for remote system control

**WAF** - Web Application Firewall
- Security device protecting web applications

**IDS/IPS** - Intrusion Detection/Prevention System
- Network security monitoring

**SIEM** - Security Information Event Management
- Log analysis and correlation system

---

## Common File Locations

### Linux
- `/etc/passwd` - User account info
- `/etc/shadow` - Password hashes
- `/var/log/` - System logs
- `/tmp/` - Temporary files
- `/home/<username>/` - User home directory
- `/root/` - Root user home directory

---

## CTF Categories

**OSINT** - Open Source Intelligence
- Information gathering from public sources

**RE** - Reverse Engineering
- Analyzing compiled programs to understand their function

**PWN** - Binary exploitation
- Exploiting memory corruption vulnerabilities

**MISC** - Miscellaneous
- Challenges that don't fit other categories

**STEGO** - Steganography
- Hidden data in images, audio, or other files

**CRYPTO** - Cryptography
- Breaking or analyzing encryption and encoding

**FORENSICS** - Digital forensics
- Analyzing files, memory dumps, network captures

---

## Quick Port Reference

| Port | Service | Notes |
|------|---------|-------|
| 21 | FTP | Try anonymous:anonymous |
| 22 | SSH | Note version for exploits |
| 23 | Telnet | Try default credentials |
| 25 | SMTP | Email server |
| 53 | DNS | Zone transfers, subdomain enum |
| 80 | HTTP | Web enumeration priority |
| 110 | POP3 | Email protocol |
| 135 | RPC | Windows service |
| 139/445 | SMB | File shares, check anonymous |
| 143 | IMAP | Email protocol |
| 161 | SNMP | Try "public" community string |
| 389 | LDAP | Directory services |
| 443 | HTTPS | Web enumeration priority |
| 993 | IMAPS | Secure IMAP |
| 995 | POP3S | Secure POP3 |
| 1433 | MSSQL | Database server |
| 3306 | MySQL | Database server |
| 3389 | RDP | Windows remote desktop |
| 5432 | PostgreSQL | Database server |
| 5985/5986 | WinRM | Windows remote management |

---
