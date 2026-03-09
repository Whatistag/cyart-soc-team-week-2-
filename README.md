# SOC-OPERATIONS-REPORT

PART 1 – THEORETICAL KNOWLEDGE
________________________________________
1️⃣ Alert Priority Levels

🔹 Severity Levels

Critical

  Represents an immediate and severe threat to the organization. Usually involves active exploitation, ransomware, or data breaches requiring urgent response.

High

  Indicates serious security incidents such as unauthorized admin access or confirmed malware infection. Needs fast investigation but may not yet be causing major damage.

Medium

  Suspicious or potentially harmful activity like repeated login failures or brute-force attempts. Requires monitoring and analysis.

Low

  Minor or informational alerts such as port scans or policy violations. These usually have minimal business impact.
________________________________________
🔹 Assignment Criteria

Asset Criticality
  Priority depends on the importance of the affected system. A production server or domain controller is more critical than a test VM or user laptop.

Exploit Likelihood
  If a public exploit exists or the vulnerability is actively exploited, the priority increases significantly.

Business Impact
  Incidents that may cause financial loss, data exposure, or regulatory penalties are given higher priority.

🔹 CVSS Scoring
  CVSS (Common Vulnerability Scoring System) is a standardized method to measure vulnerability severity.

Example:

  Log4Shell had a CVSS score of 9.8, meaning it was critical because it allowed remote code execution and had a public exploit.

Severity Mapping:

•	9.0–10.0 → Critical

•	7.0–8.9 → High

•	4.0–6.9 → Medium

•	0.1–3.9 → Low

Reference frameworks:

•	FIRST (CVSS Guide)

•	NIST SP 800-61
________________________________________

2️⃣ Incident Classification

🔹 Incident Categories

Malware

  Malicious software infections like trojans, worms, ransomware, or spyware.

Phishing

  Fraudulent emails or messages designed to steal credentials or deliver malware.

DDoS (Distributed Denial of Service)

  Attack that overwhelms a system or network with traffic, causing service disruption.

Insider Threat

  Security risk originating from employees or authorized users misusing access.

Data Exfiltration

  Unauthorized transfer of sensitive data outside the organization.

🔹 MITRE ATT&CK Mapping

Framework developed by
MITRE Corporation
  MITRE ATT&CK provides a structured matrix of attacker tactics and techniques.

Examples:

•	Phishing → T1566

•	Exploit Public-Facing Application → T1190

This helps SOC analysts standardize detection and reporting.
________________________________________
3️⃣ Basic Incident Response Lifecycle

Based on NIST SP 800-61

Preparation
  
   Establish policies, tools, training, and response plans before incidents occur.

Identification
    Detect and confirm security incidents using alerts, logs, and analysis.

Containment

  Limit the damage by isolating affected systems and blocking malicious activity.

Eradication

   Remove malware, close vulnerabilities, and eliminate root cause.

Recovery

  Restore systems to normal operation and monitor for reinfection.

Lessons Learned

  Conduct post-incident review to improve future response and strengthen security controls.


# PART 2 – PRACTICAL IMPLEMENTATION
🖥️ COMPLETE LAB ARCHITECTURE

Create 4 Virtual Machines

Machine	OS	Purpose	Tools Installed

1️⃣ Kali Linux	Attacker	Launch attacks	Metasploit

2️⃣ Kali_wazuh Server	SOC Server	SIEM + Case Mgmt	Wazuh, TheHive, CrowdSec

3️⃣ Windows 10	Victim	Target machine	Wazuh Agent, Velociraptor Agent, FTK Imager

4️⃣ Metasploitable2	Vulnerable Server	Exploitable target	(Pre-installed vulnerable services)
________________________________________
🧱 MACHINE 1 – Kali Linux (Attacker)

Install:

•	Metasploit (already installed in Kali)

Verify:

    msfconsole
 <img width="682" height="530" alt="image" src="https://github.com/user-attachments/assets/d7d4696f-5a6d-47b3-b4e6-561cccc07c9a" />

🧱 MACHINE 2 – Kali_wazuh Server (SOC Machine)

This is Main SOC Server.

You will install:

•	Wazuh

•	TheHive

•	CrowdSec

•	Velociraptor (Server mode)
________________________________________
✅ Step 1 – Install Wazuh (On Ubuntu Only)

    curl -sO https://packages.wazuh.com/4.7/wazuh-install.sh

    sudo bash wazuh-install.sh -a

