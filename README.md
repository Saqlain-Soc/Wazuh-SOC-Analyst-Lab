# 🛡️ WAZUH SOC HOME LAB

![Cybersecurity](https://img.shields.io/badge/Focus-Cybersecurity-blue)
![Wazuh](https://img.shields.io/badge/SIEM-Wazuh-005571)
![Kali Linux](https://img.shields.io/badge/Attacker-Kali%20Linux-557C94)
![Blue Team](https://img.shields.io/badge/Domain-Blue%20Team-navy)
![Status](https://img.shields.io/badge/Status-Completed-brightgreen)

SIEM Deployment, Detection Engineering & Automated Incident Response

*WAZUH | SOC ANALYSIS | THREAT DETECTION | ACTIVE RESPONSE*

---

## 🔹 Lab Overview

| **Field** | **Details** |
| :--- | :--- |
| 👨‍💻 **Analyst** | **Saqlain Abbas** |
| 🧪 **Project** | **Wazuh SOC Home Lab** |
| 🛰️ **SIEM Platform** | **Wazuh (Manager, Indexer, Dashboard)** |
| 💻 **Windows Endpoint** | `MYDFIR-Saqo` |
| 🐧 **Linux Endpoint** | `saqo-linux` |
| 🔴 **Attacker Machine** | **Kali Linux** |
| 🖥️ **Virtualization** | **VirtualBox** |
| 🔐 **Authorization** | ✅ **Personal Lab Environment** |
| 📌 **Assessment Type** | **Detection Engineering & SOC Investigation** |
| ✅ **Status** | **Completed** |

---

## 1. ⚠️ Liability Disclaimer

All activities documented in this repository were performed inside an **isolated personal lab environment** built entirely on virtual machines that I own. No external, production, or third-party systems were targeted at any point. All attack simulations were self-generated for the purpose of validating detection and response capability.

---

## 2. 📖 Introduction

This project documents the end-to-end build of a functional **Security Operations Center home lab** using the Wazuh open-source SIEM/XDR platform.

The objective was not simply to install a SIEM, but to complete the full defensive cycle:

> **Deploy → Collect Telemetry → Simulate Attacks → Engineer Detections → Automate Response → Investigate & Report**

The lab monitors a Windows and a Linux endpoint, with a Kali Linux machine used to generate adversarial activity. Custom detection rules were authored, and Wazuh **Active Response** was configured to automatically mitigate brute-force attacks at the firewall level.

---

## 🛠️ 3. Lab Architecture

| **Component** | **Role** | **Function** |
| :--- | :--- | :--- |
| 🛰️ **Wazuh Manager** | SIEM Core | Rule processing, correlation, alerting |
| 📊 **Wazuh Indexer + Dashboard** | Visualization | Log storage, search, dashboards |
| 💻 **MYDFIR-Saqo** | Windows Agent | Endpoint telemetry source |
| 🐧 **saqo-linux** | Linux Agent | SSH / syslog telemetry source |
| 🔴 **Kali Linux** | Attacker | Attack simulation |
| 🖥️ **VirtualBox** | Hypervisor | Isolated virtual network |

---

## 🔍 4. Activities Performed

### 4.1 Wazuh Deployment

Deployed the full Wazuh stack — Manager, Indexer, and Dashboard — and verified that all core services were running and reachable via the web interface.

### 4.2 Agent Enrollment

Installed and enrolled Wazuh agents on both the Windows and Linux endpoints, confirming successful registration and active heartbeat with the manager.

**📸 Evidence — Agent Status**

![Active Agents](Active%20Agents.png)

> **Result:** 2 agents active | 0 disconnected | 0 pending | 0 never connected

### 4.3 Log Collection & Telemetry Validation

Confirmed that Windows Security events and Linux authentication logs were being ingested and indexed under the `wazuh-alerts-*` index pattern.

### 4.4 Attack Simulation

Generated adversarial activity from the Kali attacker VM and directly on the Windows endpoint, including account manipulation, scripting-file drops, and an SSH brute-force campaign against the Linux host.

### 4.5 Detection Engineering

Authored a **custom Wazuh rule** to correlate repeated SSH authentication failures originating from a single source IP within a short time window — converting several low-value events into one high-confidence alert.

### 4.6 Active Response Configuration

Configured the `firewall-drop` Active Response script to trigger automatically upon the custom rule firing, blocking the attacking host at the OS firewall level without analyst intervention.

### 4.7 SOC Dashboard Development

Built a custom dashboard — *MYDFIR-Saqo Basic SOC Activity Overview* — visualizing failed Windows logons, Windows account changes over time, and Linux SSH authentication failures.

**📸 Evidence — Custom SOC Dashboard**

![SOC Dashboard](Dashboard.png)

---

## 🎯 5. Detections Achieved

| **#** | **Detection** | **Host** | **Technique** |
| :---: | :--- | :--- | :--- |
| 01 | **Guest account enabled** | `MYDFIR-Saqo` | Account Manipulation |
| 02 | **Windows account change cluster** (4722, 4724, 4725, 4732, 4733, 4738) | `MYDFIR-Saqo` | Privilege Escalation |
| 03 | **Scripting file created in Temp/User folder** | `MYDFIR-Saqo` | Execution / Staging |
| 04 | **Failed Windows logon activity** (12 events) | `MYDFIR-Saqo` | Credential Access |
| 05 | **SSH brute-force attempts** | `saqo-linux` | Brute Force |
| 06 | **Custom rule — multiple SSH failures, same source IP** | `saqo-linux` | Correlated Detection |
| 07 | **Active Response — host blocked via firewall-drop** | `saqo-linux` | Automated Mitigation |

**📸 Evidence — Windows Detection Activity**

![Detection](Detection.png)

**📸 Evidence — SSH Brute Force Detection & Automated IP Block**
![Active Response](Active%20Response%20Ip%20Block.png)


> **Response Time:** The `firewall-drop` Active Response executed approximately **300 milliseconds** after the custom detection rule fired.

---

## ⚠️ 6. Risk Analysis / Impact

| **#** | **Finding** | **Observation** | **Potential Impact** | **Risk Level** |
| :---: | :--- | :--- | :--- | :---: |
| 01 | **Guest account enabled** | Default-disabled account activated | Expanded attack surface | <span style="color:orange">●</span> **Medium** |
| 02 | **Rapid account privilege changes** | Cluster of account-management events | Potential privilege escalation | <span style="color:red">●</span> **High** |
| 03 | **Scripting files in Temp folder** | Script drops in user-writable path | Payload staging / execution | <span style="color:red">●</span> **High** |
| 04 | **SSH brute-force attempts** | Repeated auth failures, single source | Unauthorized access attempt | <span style="color:red">●</span> **High** |
| 05 | **Failed Windows logons** | 12 failed attempts recorded | Credential guessing | <span style="color:orange">●</span> **Medium** |

### 🔑 Risk Level Key

<span style="color:red">●</span> **High**    <span style="color:orange">●</span> **Medium**    <span style="color:green">●</span> **Low**

> **Note:** *All activity was intentionally self-generated within an isolated lab. These findings represent detection validation, not a real-world compromise.*

---

## 🛡️ 7. Recommendations

* Keep the **Guest account disabled** by default and alert on unexpected enablement (Event ID 4722)
* Monitor **account-management event IDs as a group** (4720, 4722, 4724, 4725, 4726, 4732, 4733, 4738) and investigate tight clusters on a single host
* Alert on **script file creation in Temp and user-writable directories** and correlate with subsequent account activity
* Enforce **threshold-based alerting** on repeated failed Windows logons per host
* Harden SSH: prefer **key-based authentication**, restrict access to trusted networks, and disable direct root login
* Deploy layered brute-force protection — keep **Active Response** enabled alongside OS-level controls such as `fail2ban`
* Keep custom detection rules **documented and version-controlled**; re-test after every Wazuh upgrade
* Periodically **validate Active Response** to confirm blocks are applied and logged correctly

---

## ✅ 8. Conclusion

This lab moved me beyond simply reading about SIEM concepts into actually operating one. I deployed the platform, onboarded endpoints, generated realistic adversarial telemetry, and then had to **investigate my own attacks as an analyst would** — correlating events across two operating systems into a coherent timeline.

The most valuable outcome was understanding the gap between **generating an event** and **detecting an event**. A single Windows security log is rarely meaningful on its own; the analytical value comes from correlation, context, and a well-authored detection rule. Writing a custom rule and watching an automated response fire within milliseconds made the detect-and-respond lifecycle concrete rather than theoretical.

---

## 📊 9. Project Summary

| **Category** | **Details** |
| :--- | :--- |
| **Project** | Wazuh SOC Home Lab |
| **Focus** | SIEM Deployment, Detection Engineering, Incident Response |
| **SIEM Platform** | Wazuh |
| **Endpoints Monitored** | 2 (Windows + Linux) |
| **Attacker Platform** | Kali Linux |
| **Custom Rules Authored** | ✅ Yes |
| **Active Response** | ✅ `firewall-drop` |
| **Custom Dashboards** | ✅ Yes |
| **Investigation Report** | ✅ Included |
| **Status** | **Completed** |

---

## 📁 10. Repository Contents

```
wazuh-soc-home-lab/
├── README.md
├── SOC-Investigation-Report.pdf
├── rules/
│   └── local_rules.xml
├── config/
│   └── ossec.conf (sanitized)
└── screenshots/
    ├── Active_Agents.png
    ├── Dashboard.png
    ├── Detection.png
    └── Active_Response_Ip_Block.png
```

---

## 👤 Author

### Saqlain Abbas

**🔐 Cybersecurity / SOC Analyst**

> `Deploy → Detect → Investigate → Respond`

This repository forms part of my practical cybersecurity learning portfolio and documents my hands-on SOC laboratory exercises.

<p align="center">
  <a href="www.linkedin.com/in/saqlain-abbas-a61b59414">
    <img src="https://img.shields.io/badge/🔵_LinkedIn-Professional%20Profile-0A66C2?style=for-the-badge" />
  </a>
  &nbsp;
  <a href="https://github.com/Saqlain-Soc">
    <img src="https://img.shields.io/badge/⚫_GitHub-Security%20Projects-181717?style=for-the-badge" />
  </a>
</p>

---
