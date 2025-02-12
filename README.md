# SOC-Analyst-Lab-Walkthrough

# SOC Analyst Lab Walkthrough

## Overview
This is a step-by-step walkthrough of the **"So You Want to Be a SOC Analyst"** lab by **Eric Capuano**, following **Gerard Auger's** video and Eric Capuano's Substack guide. The goal of this lab is to build a **SOC (Security Operations Center) environment** for threat detection and analysis using **Sysmon, Lima Charlie, and Sliver**.

---

## Step 1: Setting Up the VM Environment
We set up a **virtualized environment** to simulate both **attacker and victim machines** for security monitoring and incident response.

### Tools Used:
- **VMware Workstation / VirtualBox** – Virtual machine management.
- **Ubuntu 22.04.1 LTS** – Used as the attacker system.
- **Windows 10 Evaluation VM** – Used as the victim system.

---

## Step 2: Installing Ubuntu 22.04.1 LTS
The **Ubuntu attacker VM** is installed and configured.

### Installation Steps:
1. Download **Ubuntu 22.04.1 LTS** from [Ubuntu’s official website](https://ubuntu.com/download/desktop).
2. Create a new VM in VMware/VirtualBox.
3. Assign:
   - **2 vCPUs**
   - **4GB RAM**
   - **20GB Storage**
4. Install Ubuntu with default settings.
5. Update the system:
   ```bash
   sudo apt update && sudo apt upgrade -y
