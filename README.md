# 🛡️ Cybersecurity Home Lab

> A personal, hands-on cybersecurity lab — built to learn by doing.

![Status](https://img.shields.io/badge/Status-In_Progress-orange?style=flat-square)
![Focus](https://img.shields.io/badge/Focus-Offensive_&_Defensive-red?style=flat-square)
![SIEM](https://img.shields.io/badge/SIEM-Splunk-FF5733?style=flat-square&logo=splunk)
![Platform](https://img.shields.io/badge/Platform-VirtualBox-183A61?style=flat-square&logo=virtualbox)
![License](https://img.shields.io/badge/License-MIT-yellow?style=flat-square)

---

## 🧠 What Is This?

This is my personal cybersecurity home lab — a safe, isolated environment where I simulate real-world attacks and practice detecting them.

Everything here is self-built, self-documented, and continuously evolving. The goal is simple: **learn cybersecurity by actually doing it**, not just reading about it.

---

## 🎯 What I'm Practising

| Area | Description |
|---|---|
| 🔴 **Offensive Security** | Simulating attacks — port scans, brute force, reverse shells |
| 🔵 **Defensive Security** | Monitoring logs and detecting threats using Splunk |
| 📋 **Log Analysis** | Collecting and analysing Windows Event Logs & Sysmon data |
| 🔍 **Threat Detection** | Writing Splunk queries (SPL) to catch malicious behaviour |

---

## 🗂️ What's Inside

```
cybersecurity-homelab/
│
├── 📁 homelab-setup/          →  How the lab is built (VMs, network, Splunk)
├── 📁 attack-simulations/     →  Step-by-step attack walkthroughs             
├── 📁 detection-queries/         →  Detection queries for real attack scenarios  (coming soon)

```

---

## 🔬 The Lab Environment

The lab runs entirely inside **Oracle VirtualBox** on a standard laptop/desktop — no special hardware needed.

```
  [ Kali Linux ]  ──attacks──►  [ Windows 11 ]  ──logs──►  [ Splunk ]
    Attacker                       Victim                    Detection
```

Two virtual machines talk to each other over a **completely isolated private network** — nothing touches the real internet during simulations.

---

## 📌 Current Progress

- [x] Lab environment set up (VirtualBox + 2 VMs)
- [x] Network isolated and configured
- [x] Snapshots created for clean resets
- [x] Splunk Universal Forwarder installed on Windows
- [x] Windows Event Logging + Sysmon enabled
- [ ] Port scan detection (Splunk query)
- [ ] Brute force detection (Splunk query)
- [ ] PowerShell abuse detection
- [ ] Reverse shell detection
- [ ] Attack simulation writeups

---

## 🚀 Why I Built This

Certifications teach theory. Labs teach instinct.

I built this to go beyond multiple-choice questions — to understand how attacks actually look in logs, what defenders see in real time, and how to write detections that catch real threats. Every folder in this repo is a lesson learned hands-on.

---

## 📖 Start Here

👉 **[homelab-setup/readme.md](./homelab-setup/readme.md)** — Full breakdown of how the lab is built, configured, and monitored.

---

## ⚠️ Disclaimer

Everything in this repository is for **educational purposes only**. All simulations are run inside a fully isolated virtual environment. No techniques here are used against any system without explicit permission.

---

*Built with curiosity. Defended with logic.*