After install:

Access:

    https://<Kali_wazuh_Server-IP>
 <img width="941" height="452" alt="image" src="https://github.com/user-attachments/assets/126a1aed-a7d0-4479-8d05-68d86c7c0ac4" />

________________________________________


✅ Step 2 – Install TheHive (On Ubuntu Only)

Install Java:

    sudo apt install openjdk-11-jdk -y

Install Cassandra:

    sudo apt install cassandra -y

Install TheHive:

    sudo systemctl start thehive

Access:

http://<Kali_wazuh_server-IP>:9000
<img width="941" height="451" alt="image" src="https://github.com/user-attachments/assets/b47ee92a-d07f-483a-ae24-b6f7989f63dc" />

 ________________________________________
✅ Step 3 – Install CrowdSec (On Ubuntu Only)

    sudo apt install crowdsec -y

Block attacker IP later using:

    sudo cscli decisions add --ip <attacker-ip> --duration 24h
________________________________________

✅ Step 4 – Install Velociraptor Server (On Ubuntu Only)

    wget https://github.com/Velocidex/velociraptor/releases/latest/download/velociraptor-v0.6.6-linux-amd64

    chmod +x velociraptor-v0.6.6-linux-amd64

    sudo ./velociraptor-v0.6.6-linux-amd64 gui

Access:

    https://<Kali_wahuz_server-IP>:8889
<img width="941" height="452" alt="image" src="https://github.com/user-attachments/assets/27649475-1ae0-40e4-843d-17fcdea1fcee" />
 
 ________________________________________
🧱 MACHINE 3 – Windows 10 (Victim)

Install:

•	Wazuh Agent

•	Velociraptor Agent

•	FTK Imager
________________________________________
✅ Step 1 – Install Wazuh Agent

Download Windows Agent from:

    https://packages.wazuh.com

During installation:

Enter Ubuntu IP as Manager.

Then on Ubuntu:

    sudo /var/ossec/bin/manage_agents

Add agent → Copy key → Paste in Windows agent.

Restart Windows agent service.
<img width="665" height="349" alt="image" src="https://github.com/user-attachments/assets/740fbe8b-b682-47dd-ae1f-0bff587df6e5" />
 
________________________________________
✅ Step 2 – Install Velociraptor Agent

Download Windows binary.

Run as administrator:

    velociraptor.exe client -c client.config.yaml

Connect to Ubuntu Velociraptor server.
<img width="977" height="469" alt="image" src="https://github.com/user-attachments/assets/c017d5be-512a-4264-bd9a-15b5294a3e62" />

 
✅ Step 3 – Install FTK Imager

Download and install.

To collect memory:

File → Capture Memory → Save .mem file.

________________________________________
🧱 MACHINE 4 – Metasploitable2

No installation required.

Just:

•	Import into VirtualBox

•	Get IP address using:

    ifconfig
 <img width="941" height="522" alt="image" src="https://github.com/user-attachments/assets/d158ce8b-32bd-4c60-a623-9059277916ec" />

 ________________________________________

# SOC Alert Management Practice Report
1. Objective

The purpose of this exercise is to practice SOC alert handling by creating an alert classification system, prioritizing alerts using severity scoring, documenting response procedures, creating incident tickets, and practicing escalation processes.

2. Alert Classification System

<img width="1920" height="691" alt="SOC Alert Classification Matrix" src="https://github.com/user-attachments/assets/f7b9be45-9ad1-4fd6-8194-9a80e7b40b2a" />

Example Mock Alert

Alert message:

Phishing Email Detected – Suspicious Link
User received email containing link to malicious website.

Mapped to:

MITRE Technique → T1566 (Phishing)

3. Alert Prioritization Using CVSS

<img width="1024" height="521" alt="Screenshot (104)" src="https://github.com/user-attachments/assets/997800d0-d74d-4ee2-a360-bef00284a07a" />

4. Wazuh Alert Dashboard

In Wazuh:

<img width="1920" height="922" alt="windows_agent_failed_logon_attemp_alert_found" src="https://github.com/user-attachments/assets/72baa5dd-f3c2-4f03-9187-b93d15d8ff1a" />

5. Incident Ticket in TheHive

Create a case in TheHive.

