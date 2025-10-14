 # 🖥️ Windows Server 2019 Active Directory Home Lab

This beginner-friendly project walks through setting up a functional Active Directory (AD) environment using Windows Server 2019 and VirtualBox. You'll install a Domain Controller and join a Windows 10 client to the domain.

---

## 📌 Requirements

- VirtualBox (latest version): [https://www.virtualbox.org/wiki/Downloads](https://www.virtualbox.org/wiki/Downloads)
- Windows Server 2019 ISO: [Microsoft Evaluation Center](https://www.microsoft.com/en-us/evalcenter/evaluate-windows-server-2019)
- Windows 10 ISO: [Download Windows 10 Disc Image (ISO File)](https://www.microsoft.com/en-us/software-download/windows10ISO)
- At least 8GB RAM (16GB recommended)
- At least 100GB free disk space
- Modern CPU with virtualization enabled (check BIOS settings)

---

## 🧪 Lab Overview

| Component         | Details                                |
|------------------|----------------------------------------|
| Domain Name       | `corp.local`                          |
| Server Name       | `DC-Server`                           |
| Windows Server    | Server 2019 Standard Evaluation ISO   |
| Client OS         | Windows 10                            |
| Hypervisor        | VirtualBox                            |

---

## ⚙️ Setup Steps

### 📦 Step 1: Install VirtualBox

- Download and install VirtualBox from the official site.
<img width="1915" height="1038" alt="image" src="https://github.com/user-attachments/assets/82cd20d3-0acd-45d7-83cb-8ef246c49375" />

---

### 💽 Step 2: Create a VM for Windows Server 2019

- Open VirtualBox and create a new VM
- Assign 4GB RAM (or more)
- Choose “Create a virtual hard disk now” (min. 60GB)
- Attach the Server 2019 ISO as a virtual optical disk

---

### 🪟 Step 3: Install Windows Server 2019

- Boot into the ISO
- Select language and install version (Standard Edition with Desktop Experience)
- Set administrator password during installation
- Login to the system after reboot

---

### 🛡️ Step 4: Install Active Directory Domain Services
📸 Screenshot Example:

After installation, your **Server Manager** dashboard will show a new "AD DS" role added in the left pane. This confirms that Active Directory Domain Services is installed.

Save as: `screenshots/server-manager-dashboard.png`

1. Open **Server Manager**
2. Click **Add Roles and Features**
3. Select `Active Directory Domain Services (AD DS)`
4. Follow the wizard, then click **Promote this server to a domain controller**
5. Create a new forest and domain (e.g., `corp.local`)
6. In the **Deployment Configuration** window, choose “Add a new forest” and enter your root domain name (e.g., `corp.local`).
7. On the **Domain Controller Options** screen, select Forest functional level and Domain functional level (keep default: Windows Server 2016), and set a **DSRM password**.
8. Prerequisites will be checked. If all pass, click **Install** to begin domain controller promotion.
9. Server will reboot automatically once setup is complete.


📸 Screenshots:
- `screenshots/ad-role-add.png`
- `screenshots/domain-setup.png`


🗂️ Step 5: Create Organizational Units (OUs)
📸 Screenshot Example: Display the OU structure in **Active Directory Users and Computers** showing your new OUs (e.g., `Users`, `Computers`, `IT`, `HR`, `Sales`).

Save as: `screenshots/ou-structure.png`

Creating OUs helps you organize users and computers logically. This allows for better Group Policy application and delegated administration.

You can create the OUs manually using **Active Directory Users and Computers**

#### 🧩 Example OU Structure:
```
corp.local
├── Users
│   ├── IT
│   ├── HR
│   ├── Sales
├── Computers
│   ├── IT-Computers
│   ├── HR-Computers
│   ├── Sales-Computers
```


### 🌐 Step 6: Configure Static IP  

Steps:
1. Open **Control Panel** and go to:
   `Network and Internet > Network and Sharing Center > Change adapter settings`
2. Right-click your **Ethernet** adapter and choose **Properties**
3. Double-click on `Internet Protocol Version 4 (TCP/IPv4)`
4. Select **Use the following IP address** and enter:
   - IP address: `192.168.1.2`
   - Subnet mask: `255.255.255.0`
   - Default gateway: `192.168.1.1`
   - Preferred DNS server: `127.0.0.1`
5. Click **OK** to apply changes

This ensures your Windows Server has a static IP address, which is required for reliable DNS and domain controller functionality.

📸 Screenshot Examples:
- `screenshots/control-panel-network.png`
- `screenshots/network-adapter-properties.png`
- `screenshots/ipv4-settings.png`

### 🧑‍💼 Step 7: Add Users with PowerShell  

Steps:
1. Write a PowerShell script to bulk-create users from a CSV
2. Download the sample PowerShell script and CSV file from this repository:

bulk-user-creation.ps1 
3.Customize the users.csv file with names, usernames, departments, and OUs.

4. Run the PowerShell script as Administrator on your domain controller:
5. Save and run the script in PowerShell as Admin
6. Verify users are created under the correct OUs

📸 Screenshot Example:  
- `screenshots/powershell-bulk-users.png`
---


## 🧩 Step 8: Configure Static IP and Join Client to Domain



### 🔧 Part A: Set Static IP for Windows 10 Client

1. On the Windows 10 client, go to **Control Panel** > **Network and Sharing Center**  
2. Click **Change adapter settings** on the left pane  
3. Right-click your active Ethernet connection and select **Properties**  
4. Select **Internet Protocol Version 4 (TCP/IPv4)** and click **Properties**  
5. Choose **Use the following IP address** and set:

   - **IP address:** `192.168.1.10` *(or any IP in the same subnet as your server)*
   - **Subnet mask:** `255.255.255.0`
   - **Default gateway:** `192.168.1.1` *(optional, needed if using internet)*
   
6. Set **Preferred DNS server** to your **Domain Controller’s IP** (e.g., `192.168.1.2`)  
7. Click **OK**, then **Close**

> 🛠️ *This step is critical so the client can resolve and locate the Domain Controller.*
- `screenshots/static-ip-settings.png`
- `screenshots/client-system-properties.png`

---

### 🤝 Part B: Join Windows 10 to the Domain

1. Go to **System Properties**  
   - Right-click **This PC** > **Properties**
   - Click **Advanced system settings**
   - Under the **Computer Name** tab, click **Change**

2. In the **Domain** field, enter your domain name (e.g., `corp.local`)  
3. Click **OK**  
4. When prompted, enter **Domain Admin credentials** (e.g., `corp\Administrator`)  
5. If successful, you will see: **"Welcome to the domain"**  
6. Click **OK** and restart the Windows 10 client  
7. On the login screen, click **Other User** and sign in as:  
   `corp\studentuser1`  
   *(Replace with the domain username you created earlier)*

✅ If login is successful, your Windows 10 client is now part of the domain and can be managed through Group Policy and Active Directory tools.
### 📸 Screenshots:
- `screenshots/domain-join-success.png`


📌 Note:
If you forget the password for the domain user account, you can reset it directly from the Windows Server VM using Active Directory Users and Computers:

Open Server Manager

Go to Tools → Active Directory Users and Computers

Navigate to the appropriate OU and locate the user account

Right-click the user and select Reset Password

Enter a new password, confirm it, and uncheck User must change password at next logon if needed

This will allow you to log back into the Windows 10 client using the updated credentials.

## 🏁 Completion

Once complete, this lab will help you showcase:
- VirtualBox VM setup and OS install
- Active Directory configuration
- User and OU management via GUI + PowerShell
- Domain join and client integration

---

> Created by Abdul Giwa | For portfolio and job-readiness demonstration


---



