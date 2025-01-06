![image](https://github.com/user-attachments/assets/c9ba7c47-49be-4e7b-acc7-06d074632ee5)
# Configuring-On-Premises-Active-Directory-within-Azure-VM
This tutorial outlines the implementation of an Ubuntu web server within an Azure virtual machine.
## Environments and Technologies Used
+ Microsoft Azure (Virtual Machine)
+ Remote Desktop
+ Active Directory Domain Services
+ PowerShell
## Operating Systems Used
+ Windows Server 2022
+ Windows 10 (21H2)
## Deployment and Configuration Steps
In this lab, we'll start by setting up 2 virtual machines that will be in the same VNET. The first VM will be a Domain Controller(DC). A DC is a server that manages network and identity security within a domain. The second VM will be the Client. For the DC virtual machine, which will be named DC-1, will be created using Windows Server 2022.

<img width="947" alt="SS1" src="https://github.com/user-attachments/assets/6adde02d-8665-42d9-be80-b6faac5d3c2c" />

Navigate to DC-1's networking settings then open ipconfig1 to change the and set DC-1's Virtual Network Interface Card (vNIC) private IP address to static. A static IP address ensures that it will not change. Next we will create the Client VM. It will be named Client-1 and for image select Windows 10 Pro. Make sure that you use the same resource group and VNET as DC-1.

<img width="791" alt="SS2" src="https://github.com/user-attachments/assets/df6b4302-4404-4a1a-bcf5-0670010db4dc" />

Now we will ensure connectivity between the Client and the DC. Login to Client-1 using Microsoft Remote Desktop. Open up the command prompt and ping DC-1's private IP address. In this case my IP is 10.0.0.4. This can be done by typing `ping -t 10.0.0.4`. You'll see that the ping request continuously times out and this is due to the current firewall settings. 

<img width="798" alt="SS3" src="https://github.com/user-attachments/assets/451778a6-916e-49c9-a900-3eaf6e7baa4b" />

To fix this, enable ICMPv4 on DC-1's local Windows firewall. Login to DC-1 using Microsoft Remote Desktop and navigate to Start > Windows Admin Tools > Windows Defender Firewall with Advanced Security > Inbound Rules. Find "Core Networking Diagnostics ICMPv4-In" and enable it.

<img width="655" alt="SS4" src="https://github.com/user-attachments/assets/f72631aa-776f-476d-8cf5-2d8635f88eb2" />

Once you log back in to Client-1 and open the command line prompt, DC-1 will automatically start to be pinged. Now we will need to install Active Directory. Log back in to DC-1 and open Server Manager. Select "Add Roles and Features" make sure "Active Directory Domain Services" is selected when you get to the Server Roles portion of the configuration. Once installation is complete, promote the server to a domain controller which can be found in the Server Manager Dashboard. Setup a new Forest with a domain name of "mydomain.com". Create a password and allow installation to complete. DC-1 will automatically restart. Login as user "mydomain.com\labuser". If you completed these steps correctly, you should be able to run AD Users & Computers.

<img width="811" alt="SS5" src="https://github.com/user-attachments/assets/793780e3-7fe4-460a-98a5-da7a693391d0" />

Now, let's create an admin account and a normal user account in AD. While in AD Users & Computers, right click mydomain.com and create 2 new Organization Units (OUs). OUs are containers in an AD domain that hold users, groups, and computers. Name the first one "_EMPLOYEES" and the second one "_ADMINS". Right click your _ADMINS OU then click New then User. First and Last Name should be Jane Doe (or whatever you like). User login name will be jane_admin. Create a password and click Finish. Now add jane_admin to the domain admins security group. This can be done by right clicking Jane Doe > Properties > Member of > Add. 

<img width="737" alt="SS6" src="https://github.com/user-attachments/assets/5ff286ff-de62-4ecc-a930-1284171e98cd" />

Great! Now you have the ability to login and use jane_admin as the administrator account. Go back to the Azure portal and navigate to the Client-1 VM's networking settings. Open the link next to the NIC > select DNS server > Custom. For the DNS server field, enter DC-1's private IP address. After that updates, restart Client-1 from within the Azure portal.

<img width="755" alt="SS7" src="https://github.com/user-attachments/assets/d3e265fd-d8c7-4701-abf1-8ff73cd77a7b" />

Log back into Client-1 using Microsoft Remote Desktop using the original local admin (labuser). We are going to join Client-1 to the domain. Right click the Start menu and select System. On the right-hand side select Rename This PC (advanced) > Change. Under the "Member Of" field, select Domain and enter "mydomain.com". Use the username mydomain.com\jane_admin and the password you created. Restart the computer.

<img width="691" alt="SS8" src="https://github.com/user-attachments/assets/094ca6e3-8cbd-498b-9464-c395b38b12ea" />
<img width="644" alt="SS9" src="https://github.com/user-attachments/assets/3de228bb-ccf3-4eb5-9696-7294207943a1" />

Now let's setup Remote Desktop for non-admin users on Client-1. Login to Client-1 as an admin (mydomain.com\jane_admin). Once again right-click the Start menu and select System. Select Remote Desktop. Type in the name of your domain users and click OK. You can add as many additional users as you like.

<img width="644" alt="SS10" src="https://github.com/user-attachments/assets/ca116189-aa35-4309-affc-2364cf123b77" />

You should now be able to login to Client-1 as a normal user. To verify that normal users can RDP into Client-1, log back into DC-1 using jane_admin. Open Powershell ISE and run as administrator. In the top left corner select "New Script". Paste the contents of [this script](https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1). Run the script by clicking the green arrow at the top of the screen.

<img width="722" alt="SS11" src="https://github.com/user-attachments/assets/b73a2fa0-9d3f-4ef6-9f30-2e401bd37f0b" />

Once the users have been created, go back to AD Users and Computers > mydomain.com > _EMPLOYEES. Attempt to log in to Client-1 using one of the accounts that were created.

<img width="713" alt="SS12" src="https://github.com/user-attachments/assets/7470896e-9a3c-4cca-81e7-627a79bc7b99" />

<img width="331" alt="SS13" src="https://github.com/user-attachments/assets/f4ebd06c-1572-43e3-bafb-ef69505af757" />

As you can see we've successfully login using a normal user. Congrats! You've implemented on-premises Active Directory and created users within Azure VM.
