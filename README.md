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
To allow seamless communication between the **attacker (Ubuntu VM)** and **victim (Windows VM)**, we configure a **custom subnet**.

| **Component**         | **IP Address**         |
|----------------------|----------------------|
| **Subnet IP**       | `192.168.20.0/24`    |
| **Gateway IP**      | `192.168.20.2`       |
| **Linux Attacker VM** | `192.168.20.12`     |
| **Windows Victim VM** | `192.168.20.10`     |

---

###  **Configuring Network on Ubuntu**
We manually assign the **Ubuntu Attacker VM** a **static IP**.

#### **1️ Open the Network Configuration File**
```bash
sudo nano /etc/netplan/00-installer-config.yaml
