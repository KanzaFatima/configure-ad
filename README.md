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

- Resource group and virtual machine
- Create domain controller and client 1 and ensure connectivity between them
- Install Active Directory Domain Services on DC-1 
- Create bunch of new users using Power Shell ISE as an administrator

<h2>Deployment and Configuration Steps</h2>

<p>
Create the DC-1 (Domain Controller) VM Windows Server 2022 and the Client VM (Client-1) Windows 10. Make sure use the same Resource Group and Vnet that was created while creating the DC-1.
</p>
<br />
<p>
<img src="https://i.imgur.com/YKi0ZWr.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Set Domain Controller’s NIC Private IP address to be static


</p>
<br />

<p>
<img src="https://i.imgur.com/NIhcOd2.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>

Go to DC-1>networking >network interface dc-1365>Ip configurations and change the Ip address from dynamic to static and save it.
  
Make sure both DC-1 and Client-1 are in same virtual network.
</p>
<br />

<p>
<img src="https://i.imgur.com/QposvOi.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Ensure the connectivity between the client and Domain Controller and to do this login to Client-1 with Remote Desktop and ping the DC-1 private IP address with ping -t  (perpetual ping), and it will time out because domain controller windows firewall is blocking ICMP traffic.

</p>
<br />

<p>
<img src="https://i.imgur.com/65Yc0pN.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Login to the DC-1 with remote desktop and enable ICMPv4 on the local windows Firewall.
  
  
To do that click start Wf.msc>inbound rules>sort by protocol and find icmpv4>enable both.
Minimize DC-1 and check client-1

</p>
<br />

<p>
<img src="https://i.imgur.com/axndXG6.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
We can see the ping is succeed
  
  
Press ctrl c to stop the ping
</p>
<br />

<p>
<img src="https://i.imgur.com/Z0fbZu5.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Install Active Directory Domain Services.

Login to DC-1 and open server manager>add roles and features, proceed these steps and then mark active directory domain services>add features and install.

</p>
<br />
<p>
<img src="https://i.imgur.com/VBHNabC.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

<img src="https://i.imgur.com/ch0Sfkq.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
  

<img src="https://i.imgur.com/9a3tLi5.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
  
</p>
<p>
Click to the yellow exclamation mark on top right side>promote this server to a domain controller>add a new forest as mydomain.com ( it can be anything, just remember what it is)>mydomain.com>set password> and install 

</p>
<br />

<p>
<img src="https://i.imgur.com/BkCxDE9.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Restart and then log back into DC-1 as user: mydomain.com\kanzalab (fully qualified domain name)
</p>
<br />

<p>
<img src="https://i.imgur.com/1WZk2vk.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Create couple of organizational units inside Active Directory Users and Computer (ADUC) 
Click start and type active directory users and computers>right click mydomain.com>new>organizational unit.

</p>
<br />

<p>
<img src="https://i.imgur.com/6yBwtlZ.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Create two Organizational units, first is _EMPLOYEES and the second is _ADMINS and then refresh mydomain.com.
</p>
<br />

<p>
<img src="https://i.imgur.com/K3gTgmi.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Create another administrative account that tie to us and then logout from kanza lab and create our own admin account.
Admins>right click and add new users as kanza naqvi 

</p>
<br />

<p>
<img src="https://i.imgur.com/kRYy1Dw.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Create a new employee named kanza naqvi with the username kanza_admin.
</p>
<br />

<p>
<img src="https://i.imgur.com/ZNBInlU.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Assign kanza naqvi to Domain Admin Group. Domain Admin group is a build in security group and anyone who is the member of this group can make changes to the domain and perform all kind of activity.

Right click to kanza naqvi>properties>member of>add>type domain and click check names and join domain admin group.
Add kanza_admin to the “Domain Admins” Security Group

</p>
<br />

<p>
<img src="https://i.imgur.com/gQUCjfu.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>

Log out/close the Remote Desktop connection to DC-1 and log back in as “mydomain.com\jane_admin”
User kanza_admin as your admin account.
  
  
Join Client-1 to (mydomain.com) and when we do that even though our account has not existed on this client 1 before  we will still able to join client 1 as kanza_admin or any other domain account
</p>
<br />

<p>
<img src="https://i.imgur.com/Klarkwf.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
From the Azure Portal, set Client-1 DNS settings to the DC-1 Private IP address because we want client-1 to use DNS server of DC-1.
Because domain controller knows what mydomain.com is, if we uses client-1 dns server and say I want to join mydomain.com, this dns server is going to look out on the internet and it will fail but if we tell client-1 to use our domain controller dns server as its dns server, when client-1 will try to join the mydomain.com our domain controller/dns server is use to join it's ip private address and client 1 is able to join it.
  
  
Go to DC-1 inside the azure portal and copy private ip address and then go to client 1>networking>click network interface clint 1138>dns servers>custom and copy dc1 private ip address>save
  
  
From the Azure Portal, restart Client-1 within the portal
Login to Client-1 (Remote Desktop) as the original local admin (kanzalab) and join it to the domain (computer will restart)

</p>
<br />

<p>
<img src="https://i.imgur.com/nKYbb8s.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Open the command line in Client-1 and type ipconfig/ all and we can see the DNS Server is using DC-1 private IP Address as its dns server.
</p>
<br />

<p>
<img src="https://i.imgur.com/QBxeBM6.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Join client 1 to the domain. Click start system>rename this pc advanced>change>domain> mydomain.com. 
</p>
<br />

<p>
<img src="https://i.imgur.com/nh8fm2o.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Now use mydomain.com\kanza_admin and password.
Restart the computer.
</p>
<br />

<p>
<img src="https://i.imgur.com/ZHugLK5.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>

  
<img src="https://i.imgur.com/ZslhlgR.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Login to Client 1 go to system>remote desktop>select users that can remotely access this PC>add>domain users>check names>ok.
All domain users are allowed to login to this computer. 

</p>
<br />

<p>
<img src="https://i.imgur.com/uftNQG6.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
These are the different users who loged in to this computer.
</p>
<br />

<p>
<img src="https://i.imgur.com/hNLkctu.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Create bunch of random domain users inside our domain and then we gonna use one these random user to login in to  client 1.First this is the raw code that we will paste in to Power Shell ISE
</p>
<br />

<p>
<img src="https://i.imgur.com/x8IzAHJ.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
  
  
<img src="https://i.imgur.com/q0K4zGS.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Open the Power Shell ISE as an Administrator and in the new folder paste the raw code and click run and we can see the list of users is creating who are able to remote desktop into client-1.
</p>
<br />

<p>
<img src="https://i.imgur.com/1Eu2dCk.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Now if we go to ADUC and refresh employees we can see accounts are creating.
</p>
<br />

<p>
<img src="https://i.imgur.com/L0TD3nS.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Select any rendom user and logout from client-1 and login back as that user.
</p>
<br />

<p>
<img src="https://i.imgur.com/oL282U2.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
These are different users who logged into to this computer.
</p>
<br />

<p>
<img src="https://i.imgur.com/L5A8JCO.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
  
<img src="https://i.imgur.com/gwsVhKG.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
  
<p>
We can unlocked the accounts, reset passwords, enable and disable account.
</p>
<br />
