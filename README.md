# 🔍 [Wireshark- Capture & Analyze Traffic]

**Author:** Dashane James  
**Lab Environment:** [VMware Workstation | Kali Linux | Metasploitable 2| Wireshark]  
**Purpose:** [Capturing live traffic between virtual machines and learning to filter and read packet captures.]  
**Status:** 🟢 Active / 🟡 In Progress / 🔵 Completed

---

## 📋 Overview

This repository documents a series of assessments run inside my isolated Vmware home lab with the goal of gaining familiarity with capturing and analyzing packets in Wireshark. I then took it a step further by filtering and analyzing packets by ICMP, TCP, and the target's IP. These scenarios help build a deeper understanding of how communication occurs on a network and also are commonly tested in PBQs on the CYSA+ exam.  

---

## 🧪 Lab Environment
| Component | Details |
|---|---|
| Hypervisor | VMware Workstation (Host-Only Network) |
| Attacker Machine | Kali Linux 2026.1 — `192.168.79.129` |
| Target Machine | [Metasploitable 2] — `192.168.79.130` |
| Primary Tool | Wireshark |
| Network Type | Host-Only (isolated, no internet exposure) |
| Host OS | Windows 11 — ASUS Vivobook 14 |

> ⚠️ **Note:** All activity was performed in a controlled, isolated lab environment against deliberately vulnerable machines. No unauthorized access to live networks was performed.

---

## 🛠️ Tools Used

- **Wireshark** — GUI-based packet capture and analysis tool used to capture, filter, and reconstruct network traffic
- **Nmap** — Used alongside Wireshark to generate TCP traffic (SYN/RST behavior) for packet-level analysis
- **Ping (ICMP)** — Used to generate simple, observable ICMP traffic for comparison against TCP behavior

---

## 📁 Repository Structure

```
[Repo-Name]/
├── README.md
├── [folder-1]/
│   ├── [file1.txt]        # Description of file
│   └── [file2.md]         # Description of file
├── [folder-2]/
│   ├── [file3.md]         # Description of file
│   └── [file4.txt]        # Description of file
└── reports/
    └── [final-report.md]  # Full assessment report
```

---

## 🔬 Tasks / Assessments Performed

## 1. [Open Wireshark in Kali, start capture on eth0]

I started up Wireshark and I pressed start capture and indicated that I wanted it to capture packets on eth0. The purpose of this is to run a basic capture of traffic on eth0 to analyze the packets accordingly.


Output

The output gave me hundreds of packets being captured. The recurring theme was under ICMP the status info read as port unreachable. After taking a closer look, I noticed that Kali is trying to reach the host gateway (192.168.79.1) with DNS queries but there is no real DNS server on my isolated network. This then responds with ICMP destination unreachable (port unreachable) which means there's no service listening on that port to answer the DNS query. So, essentially this is just background noise not actual target data.


Findings: I found that the captured traffic was mostly background noise from Kali's background services (DNS/NTP queries) failing due to the isolated network having no internet access. While this is a  bit distracting, its important to recognize and filter out so real traffic isn't obscured by all the background noise.

<img width="773" height="402" alt="Screenshot 2026-07-21 212327" src="https://github.com/user-attachments/assets/62c46ac9-7120-4837-abd5-0848657124d6" />



## 2. [Ping Metasploitable and run a quick nmap scan]
[I ran a ping to confirm connectivity and generate simple ICMP traffic to analyze. I then ran a quick Nmap scan (-F) to generate more complex TCP traffic, so I could compare basic ICMP traffic against real port scanning traffic in Wireshark.]

# Commands used
Ping metasploitable - [ping 192.168.79.130]
Run a quick nmap scan - [nmap -F -n 192.168.79.130]

Output

[The output once the ping command was entered showed me the source ip(192.168.79.129) and its ping destination 192.168.79.130. It goes on to show many packets of the ping request to the target and it's reply back to the host. Then, I ran a fast nmap scan and the results were captured in Wireshark as well. The capture shows the underlying TCP behavior behind Nmap's port scanning. Closed ports respond with RST/ACK, while the open ports respond with SYN/ACK before being torn down by the OS.]
 
<img width="805" height="399" alt="Screenshot 2026-07-21 212400" src="https://github.com/user-attachments/assets/5c250565-7371-41d6-ac37-39c7b023aaef" /> Ping command shown in terminal

