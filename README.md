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
https://<Ubuntu-IP>
 
________________________________________


✅ Step 2 – Install TheHive (On Ubuntu Only)
Install Java:
sudo apt install openjdk-11-jdk -y
Install Cassandra:
sudo apt install cassandra -y
Install TheHive:
wget https://github.com/TheHive-Project/TheHive/releases/download/4.1.24/thehive_4.1.24-1_all.deb
sudo dpkg -i thehive_4.1.24-1_all.deb
sudo systemctl start thehive
Access:
http://<Ubuntu-IP>:9000

 ________________________________________
✅ Step 3 – Install CrowdSec (On Ubuntu Only)
curl -s https://packagecloud.io/install/repositories/crowdsec/crowdsec/script.deb.sh | sudo bash
sudo apt install crowdsec -y
Block attacker IP later using:
sudo cscli decisions add --ip <attacker-ip> --duration 24h
________________________________________

✅ Step 4 – Install Velociraptor Server (On Ubuntu Only)
wget https://github.com/Velocidex/velociraptor/releases/latest/download/velociraptor-v0.6.6-linux-amd64
chmod +x velociraptor-v0.6.6-linux-amd64
sudo ./velociraptor-v0.6.6-linux-amd64 gui
Access:
https://<Ubuntu-IP>:8889
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
 
________________________________________
✅ Step 2 – Install Velociraptor Agent
Download Windows binary.
Run as administrator:
velociraptor.exe client -c client.config.yaml
Connect to Ubuntu Velociraptor server.
 
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
 ________________________________________

