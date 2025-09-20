# Network Scanning Methodology (EC-Council / CEH)

This is the standard methodology for network scanning as described by EC-Council in the CEH framework. It outlines the steps used in penetration testing to discover and assess network hosts.

---

## **1. Ping Sweep / Host Discovery**
**Purpose:** Identify live hosts on a network before scanning ports.  

**Common Tools / Nmap Flags:**
- `nmap -sn <IP>` (formerly `-sP`) → ping scan / host discovery
- ICMP ping, TCP SYN ping, ARP ping, etc.

---

## **2. Port Scanning**
**Purpose:** Identify open ports on live hosts.  

**Types of Scans:**
- TCP Scans:
  - `-sS` → SYN scan (stealthy)
  - `-sT` → Connect scan (full handshake)
- UDP Scans:
  - `-sU` → UDP scan
- Top ports:
  - `--top-ports <n>` → scan most common ports

**Goal:** Determine which services are listening and accessible.

---

## **3. Service / Version Detection**
**Purpose:** Identify what service is running on a port and its version.  

**Tools / Flags:**
- `nmap -sV` → service and version detection

**Use:** Helps assess potential vulnerabilities based on software versions.

---

## **4. OS Detection / Fingerprinting**
**Purpose:** Determine the operating