<img width="767" height="398" alt="Screenshot 2026-07-21 212450" src="https://github.com/user-attachments/assets/f1ce8ac6-6417-47af-a019-6a2d0e587157" /> Ping packet output

<img width="842" height="400" alt="Screenshot 2026-07-21 212514" src="https://github.com/user-attachments/assets/68874dec-1db2-459e-9acd-5bd9b25c2fa9" /> Nmap  command shown in terminal

<img width="803" height="404" alt="Screenshot 2026-07-21 212528" src="https://github.com/user-attachments/assets/b760798c-dfae-43fb-8df9-46c813c73630" />  Nmap scan packet output

Findings: 
I found that this task displays how Nmap determines port state at the packet level. Closed ports respond with RST/ACK while open ports respond with SYN/ACK. The results matched the results of the open ports in the quick nmap scan that I ran against the target in Kali's command terminal. This proves that Nmap's results aren't just guesses, but actually come from observable TCP handshake behavior which can be used to verify scans or troubleshoot unexpected findings.

## 3. [Filter by: ICMP, TCP, then Metasploitable's IP]
[I filtered the results recorded by Wireshark in my previous task by ICMP, TCP, and target's IP. The purpose was to practice using the filter feature in Wireshark and also being able to differentiate the different types of traffic.]

Output

The output for the ICMP filter showed both my successful ping replies to the target and unrelated "Destination unreachable" errors from Kali's background DNS/NTP traffic failing on the isolated network. Next I filtered the packets to only show the TCP traffic which showed how normal traffic looks when devices are communicating, which includes whether data is delivered or not shown by the RST/ACK and SYN/ACK handshake results. Finally I  used the command "ip.addr == 192.168.79.130" to display all traffic going to and from this metasploitable ip address. This included seeing ping requests being sent by the host and the targets reply.

<img width="787" height="419" alt="Screenshot 2026-07-21 212627" src="https://github.com/user-attachments/assets/ca0c8548-3634-4114-98ba-041b57c77afe" /> Filtered by ICMP

<img width="792" height="422" alt="Screenshot 2026-07-21 212702" src="https://github.com/user-attachments/assets/ab1b38cc-b39c-4a51-b0e1-73c86bcb109e" /> Filtered by TCP

<img width="856" height="422" alt="Screenshot 2026-07-21 212904" src="https://github.com/user-attachments/assets/079fa835-9766-48d3-858c-f4d9f76e8600" /> Filtered by Ip Address


Findings: 
I found that this task showed me how filtering isolates different types of traffic from a large and mixed packet capture which separated real target communication from unrelated background noise. I also learned how it can highlight protocol-specific behavior like the TCP handshake. Using ip.addr == to isolate traffic to a specific host was especially useful, as it let me focus directly on relevant packets instead of having to scroll through unrelated system chatter. 

The syntax for using the filter to narrow down a specific Ip addres is ip.addr == 192.168.79.130

## 4. [Right Click TCP packet/Follow TCP stream to read a full conversation]
[For this task I dove further into breaking down the packets generated in Metasploitable. Specifically I looked into a TCP packet by right clicking one of the packets and clicking the option to "follow TCP stream". By doing this it allowed me to read the full conversation being sent between the host and the target which displayed valuable information.]


Output

The output first displays the HTTP request sent from Kali to metasploitable, followed by the server's response. This included the full HTTP response, headers, and page content. Also displayed was the server banner, a warning, msf developer contact information, and login info all in  clear text. There is also some HTML code which indicated Metasploitable's homepage is acting as a directory of vulnerable web applications hosted on the target machine, such as TWiki, phpMyAdmin, Mutillidae, DVWA, and WebDAV.

<img width="574" height="403" alt="Screenshot 2026-07-22 120250" src="https://github.com/user-attachments/assets/2ebe13a5-ca45-4055-9145-5cf614cd79c8" />


##Findings 

GET / HTTP/1.1 - The most basic request a browser can make which essentially means "give me whatever the default page is at this address." The response confirms it by showing: <title>Metasploitable2 - Linux</title> which is what you see when visiting the target's IP address in the web browser. This confirms that requesting the root path returns the server's default file (index.html = home page)
More importantly, as I stated earlier this page exposed sensitive information in plaintext, including the Apache/PHP version banners, developer contact info, and default login credentials (msfadmin/msfadmin) all readable in the HTTP response body. This is a good example of a key risk of using unencrypted protocols because anyone capturing this traffic can also read this sensitive information without needing to break any encryption.


📊 Key Findings Summary

| Port/Service | Tool Used | Risk Level | Notes |
|---|---|---|---|
| 80/tcp HTTP | Wireshark (Follow TCP Stream) | 🔴 Critical | Full HTTP request/response readable in plaintext, including default credentials (msfadmin/msfadmin) and server version banners |
| N/A (background traffic) | Wireshark | 🟢 Low | DNS/NTP queries failing due to isolated network — background noise, not a vulnerability, but must be filtered out to avoid confusion during analysis |

**Risk Levels:** 🔴 Critical | 🟠 High | 🟡 Medium | 🟢 Low

🗺️ MITRE ATT&CK Mapping

| Action Performed | ATT&CK Tactic | Technique ID | Technique Name |
|---|---|---|---|
| Packet capture and traffic analysis | Reconnaissance | T1040 | Network Sniffing |
| Reconstructing HTTP session via Follow TCP Stream | Credential Access | T1040 | Network Sniffing |
| Filtering traffic by protocol/host to isolate relevant data | Reconnaissance | T1595 | Active Scanning |

🛡️ Defensive Recommendations
Based on findings, the following remediations would be recommended in a real environment:

1. **HTTP transmits data in plaintext (Critical)** — Enforce HTTPS/TLS for all web services to prevent credentials and session data from being readable in captured traffic.
2. **Default credentials exposed on webpage** — Remove hardcoded default login information from any production-facing page.
3. **Background system noise can obscure real traffic** — Establish a baseline of expected network chatter (DNS, NTP, ARP) so analysts can quickly distinguish it from genuine anomalies during an investigation.

🔑 Technical Notes
[Any important notes about your lab setup, workarounds, or lessons learned. Example:

"Always add the -n flag to Nmap scans in this VMware environment to prevent DNS resolution hangs."]

-sP and -sT contradict eachother (Can't be used together because -sP means just do a ping/host-discovery sweep, skip ports entirely but -sT tells Nmap to do a full TCP connect port scan. These commands contradict eachother.)

# Any important commands or workarounds

# Filter to show only traffic to/from a specific host
ip.addr == 192.168.79.130

# Generate simple ICMP traffic for analysis
ping 192.168.79.130

# Generate TCP traffic (SYN/RST behavior) for analysis
nmap -F -n 192.168.79.130

# Workaround: Wireshark's display filter bar requires structured syntax —
# a bare IP address alone (e.g. "192.168.79.130") is invalid. Always use
# a field comparison like ip.addr == <IP>, using double equals (==), not
# a single "=".

# To read a full application-layer conversation (e.g. HTTP) instead of individual packets: right-click a TCP packet → Follow → TCP Stream

📌 About This Project
[This repository is part of my broader cybersecurity portfolio, focused on building hands-on packet analysis skills using Wireshark — from basic traffic capture through reconstructing full application-layer conversations to identify real security risks like plaintext credential exposure.]

Related repositories:

- `Nmap-Host-Discovery-and-Lab-Baseline` — Established a baseline of the lab network using ping sweeps and host discovery, documenting the target environment before deeper scanning began
- `Nmap-Scan-Types-SYN-vs-TCP-vs-UDP` — Compared SYN, TCP connect, and UDP scan types against the target, examining differences in speed, stealth, and reliability
- `Service-Version-Detection-and-OS-Fingerprinting` — Identified exact software versions on Metasploitable and researched real CVEs tied to them
- `Nmap-Scripting-Engine` — Used Nmap's scripting engine to detect and, in one case, actively exploit a vulnerability referenced in this repo's findings
- `TCPDump-CLI-Packet-Capture` — Captured the same kind of traffic from the command line, including a live Telnet session showing plaintext credential exposure

👤 Author
Dashane James
Senior Field Service Technician → Cybersecurity Analyst
📍 Yonkers, NY
🎓 B.S. Information Technology — SUNY Canton
🏆 CompTIA Security+ | CySA+ (In Progress)
🔗 GitHub | Zero Trust Cyber Security Brand

This repository is part of an active portfolio demonstrating hands-on cybersecurity skills. All lab work performed in isolated environments for educational purposes.









**Author:** Dashane James  
**Lab Environment:** VMware Workstation | Kali Linux 2026.1 | Metasploitable 2  
**Purpose:** Hands-on network reconnaissance practice aligned with CompTIA CySA+ (CS0-003) exam objectives  
**Status:** 🟢 Active — updated as lab exercises are completed

---

## 📋 Overview

This repository documents a series of network reconnaissance and port scanning assessments performed in an isolated VMware home lab environment. All scanning activity was conducted against a purposely vulnerable target (Metasploitable 2) on a Host-Only network with no internet exposure.

The goal of this project is to build and demonstrate hands-on proficiency with Nmap — one of the core tools listed in the CompTIA CySA+ exam objectives — while developing real analyst workflows around host discovery, service enumeration, and vulnerability identification.

---

## 🧪 Lab Environment

| Component | Details |
|---|---|
| Hypervisor | VMware Workstation (Host-Only Network) |
| Attacker Machine | Kali Linux 2026.1 — `192.168.79.129` |
| Target Machine | Metasploitable 2 — `192.168.79.130` |
| Network Type | Host-Only (isolated, no internet exposure) |
| Host OS | Windows 11 — ASUS Vivobook 14 |

> ⚠️ **Note:** All scans were performed in a controlled, isolated lab environment against a deliberately vulnerable machine. No unauthorized scanning of any live networks was performed.

---

## 🛠️ Tools Used

- **Nmap 7.98** — Network discovery and port scanning
- **Wireshark** — Packet capture and traffic analysis
- **TCPDump** — CLI-based packet capture
- **Netcat** — Port connectivity verification

---

## 📁 Repository Structure

```
Nmap-Network-Assessment/
├── README.md
├── scans/
│   ├── baseline_fast_scan.txt        # Initial -F fast scan output
│   ├── full_port_scan.txt            # Full -p- all ports scan
│   ├── service_version_scan.txt      # -sV version detection output
│   └── nse_vuln_scan.txt             # NSE script vulnerability results
├── analysis/
│   ├── open_ports_breakdown.md       # Port-by-port analysis and risk notes
│   ├── cve_findings.md               # CVEs identified from version detection
│   └── mitre_attack_mapping.md       # ATT&CK technique mapping
└── reports/
    └── recon_assessment_report.md    # Full analyst-style assessment report
```

---

## 🔬 Assessments Performed

### 1. Host Discovery & Baseline Scan
Performed initial host discovery across the lab subnet and established a baseline of all open ports on the target.

```bash
# Subnet ping sweep
sudo nmap -sT -Pn --disable-arp-ping -n -sP 192.168.79.0/24

# Fast baseline scan — top 100 ports
sudo nmap -sT -Pn --disable-arp-ping -n -F 192.168.79.130 -oN scans/baseline_fast_scan.txt
```

**Key Finding:** 18 open ports identified on initial fast scan including several high-risk services.

---

### 2. Full Port Scan
Comprehensive scan of all 65,535 TCP ports to ensure no services were missed by the fast scan.

```bash
sudo nmap -sT -Pn --disable-arp-ping -n -p- 192.168.79.130 -oN scans/full_port_scan.txt
```

---

### 3. Service Version Detection
Identified exact software versions running on open ports to enable CVE identification.

```bash
sudo nmap -sV -Pn --disable-arp-ping -n -p 21,22,23,80,3306,5432,5900 192.168.79.130 -oN scans/service_version_scan.txt
```

---

### 4. OS Fingerprinting
Attempted OS detection to identify the target operating system.

```bash
sudo nmap -O -Pn --disable-arp-ping -n 192.168.79.130
```

---

### 5. NSE Script Scanning
Used Nmap Scripting Engine to perform targeted vulnerability checks against specific services.

```bash
# Default script scan
sudo nmap -sC -Pn --disable-arp-ping -n -p 21,22,80,445 192.168.79.130

# FTP anonymous login check
sudo nmap --script ftp-anon -Pn --disable-arp-ping -n -p 21 192.168.79.130

# SMB vulnerability check
sudo nmap --script smb-vuln* -Pn --disable-arp-ping -n -p 445 192.168.79.130

# Broad vulnerability scan
sudo nmap --script vuln -Pn --disable-arp-ping -n -p 21,22,80 192.168.79.130 -oN scans/nse_vuln_scan.txt
```

---

## 📊 Key Findings Summary

| Port | Service | Version | Risk Level | Notes |
|---|---|---|---|---|
| 21/tcp | FTP | vsftpd 2.3.4 | 🔴 Critical | Known backdoor vulnerability (CVE-2011-2523) |
| 22/tcp | SSH | OpenSSH 4.7p1 | 🟡 Medium | Outdated version |
| 23/tcp | Telnet | Linux telnetd | 🔴 Critical | Plaintext credential transmission |
| 80/tcp | HTTP | Apache 2.2.8 | 🔴 Critical | Outdated, multiple known CVEs |
| 139/tcp | NetBIOS | Samba smbd | 🟠 High | SMB exposure risk |
| 445/tcp | SMB | Samba smbd 3.X | 🟠 High | Legacy SMB vulnerabilities |
| 3306/tcp | MySQL | MySQL 5.0.51a | 🔴 Critical | Database exposed on network |
| 5432/tcp | PostgreSQL | PostgreSQL 8.3 | 🟠 High | Database exposed on network |
| 5900/tcp | VNC | Protocol 3.3 | 🔴 Critical | Remote desktop with weak auth |

---

## 🗺️ MITRE ATT&CK Mapping

| Action Performed | ATT&CK Tactic | Technique ID | Technique Name |
|---|---|---|---|
| Nmap host discovery | Reconnaissance | T1595 | Active Scanning |
| Port scanning | Reconnaissance | T1595.001 | Scanning IP Blocks |
| Service version detection | Reconnaissance | T1592 | Gather Victim Host Info |
| NSE script scanning | Reconnaissance | T1595.002 | Vulnerability Scanning |

---

## 🛡️ Defensive Recommendations

Based on findings, the following remediations would be recommended in a real environment:

1. **Disable Telnet (port 23)** — Replace with SSH immediately. Telnet transmits credentials in plaintext.
2. **Patch or replace vsftpd** — CVE-2011-2523 is a critical backdoor. Upgrade or use SFTP.
3. **Restrict database ports** — MySQL (3306) and PostgreSQL (5432) should never be exposed directly to a network. Bind to localhost only.
4. **Disable VNC or enforce strong authentication** — VNC Protocol 3.3 has no encryption. Use a VPN or SSH tunnel.
5. **Update Apache** — Version 2.2.8 is end-of-life with numerous critical CVEs. Upgrade to current release.
6. **Restrict SMB access** — Apply host-based firewall rules to limit SMB (445) to authorized hosts only.

---

## 📚 CySA+ Exam Relevance

This lab directly maps to the following CompTIA CySA+ (CS0-003) exam domains:

| Domain | Coverage |
|---|---|
| Security Operations (33%) | Network scanning, host discovery, traffic analysis |
| Vulnerability Management (30%) | CVE identification, CVSS scoring, risk prioritization |
| Incident Response (20%) | Understanding attack surface for IR planning |
| Reporting & Communication (17%) | Documented findings and remediation recommendations |

---

## 🔑 Technical Notes

> **VMware Lab Note:** Nmap scans in this VMware Host-Only environment hang indefinitely when DNS resolution is enabled. The `-n` flag (disable DNS resolution) is required for all scans in this lab setup. This is a known VMware Host-Only network behavior and does not affect real-world scan performance.

**Working scan command for this lab:**
```bash
sudo nmap -sT -Pn --disable-arp-ping -n -F [target IP]
```

---

## 📌 About This Project

This lab is part of an ongoing cybersecurity portfolio being built during active CySA+ exam preparation. Additional repositories covering Wireshark analysis, Splunk SIEM detection, OpenVAS vulnerability scanning, and incident response simulations will be added as they are completed.

**Related repositories:**
- `CySA-Home-Lab` — Full 30-day lab plan with all tools and exercises
- `Splunk-SIEM-Lab` — Coming soon
- `OpenVAS-Vulnerability-Assessment` — Coming soon

---

## 👤 Author

**Dashane James**  
Senior Field Service Technician → Cybersecurity Analyst  
📍 Yonkers, NY  
🎓 B.S. Information Technology — SUNY Canton  
🏆 CompTIA Security+ | CySA+ (In Progress)  
🔗 [GitHub](https://github.com/Dashanejames1) | [Zero Trust Cyber Security Brand](https://www.instagram.com/zerotrust_cybersecurity)

---
