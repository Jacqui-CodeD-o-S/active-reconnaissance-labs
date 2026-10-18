# active-reconnaissance-labs
Active web reconnaissance, directory enumeration, and target verification against Metasploitable2 using DIRB and Kali Linux.
# Web Reconnaissance & Directory Enumeration Lab

## Overview
This lab demonstrates active web reconnaissance and content discovery against a vulnerable target virtual machine (Metasploitable2) from an attacker machine (Kali Linux) in an isolated host-only lab environment.

## Skills & Tools Demonstrated
* **Security Concepts:** Active Reconnaissance, Rules of Engagement (RoE), Target IP Verification, Directory Brute-Forcing, HTTP Status Codes (200 OK, 403 Forbidden, 301 Redirect).
* **Tools Used:** `dirb`, Linux CLI, VMware Workstation Pro.
* **Target OS:** Metasploitable2 (`192.168.154.128`)
* **Attacker OS:** Kali Linux (`jackierich@kali-attacker`)


## Methodology
1. **Target Verification & Scope Alignment:** Verified local network connectivity to the target IP address to ensure strict compliance with lab Rules of Engagement (RoE) prior to scan execution.
2. **Automated Content Discovery:** Executed `dirb` against `http://192.168.154.128` using default wordlists (`common.txt`) to identify hidden web endpoints and administrative interfaces.
3. **Result Analysis:** Mapped attack surfaces by identifying key web application endpoints including:
   - `/dvwa/` (Damn Vulnerable Web Application)
   - `/mutillidae/` (OWASP Top 10 Web Application)
   - `/phpMyAdmin/` (Database Management Panel)
## Network Port & Service Fingerprinting (Nmap)

### Command Executed
bash
sudo nmap -sV -O -p- 192.168.154.128 -oN nmap_metasploitable_results.txt
### Key Open Ports & Exposed Services
| Port | Protocol | Service | Impact / Security Assessment |
| :--- | :--- | :--- | :--- |
| **21** | TCP | FTP | Cleartext file transfer protocol; running vulnerable service version (`vsftpd 2.3.4`). |
| **22** | TCP | SSH | Encrypted administrative shell access (`OpenSSH 4.7p1`). |
| **23** | TCP | Telnet | Legacy unencrypted administrative shell; vulnerable to credential sniffing. |
| **80** | TCP | HTTP | Web service hosting vulnerable web applications (`DVWA`, `Mutillidae`). |
| **139 / 445** | TCP | SMB | Network file sharing services exposed directly to local subnet. |
| **3306** | TCP | MySQL | Database service port exposed externally without network segmentation. |

### Defensive Recommendations
* **Disable Unencrypted Protocols:** Replace cleartext Telnet (Port 23) and standard FTP (Port 21) with secure alternatives like SSH (Port 22) and SFTP.
* **Implement Network Segmentation:** Restrict database ports (Port 3306) to local loopback (`127.0.0.1`) or backend database subnets using firewall rules.
* **Patch Management:** Upgrade outdated service daemons to patch known public vulnerabilities.
## Key Takeaways & Defense Countermeasures
* **Attacker Impact:** Automated directory enumeration exposes hidden or poorly configured administrative interfaces that are not publicly linked on the web server's primary pages.
* **Defensive Mitigation:** 
  - Deploy Web Application Firewalls (WAF) and rate limiting to detect and block directory brute-forcing.
  - Disable directory listing (`Options -Indexes` in Apache).
  - Enforce strict authentication and multi-factor authentication (MFA) on sensitive endpoints.
