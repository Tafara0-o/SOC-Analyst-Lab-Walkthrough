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

Modify the network configuration file:

```yaml
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
Apply the changes:

bash

Copy
sudo netplan apply
Step 4: Network Check
Verify network connectivity between the machines.

✅ Ping the Windows Victim Machine from Ubuntu:
bash

Copy
ping 192.168.20.10
✅ Ping the Gateway:
bash

Copy
ping 192.168.20.2
Step 5: Installing Windows 10 Evaluation VM (Victim)
The Windows 10 Evaluation VM is set up as the victim machine, where we will install security monitoring tools.

🔹 Installation Steps:
Download the Windows 10 Evaluation ISO from Microsoft.
Install Windows using default settings.
Configure a Static IP (192.168.20.10).
Disable Windows Updates to prevent interference.
Step 6: Disabling Windows Defender & Unnecessary Services
To prevent interference with our tools, we disable Windows Defender and unnecessary services.

🔹 Disable Windows Defender via PowerShell:
powershell

Copy
Set-MpPreference -DisableRealtimeMonitoring $true
Set-MpPreference -DisableBehaviorMonitoring $true
Set-MpPreference -DisableBlockAtFirstSeen $true
🔹 Disable Windows Update & Firewall:
powershell

Copy
Stop-Service wuauserv
Set-Service wuauserv -StartupType Disabled
Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled False
Step 7: Installing Sysmon
Sysmon (System Monitor) provides detailed event logs for process creation, network connections, and file modifications.

🔹 Install Sysmon
Download Sysmon:
powershell

Copy
Invoke-WebRequest -Uri "https://download.sysinternals.com/files/Sysmon.zip" -OutFile "$env:TEMP\Sysmon.zip"
Extract and Install:
powershell

Copy
Expand-Archive -Path "$env:TEMP\Sysmon.zip" -DestinationPath "$env:TEMP\Sysmon"
cd "$env:TEMP\Sysmon"
.\sysmon.exe -accepteula -i sysmonconfig.xml
Verify Installation:
powershell

Copy
Get-Service Sysmon
Step 8: Installing Lima Charlie Agent & Portal Setup
Lima Charlie is an Endpoint Detection & Response (EDR) platform that collects security events.

🔹 Installation Steps:
Create an account on Lima Charlie Portal.
Download the Windows Lima Charlie agent.
Install the agent on the Windows Evaluation VM following the Lima Charlie guide.
Step 9: Configuring Lima Charlie to Ship Sysmon Events
Now that Sysmon is active, we configure Lima Charlie to collect and process Sysmon event logs.

🔹 Steps:
Open the Lima Charlie portal.
Navigate to "Sources" → Add New Sysmon Source.
Configure event forwarding to Lima Charlie’s telemetry system.
Validate that Sysmon logs are being collected and displayed in the Lima Charlie dashboard.
Step 10: Setting Up the Attack System & Sliver
The attack system is configured, and Sliver, a C2 (Command & Control) framework, is installed.

🔹 Installing Sliver on Ubuntu
bash

Copy
curl https://sliver.sh/install | sudo bash
🔹 Running Sliver
bash

Copy
sliver-server
Next Steps
The next steps in the lab involve:

Running attacker simulations using Sliver.
Generating malicious activity and monitoring detection via Sysmon & Lima Charlie.
Performing incident response investigations.
This documentation is formatted in Markdown for GitHub compatibility. 🚀

mipsasm

Copy

---

### **Changes & Improvements:**
✅ **Fixed Markdown Syntax** – Removed unnecessary "Copy" labels and ensured proper formatting.  
✅ **Consistent Code Blocks** – Used the appropriate syntax highlighting for Bash, PowerShell, and YAML.  
✅ **Improved Readability** – Added descriptions, separators, and bullet points for better structure.  
✅ **GitHub-Ready** – Fully optimized for **README.md**, **Wiki**, or **Documentation** use.  

Would you like me to add **screenshot placeholders** or additional details? 😊🚀
remove the emojis


ChatGPT-4o-Latest

Poe
Certainly! Below is the cleaned and properly formatted Markdown version of your lab write-up without emojis:

markdown

Copy
# SOC Analyst Lab Walkthrough

## Step 3: Configuring Network on Ubuntu

Modify the network configuration file:

```yaml
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
Apply the changes:

bash

Copy
sudo netplan apply
Step 4: Network Check
Verify network connectivity between the machines.

Ping the Windows Victim Machine from Ubuntu:
bash

Copy
ping 192.168.20.10
Ping the Gateway:
bash

Copy
ping 192.168.20.2
Step 5: Installing Windows 10 Evaluation VM (Victim)
The Windows 10 Evaluation VM is set up as the victim machine, where we will install security monitoring tools.

Installation Steps:
Download the Windows 10 Evaluation ISO from Microsoft.
Install Windows using default settings.
Configure a Static IP (192.168.20.10).
Disable Windows Updates to prevent interference.
Step 6: Disabling Windows Defender & Unnecessary Services
To prevent interference with our tools, we disable Windows Defender and unnecessary services.

Disable Windows Defender via PowerShell:
powershell

Copy
Set-MpPreference -DisableRealtimeMonitoring $true
Set-MpPreference -DisableBehaviorMonitoring $true
Set-MpPreference -DisableBlockAtFirstSeen $true
Disable Windows Update & Firewall:
powershell

Copy
Stop-Service wuauserv
Set-Service wuauserv -StartupType Disabled
Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled False
Step 7: Installing Sysmon
Sysmon (System Monitor) provides detailed event logs for process creation, network connections, and file modifications.

Install Sysmon
Download Sysmon:
powershell

Copy
Invoke-WebRequest -Uri "https://download.sysinternals.com/files/Sysmon.zip" -OutFile "$env:TEMP\Sysmon.zip"
Extract and Install:
powershell

Copy
Expand-Archive -Path "$env:TEMP\Sysmon.zip" -DestinationPath "$env:TEMP\Sysmon"
cd "$env:TEMP\Sysmon"
.\sysmon.exe -accepteula -i sysmonconfig.xml
Verify Installation:
powershell

Copy
Get-Service Sysmon
Step 8: Installing Lima Charlie Agent & Portal Setup
Lima Charlie is an Endpoint Detection & Response (EDR) platform that collects security events.

Installation Steps:
Create an account on Lima Charlie Portal.
Download the Windows Lima Charlie agent.
Install the agent on the Windows Evaluation VM following the Lima Charlie guide.
Step 9: Configuring Lima Charlie to Ship Sysmon Events
Now that Sysmon is active, we configure Lima Charlie to collect and process Sysmon event logs.

Steps:
Open the Lima Charlie portal.
Navigate to "Sources" → Add New Sysmon Source.
Configure event forwarding to Lima Charlie’s telemetry system.
Validate that Sysmon logs are being collected and displayed in the Lima Charlie dashboard.
Step 10: Setting Up the Attack System & Sliver
The attack system is configured, and Sliver, a C2 (Command & Control) framework, is installed.

Installing Sliver on Ubuntu
bash

Copy
curl https://sliver.sh/install | sudo bash
Running Sliver
bash

Copy
sliver-server
Next Steps
The next steps in the lab involve:

Running attacker simulations using Sliver.
Generating malicious activity and monitoring detection via Sysmon & Lima Charlie.
Performing incident response investigations.
