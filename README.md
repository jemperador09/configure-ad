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
      - Image: for our Domain Controller, choose <b>Windows Server 2022</b>
      - Size: up to you. More CPUs = faster processing
    - Create username and password (don't forget it!)
    - Click "Review + create" to create your Domain Controller
    
</p>
<p>
<img src="https://i.imgur.com/PY7SSVn.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
</p>
 <p>
   
  - Create another virtual machine (this will be your Client)
    - Image: <b>Windows 10 Pro</b>
 </p>
<p>
<img src="https://i.imgur.com/Z6HbLRL.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
</p>
<p>

  - Before clicking "Review + create," make sure the virtual network is with your Domain Controller
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
  - Restart and then log back into DC-1 as user: mydomain.com\ [username]
</p>
<p>

<b>4 ) Create an Admin and Normal User Account in Active Directory</b>

  - Go into Active Directory Users and Computers
    - Right click "mydomain.com" -> New -> Organzational Unit
      - Create folders named "_ADMINS" and "_EMPLOYEES" 
<img src="https://i.imgur.com/Qrv3G0M.png" height="30%" width="30%" alt="Disk Sanitization Steps"/>

  - Right click _ADMINS and create a new User
    - For this tutorial, when creating a password, choose the option "Password never expires"
  - Next, give created user a Domain Admin. Right click user -> Properties -> Member of -> Add -> type in Domain -> Check names for Domain Admin
<img src="https://i.imgur.com/YjJjdw4.png" height="30%" width="30%" alt="Disk Sanitization Steps"/>

  - Click Apply, then OK
  - Log out/close the Remote Desktop connection to DC-1 and log back in as “mydomain.com\ [created Admin user]” 
</p>
<p>

<b>5 ) Join Client-1 to your domain (mydomain.com)</b>

  - From the Azure Portal, set Client-1’s DNS settings to the Domain Controllers’s Private IP address
    - Virtual Machine -> Client-1 -> Networking -> DNS Servers
      - Unclick "Inherit from virtual network" and click "Custom"
      - Input DC's Private IP address and click Save
<img src="https://i.imgur.com/X6XkJNb.png" height="40%" width="40%" alt="Disk Sanitization Steps"/>

  - Once updated, restart Client-1 and log back in
    - To check if it updated, go into Command Prompt and type "ipconfig /all" (Private IP = 10.0.08)
<img src="https://i.imgur.com/5zEkVXh.png" height="40%" width="40%" alt="Disk Sanitization Steps"/>

  - Right click Windows at the bottom left and choose System
    - Click "Rename this PC (advanced)" -> Change... -> Under Member of, choose Domain and input "mydomain.com"
<img src="https://i.imgur.com/B3sMlLg.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>

  - Input your created Admin user (ie. mydomain.com\ [created Admin user]) and password
  - Once Client-1 is now a member of the domain, Client-1 will Restart
    - To ensure Client-1 is a member, check back to your Domain Controller to see if it was added under Computers in Active Directory
<img src="https://i.imgur.com/lgMocuy.png" height="40%" width="40%" alt="Disk Sanitization Steps"/>

</p>
<p>
<b>6 ) Setup Remote Desktop for non-administrative users on Client-1</b>

  - Log into Client-1 as mydomain.com\ [created Admin user] and open system properties
    - Click "Remote Desktop" and allow access to "Domain users"
<img src="https://i.imgur.com/iv1whPU.png" height="40%" width="40%" alt="Disk Sanitization Steps"/>

  - You can now log into Client-1 as a normal, non-administrative user now
</p>
<p>
<b>7 ) Create a bunch of additional users and attempt to log into Client-1 with one of the users</b>

  Doing this will simulate a group of users from either a company or school

  - Login to DC-1 as [created Admin user]
  - Open PowerShell_ise as an "administrator"
<img src="https://i.imgur.com/2GLUdrQ.png" height="40%" width="40%" alt="Disk Sanitization Steps"/>

  - Create a new File and paste the contents of the <a href=https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1>script</a> into it
  - Run the script and observe the accounts being created
<img src="https://i.imgur.com/bmsUwlz.png" height="40%" width="40%" alt="Disk Sanitization Steps"/>

  - When finished, open "Active Directory Users and Computers" and observe the accounts in the _EMPLOYEES folder
<img src="https://i.imgur.com/OCGvmzG.png" height="40%" width="40%" alt="Disk Sanitization Steps"/>

  - Attempt to log into Client-1 with one of the accounts

<img src="https://i.imgur.com/X4h9ycR.png" height="40%" width="40%" alt="Disk Sanitization Steps"/><img src="https://i.imgur.com/jilN0vP.png" height="20%" width="40%" alt="Disk Sanitization Steps"/>

  - Password should be the same as mydomain.com
  - Just to clarify this was connected successfully, go into command prompt
<img src="https://i.imgur.com/wcxHhhj.png" height="40%" width="40%" alt="Disk Sanitization Steps"/>

<b>TL;DR</b>
  - Change private IP address of your Domain Controller to "Static"
  - Change Client-1 DNS Settings on Azure to DC-1's private IP address

</p>










