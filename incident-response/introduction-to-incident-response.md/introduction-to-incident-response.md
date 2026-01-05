## Introduction to Incident Response

## 🎯 Objective

A lab to introduce the **core concepts of incident response**, familiarize them with the **incident response lifecycle**, and understand how basic threats on Windows systems are detected, analyzed, and responded to by SOC Analysts.

## **What is Incident Response?**

**Incident Response (IR)** is the structured approach to handle and manage the aftermath of a **security breach or cyberattack**, also known as an incident. It includes steps to:
- Detect the incident
- Contain the damage
- Investigate the root cause
- Recover normal operations
- Report and document findings

## **Incident Response Process (NIST SP 800-61 Rev. 2)**

The NIST Incident Response Lifecycle includes **4 main phases**:

| **Phase** | **Description** |
|----------------------------------|---------------------------------------------------------------------------------|
| **1. Preparation** | Establish policies, train team, and set up tools and logging mechanisms.       |
| **2. Detection and Analysis** | Identify potential incidents using logs, alerts, and anomaly detection systems.|
| **3. Containment, Eradication, and Recovery** | Isolate threats, remove malware/artifacts, and restore systems securely. |
| **4. Post-Incident Activity** | Conduct lessons learned, create reports, and improve incident response plans. |

## **Common Types of Incidents on Windows:**

## **Common Types of Incidents Across Platforms**

| **Platform** | **Incident Type**                             | **Description**                                                                 |
|--------------|-----------------------------------------------|---------------------------------------------------------------------------------|
| **Windows**  | Unauthorized Login Attempts                   | Multiple failed logins or brute-force via RDP or local access.                  |
|              | PowerShell-based Attacks                      | Obfuscated or encoded PowerShell commands for exploitation or lateral movement. |
|              | Malware/Ransomware Execution                  | Malicious EXE/DLL files executed, causing encryption or data theft.             |
|              | Credential Dumping                            | Use of tools like Mimikatz to extract credentials from LSASS memory.            |
|              | Lateral Movement via WMI or RDP               | Internal system traversal using Windows services and accounts.                  |
| **Linux**    | SSH Brute Force or Unauthorized Access        | Repeated login attempts or access from unknown IPs via SSH.                     |
|              | Malicious Cron Jobs for Persistence           | Scheduled tasks created by attackers to maintain access.                        |
|              | Sudo Abuse / Privilege Escalation             | Exploiting weak sudo rules or known vulnerabilities to gain root privileges.    |
|              | Web Shell Upload                              | Attackers upload backdoors via vulnerable web applications.                     |
|              | Crypto Miner Installation                     | Hidden mining software running under compromised user accounts.                 |
| **AWS**      | IAM Credential Misuse                         | Leaked API keys used from unknown geographies.                                  |
|              | Root User Console Access                      | Suspicious login or activity from the AWS root account.                         |
|              | Unusual S3 Bucket Access                      | Public or unauthorized access to private storage.                               |
|              | CloudTrail or Logging Disabled                | Logging service turned off to hide attacker activity.                           |
|              | EC2 Abuse for Command and Control             | EC2 instances being used as a staging point or C2 server.                       |
| **Network**  | DDoS Attack                                   | Flood of traffic targeting public-facing services.                              |
|              | Port Scanning / Reconnaissance                | Continuous scanning of open ports across network segments.                      |
|              | ARP Spoofing / Man-in-the-Middle (MitM)       | Tampering with traffic flows for interception.                                  |
|              | DNS Tunneling                                 | Covert data exfiltration using DNS queries.                                     |
|              | Unauthorized VPN Access                       | Compromised VPN credentials used for internal access.                           |
| **Email**    | Phishing / Credential Harvesting              | Fake emails with links to steal user credentials.                               |
|              | Malware via Attachments                       | Office or ZIP files with embedded malware.                                      |
|              | Business Email Compromise (BEC)               | Attacker impersonates an executive to trick employees.                          |
|              | Spoofed Email Domains                         | Sending emails from domains that look like legitimate ones.                     |
|              | Internal Account Compromise                   | Compromised internal mailbox used to spread phishing internally.                |

## **How SOC Analysts Respond to Incidents:**
- Monitor logs and alerts (via SIEM)
- Investigate suspicious behavior
- Contain and isolate infected systems
- Coordinate with other teams
- Document and report the incident

## Detect and Respond: Windows Server RDP Brute Force Attack

## 🧰Lab Setup and Requirements

### 🖥️Machines Required:

- **VirtualBox Ubuntu Server**
  - ssh
  - /var/log/journal/  access
  - One local user account with known username
- **Kali Linux VM**
  - Hydra pre-installed
  - Connected to same LAN or Virtual Network

### 📶Network:
- Ensure **both machines are on the same network**

## **Step 1: Simulate SSH failed login attempt using hydra**

1. On Kali Linux:
Install Hydra (if not installed):

```bash
sudo apt update && sudo apt install hydra
```

Prepare Wordlist: Use the existing list or create a custom one

2. Run command on Kali Linux:

```bash
hydra -l attackerlab -P /usr/share/wordlists/rockyou.txt ssh://<VICTIM_IP>
```

Runs the program to try every word in the rockyou.txt file

## **Step 2: Respond to incident with basic Incident Response procedure**

View logs

```bash
sudo journalctl -u ssh | tail
```

- On systemd-based systems, SSH authentication events are stored in the system journal rather than /var/log/auth.log.

![Journal Failed SSH Simulations](../../references/journal-failed-ssh-simulations.png)

Option 1: Block an IP using UFW

```bash
sudo ufw deny from <MALICIOUS IP>
```

- Blocks all incoming traffic from 10.0.2.2
- Applies instantly
- Persists after reboot

Verify:

```bash
sudo ufw status numbered
```

Option 2: Temporary Block

```bash 
sudo iptables -A INPUT -s <MALICIOUS IP> -j DROP
```

- ⚠️ Reboot clears this rule.

Option 3: Automated Blocking

Real environments use tools like:

- Fail2Ban
- Wazuh active response
- CrowdSec

Example logic:
“If 5 failed SSH logins in 5 minutes → auto-block IP for 1 hour”

## Incident Report

Incident Severity: Medium
Reason: Repeated failed authentication attempts with no successful compromise.
Affected System: Ubuntu Server
Source IP: <MALICIOUS_IP>

Containment Action Taken:

Following multiple failed SSH authentication attempts from source IP <MALICIOUS IP>, the IP was blocked at the host firewall level using UFW to prevent further access attempts. No further suspicious activity observed post-mitigation.

## ✅Conclusion
This lab demonstrated the identification, analysis, and containment of a simulated SSH brute-force attempt. Using system logs and firewall controls, the incident was detected, validated, and mitigated without service disruption. This exercise reinforces core incident response concepts including detection, containment, and documentation, aligned with the NIST 800-61 framework.

- Simulate an SSH brute-force attack using Hydra
- Detect suspicious logons via /var/log/journal/ in ubuntu server
- Respond with UFW and basic IR