Example incident ticket:

<img width="1920" height="922" alt="VirtualBox_kali_wazuh_05_03_2026_12_43_21" src="https://github.com/user-attachments/assets/0dce994d-e899-4a0b-85e2-ca8a87f98e62" />

Below is a **complete incident response report for a “Multiple SSH Login Failed” incident**. You can **copy-paste this into Google Docs** for your assignment.

---

# SOC Incident Response Report

## Incident Type: Multiple SSH Login Failures (Brute Force Attempt)

---

# 1. Executive Summary

On **18 August 2025**, the Security Operations Center detected multiple failed SSH login attempts on a Linux server. The alerts indicated a potential **brute-force attack** targeting SSH authentication. Investigation revealed repeated login failures from a suspicious external IP address attempting to gain unauthorized access. The SOC team analyzed system logs, confirmed the attack pattern, and blocked the malicious IP address to prevent further attempts. No successful login was detected, and no systems were compromised.

---

# 2. Timeline of Events

| Timestamp           | Action                                                |
| ------------------- | ----------------------------------------------------- |
| 2025-08-18 14:00:00 | SIEM generated alert for multiple SSH login failures  |
| 2025-08-18 14:05:00 | SOC analyst reviewed authentication logs              |
| 2025-08-18 14:10:00 | Suspicious IP address identified                      |
| 2025-08-18 14:15:00 | IP reputation checked on VirusTotal                   |
| 2025-08-18 14:20:00 | Firewall rule created to block malicious IP           |
| 2025-08-18 14:30:00 | Server monitoring continued to confirm attack stopped |

---

# 3. Impact Analysis

**Affected System**

* Linux server hosting internal services

**Attack Type**

* SSH brute force attempt

**Potential Risks**

* Unauthorized server access
* Credential compromise
* Data theft or system modification

**Actual Impact**

* No successful login occurred
* No system compromise detected

---

# 4. Remediation Steps

The SOC team implemented the following actions:

* Blocked the attacker IP address in the firewall
* Enabled SSH rate limiting
* Reviewed SSH authentication logs
* Enforced stronger password policies
* Implemented account lockout after multiple failed attempts
* Recommended enabling multi-factor authentication (MFA)

---

# 5. Investigation Log

| Timestamp           | Action                             |
| ------------------- | ---------------------------------- |
| 2025-08-18 14:00:00 | Alert received from SIEM           |
| 2025-08-18 14:05:00 | Reviewed `/var/log/auth.log`       |
| 2025-08-18 14:10:00 | Identified repeated login failures |
| 2025-08-18 14:15:00 | Extracted source IP address        |
| 2025-08-18 14:20:00 | Checked IP reputation              |
| 2025-08-18 14:25:00 | Blocked malicious IP               |
| 2025-08-18 14:30:00 | Confirmed attack stopped           |

---

# 6. SSH Brute Force Investigation Checklist

☑ Confirm SSH authentication logs

☑ Identify repeated failed login attempts

☑ Extract attacker IP address

☑ Check IP reputation using threat intelligence

☑ Block malicious IP address

☑ Verify no successful login occurred

☑ Strengthen SSH security policies

---

# 7. Incident Response Flow Diagram

You can create this diagram using **draw.io**.

Flow structure:

```
SSH Login Failure Alert
        ↓
SOC Analyst Investigation
        ↓
Review Authentication Logs
        ↓
Identify Suspicious IP
        ↓
Check IP Reputation
        ↓
Block Malicious IP
        ↓
Strengthen SSH Security
        ↓
Incident Closed
```

---

# 8. Post-Mortem (50 Words)

The SSH brute force attempt highlighted the importance of proactive monitoring and strong authentication controls. Although no compromise occurred, implementing stricter SSH security policies, such as multi-factor authentication and login attempt limits, will significantly reduce the risk of future attacks and improve the organization’s security posture.

---

# Optional (Good for SOC Portfolio)

You can also include a **sample log** from the Linux server:

```
Aug 18 14:02:10 server sshd[2156]: Failed password for root from 192.168.1.50 port 51432 ssh2
Aug 18 14:02:15 server sshd[2157]: Failed password for root from 192.168.1.50 port 51435 ssh2
Aug 18 14:02:20 server sshd[2158]: Failed password for root from 192.168.1.50 port 51440 ssh2
```

---

