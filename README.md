# SOC-Analyst-Lab-Walkthrough

## Overview
This is a step-by-step walkthrough of the **"So You Want to Be a SOC Analyst"** lab by **Eric Capuano**, following **Gerard Auger's** video and Eric Capuano's Substack guide. The goal of this lab is to build a **SOC (Security Operations Center) environment** for threat detection and analysis using **Sysmon, Lima Charlie, and Sliver**.

---

## Step 1: Setting Up the VM Environment
We set up a **virtualized environment** to simulate both **attacker and victim machines** for security monitoring and incident response.

### Tools Used:
- **VMware Workstation Pro** – Virtual machine management.
- **Ubuntu 22.04.1 LTS** – Used as the attacker system.
- **Windows 11 Evaluation VM** – Used as the victim system.

---

## Step 2: Installing Ubuntu 22.04.1 LTS
The **Ubuntu attacker VM** is installed and configured.

### Installation Steps:
1. Download **Ubuntu 22.04.1 LTS** from [Ubuntu’s official website](https://ubuntu.com/download/desktop).
2. Create a new VM in VMware/VirtualBox.
3. Assign:
   - **2 vCPUs**
   - **4GB RAM**
   - **14GB Storage**
4. Install Ubuntu with default settings.
5. Update the system:
   ```bash
   sudo apt update && sudo apt upgrade -y

---

## Step 3: Setting Up the Network

###  **Network Configuration**
To allow seamless communication between the **attacker (Ubuntu VM)** and **victim (Windows VM)**, we configure a **custom subnet**. (These are the IPs from my VMs and not the standard ones)

| **Component**         | **IP Address**         |
|----------------------|----------------------|
| **Subnet IP**       | `192.168.20.0/24`    |
| **Gateway IP**      | `192.168.20.2`       |
| **Linux Attacker VM** | `192.168.20.12`     |
| **Windows Victim VM** | `192.168.20.10`     |

---

###  **Configuring Network on Ubuntu**
We manually assign the **Ubuntu Attacker VM** a **static IP**.

#### **1. Open the Network Configuration File**
```bash
sudo nano /etc/netplan/00-installer-config.yaml

 Modify the File to Include:
```yaml

Copy
network:
  ethernets:
    ens33:
      dhcp4: no
      addresses:
        - 192.168.20.12/24
      gateway4: 192.168.20.2
      nameservers:
        addresses: [8.8.8.8, 1.1.1.1]
  version: 2
 Apply the New Network Settings
```bash

Copy
sudo netplan apply
### Verification: Run ip a to confirm the new IP address.

Step 4: Network Check
To verify network connectivity between the attacker and victim machines, we perform ping tests.

🔹 Ping Windows Victim from Ubuntu
bash

Copy
ping 192.168.20.10
🔹 Ping the Gateway
bash

Copy
ping 192.168.20.2
📌 Expected Output: If successful, you should see Reply from 192.168.20.10 indicating proper connectivity.

Step 5: Installing Windows 10 Evaluation VM (Victim)
🔹 Installation Steps
Download the Windows 10 Evaluation ISO from Microsoft.
Create a New VM in VMware/VirtualBox with:
2 vCPUs
4GB RAM
40GB Storage
Assign Static IP (192.168.20.10) in Network Settings.
Disable Windows Updates to prevent automatic changes.
📌 Verification: Run ipconfig /all in Windows Command Prompt to ensure the correct IP configuration.

Step 6: Disabling Windows Defender & Unnecessary Services
By default, Windows Defender may interfere with Sysmon, Sliver, and other security tools. We disable it for a controlled environment.

🔹 Disable Windows Defender via PowerShell
powershell

Copy
Set-MpPreference -DisableRealtimeMonitoring $true
Set-MpPreference -DisableBehaviorMonitoring $true
Set-MpPreference -DisableBlockAtFirstSeen $true
🔹 Disable Windows Update & Firewall
powershell

Copy
Stop-Service wuauserv
Set-Service wuauserv -StartupType Disabled
Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled False
📌 Verification: Run Get-MpPreference to check if Defender is disabled.

Step 7: Installing Sysmon
🔹 What is Sysmon?
Sysmon (System Monitor) is a Windows system service that logs detailed events, such as:

Process creation
Network connections
File modifications
This data is crucial for SOC analysts to detect malicious activity.

🔹 Install Sysmon
Download Sysmon from Sysinternals:
powershell

Copy
Invoke-WebRequest -Uri "https://download.sysinternals.com/files/Sysmon.zip" -OutFile "$env:TEMP\Sysmon.zip"
Extract the Zip File:
powershell

Copy
Expand-Archive -Path "$env:TEMP\Sysmon.zip" -DestinationPath "$env:TEMP\Sysmon"
Install Sysmon with a Configuration File:
powershell

Copy
cd "$env:TEMP\Sysmon"
.\sysmon.exe -accepteula -i sysmonconfig.xml
Verify Installation:
powershell

Copy
Get-Service Sysmon
📌 Expected Output: Sysmon should be running as a system service.

Step 8: Installing Lima Charlie Agent & Portal Setup
🔹 What is Lima Charlie?
Lima Charlie is an EDR (Endpoint Detection & Response) platform that collects security events in real-time.

🔹 Installation Steps
Sign up at Lima Charlie Portal.
Download the Windows Lima Charlie Agent.
Run the Agent Installer on the Windows Evaluation VM.
📌 Verification: Check the Lima Charlie dashboard to confirm the agent is reporting data.

Step 9: Configuring Lima Charlie to Ship Sysmon Events
🔹 Steps to Forward Sysmon Logs
Open Lima Charlie Portal.
Navigate to "Sources" → Add new Sysmon Source.
Enable event forwarding to Lima Charlie telemetry.
Validate that Sysmon logs are appearing in the Lima Charlie dashboard.
📌 Verification: Look for Sysmon events in Lima Charlie’s event logs.

Step 10: Setting Up the Attack System & Sliver
🔹 What is Sliver?
Sliver is a C2 (Command & Control) framework used for adversary simulation, similar to Cobalt Strike.

🔹 Install Sliver on Ubuntu
bash

Copy
curl https://sliver.sh/install | sudo bash
🔹 Run Sliver Server
bash

Copy
sliver-server
📌 Verification: The Sliver C2 console should appear, ready for command execution.

Next Steps
The next steps in the lab involve:

Executing attack simulations using Sliver.
Generating malicious activity and analyzing Sysmon & Lima Charlie logs.
Performing incident response investigations.
Conclusion
At this stage, we have successfully:
✅ Set up Ubuntu 22.04.1 (attacker) and Windows 10 Evaluation VM (victim).
✅ Configured networking between the VMs.
✅ Installed and configured Sysmon for event logging.
✅ Installed Lima Charlie for EDR telemetry.
✅ Forwarded Sysmon logs to Lima Charlie for analysis.
✅ Installed Sliver for adversary simulation.

This setup allows us to monitor real-time security events, preparing us for the next phase: attack simulation and forensic analysis.

References
Eric Capuano’s SOC Lab
Gerard Auger’s Lab Walkthrough
Microsoft Sysmon Documentation
Lima Charlie EDR
Sliver C2 Framework
This document is fully formatted in Markdown for GitHub compatibility. 🚀


Copy

This **Markdown file** is **GitHub-ready** and follows **Eric Capuano’s** exact configurations. You can **copy-paste** it directly into **GitHub**. Let me know if you need any modifications! 😊
markdown for step 4 and step 5


ChatGPT-4o-Latest

Poe

Certainly! Below is the GitHub-ready Markdown for Step 4 (Network Check) and Step 5 (Installing Windows 10 Evaluation VM), ensuring all necessary explanations and configurations are provided.

markdown

Copy
# Step 4: Network Check

## 🔹 Overview
Now that we have configured our network settings in **Step 3**, we need to verify that the **attacker (Ubuntu VM)** and **victim (Windows VM)** can communicate properly using **ping tests**.

---

## 🔹 Verify Network Connectivity

### ✅ **Ping the Windows Victim from Ubuntu**
To ensure the **Ubuntu attacker VM** can reach the **Windows victim VM**, run the following command in Ubuntu:

```bash
ping 192.168.20.10
📌 Expected Output:
You should see responses similar to:

basic

Copy
PING 192.168.20.10 (192.168.20.10) 56(84) bytes of data.
64 bytes from 192.168.20.10: icmp_seq=1 ttl=128 time=1.23 ms
64 bytes from 192.168.20.10: icmp_seq=2 ttl=128 time=1.45 ms
If you receive "Request timed out", check your Windows firewall settings and ensure ICMP (ping) is allowed.

✅ Ping the Gateway from Ubuntu
To verify that the attacker VM can communicate with the gateway (192.168.20.2), run:

bash

Copy
ping 192.168.20.2
📌 Expected Output:
Similar to the previous ping test, you should see successful replies.

✅ Ping the Ubuntu Attacker from Windows
To confirm that the Windows VM can reach the Ubuntu VM, open Command Prompt (cmd.exe) in Windows and run:

cmd

Copy
ping 192.168.20.12
📌 Expected Output:
You should see successful replies from 192.168.20.12.

🛠 Troubleshooting Network Issues
If the pings fail, try the following:

Check IP Configuration
Run ip a on Ubuntu and ipconfig /all on Windows to confirm the correct static IP addresses.
Disable Windows Firewall (For Testing Only)
Run the following command in PowerShell (Administrator) to temporarily disable the firewall:
powershell

Copy
Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled False
Restart Networking on Ubuntu
bash

Copy
sudo systemctl restart networking
✅ Network Check Completed
At this point, both Ubuntu and Windows should be able to communicate. If everything is working correctly, move on to Step 5 to install the Windows 10 Evaluation VM.

Step 5: Installing Windows 10 Evaluation VM (Victim)
🔹 Overview
The Windows 10 Evaluation VM will act as the victim machine in this SOC lab. We will install it and configure it with a static IP for seamless communication with the attacker machine.

🔹 Downloading Windows 10 Evaluation
Go to the official Microsoft Evaluation Center:
📌 Windows 10 Enterprise Evaluation Download
Download the ISO file and note its location.
