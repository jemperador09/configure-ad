<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>On-premises Active Directory Deployed in the Cloud (Azure)</h1>
This tutorial outlines the implementation of on-premises Active Directory within Azure Virtual Machines.<br />


<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (21H2)

<h2>High-Level Deployment and Configuration Steps</h2>

- 1 ) Setup Resources in Azure
- 2 ) Ensure connectivity between Client and Domain Controller
- 3 ) Install Active Directory
- 4 ) Create an Admin and Normal User Account in Active Directory
- 5 ) Join Client to your Domain
- 6 ) Setup Remote Desktop for non-administrative users on Client
- 7 ) Create additional users and attempt to log into Client with one of the users

<h2>Deployment and Configuration Steps</h2>

<p>
<b>1 ) Setup Resources in Azure</b>
  
  - Create a Resource group
    - In resource group, create a virtual machine (this will be your Domain Controller)
      - Region: pick the region closest to you
      - Image: for our Domain Controller, choose Windows Server 2022
      - Size: up to you. More CPUs = faster processing
    - Create username and password (don't forget it!)
    - Click "Review + create" to create your Domain Controller
    
</p>
<p>
<img src="https://i.imgur.com/PY7SSVn.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
</p>
 <p>
   
  - Create another virtual machine (this will be your Client)
 </p>
<p>
<img src="https://i.imgur.com/Z6HbLRL.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
</p>
<p>

  - Before clicking "Review + create," make sure the virtual network is with you Domain Controller
</p>

<p>
<img src="https://i.imgur.com/PMQi9uo.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
</p>

<p>
  
  - Set Domain Controller's NIC Private IP address to be <b>Static</b>
    - In your DC's Settings, go to Networking -> Network Interface -> IP configurations
    - Click on "ipconfig" and change Private IP address settings to Static
</p>

<p>
<img src="https://i.imgur.com/SEkG4CG.png" height="30%" width="30%" alt="Disk Sanitization Steps"/>
</p>

<p>
<b>2 ) Ensure connectivity between Client and Domain Controller </b>

  - Login to Client-1 with Remote Desktop and ping DC-1’s private IP address with ping -t (perpetual ping)
<img src="https://i.imgur.com/meTWqbu.png" height="30%" width="30%" alt="Disk Sanitization Steps"/>

  - Notice how the ping "timed out." In order to fix this, we must enable ICMPv4 on the local windows Firewall in DC-1
    - Go into Windows Defender Firewall -> Inbound Rule -> Right click both "Core Networking Diagnostics - ICMP Echo Request (ICMPv4-In) to "Enable Rule"  
<img src="https://i.imgur.com/Ao72vS2.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

  - Check back at Client-1 to see the ping succeed
<img src="https://i.imgur.com/Ws6oVzT.png" height="30%" width="30%" alt="Disk Sanitization Steps"/>
</p>

<p>

<b>3 ) Install Active Directory</b>

  - Login to DC-1 and install Active Directory Domain Services
    - Service Manager -> click "Add roles and features"
    - Click next till you get to Server Roles, then check the box for Active Directory Domain Services
<img src="https://i.imgur.com/v49fbML.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>

  - Once features were added, keep clicking next and then Install
  - After installation, at the top right click the flag to reveal its popup menu
    - Click on "Promote this server to a domain controller"
<img src="https://i.imgur.com/oYYjOFd.png" height="30%" width="30%" alt="Disk Sanitization Steps"/>

  - To promote DC, setup a new forest as mydomain.com (can be anything, just remember what it is)
<img src="https://i.imgur.com/eU8rxhx.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>

  - Create a password and keep clicking next till Prerequisites Check passed successfully
  - Once passed, click Intall (DC will Restart once installed)
  - Restart and then log back into DC-1 as user: mydomain.com\(username)
</p>













