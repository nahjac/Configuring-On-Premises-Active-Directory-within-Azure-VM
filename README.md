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
*pic of parameters of created DC vm*
Navigate to DC-1's netowrking settings then open ipconfig1 to change the and set DC-1's Virtual Network Interface Card (vNIC) private IP address to static. A static IP address ensures that it will not change. Next we will create the Client VM. It will be named Client-1 and be a Windows 10 Pro resource. Make sure that you use the same resource group and VNET as DC-1.
*pic of parameters of created client vm*
Now we will ensure connectivity between the Client and the DC. Login to Client-1 using Microsoft Remote Desktop.
