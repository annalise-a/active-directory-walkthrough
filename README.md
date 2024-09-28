# Introduction

In this homelab, we'll be installing a server VM and a client VM, setting up Active Directory, creating Organizational Units (OUs), groups, and users, and implementing a Group Policy Object (GPO). Learning Active Directory is really important for anyone in IT because it lays the groundwork for understanding Windows-based networks and provides skills for managing users and computers effectively. By learning Active Directory, we can implement security measures, simplify user onboarding, and manage roles within an organization, while ensuring that configurations remain consistent through GPOs.

## Sections:
1. Installing the Prerequisites
2. Installing the Client VM (Windows 10 Enterprise)
3. Installing the Server VM (Windows Server 2022)
4. Setting Up the Server
5. Assigning a Static IP Address to the Server
6. Creating Organizational Units (OUs), Groups, and Users
7. Joining a Client to the Domain
8. Creating and Implementing a Group Policy Object (GPO)

## Section 1: Installing the Prerequisites
- VMware Workstation Pro 17 will be used as the hypervisor for this lab, but any other hypervisor can be used.  
	- The software is now available for free through Broadcom. You must create an account through Broadcom to download it.  
- The following ISOs are required:  
- Windows 10 Enterprise ISO:  
	- Download here: [https://www.microsoft.com/en-us/software-download/windows10ISO](https://www.microsoft.com/en-us/software-download/windows10ISO)  
- Windows Server 2022 ISO:  
	- Download here: [https://www.microsoft.com/en-US/evalcenter/evaluate-windows-server-2022](https://www.microsoft.com/en-US/evalcenter/evaluate-windows-server-2022)

## Section 2: Installing the Client VM (Windows 10 Enterprise)
**2.1** Open your selected hypervisor and create a New Virtual Machine.  
![2.1 image](/images/2.1.png)

**2.2** In the New Virtual Machine Wizard, leave the configuration on Typical and click Next.  
![2.2 image](/images/2.2.png)

**2.3** Select I will install the operating system later. (Do not add an ISO at this step.)  
![2.3 image](/images/2.3.png)

**2.4** Choose Windows 10 x64 as the operating system version.  
![2.4 image](/images/2.4.png)

**2.5** Name your virtual machine and choose a storage location if needed.  
![2.5 image](/images/2.5.png)

**2.6** Set the Maximum Disk Capacity and leave the default Split virtual disk into multiple files.  
- I set mine at 30 GB, you can set yours higher or lower as needed.  
- Do not set it too low though because the OS will not boot then.  

**2.7** After the VM is created, right-click the VM and select Virtual Machine Settings > Hardware > CD/DVD (SATA). Choose Use ISO image file and select the Windows 10 Enterprise ISO.  
![2.7 image](/images/2.7.png)

**2.8** Power on the VM. Press any key immediately to boot from the ISO file.  
If you do not press any key fast enough, it will fail to boot.  
To solve this, shut down the VM and power it back on and try again.  
You should be greeted with this screen:
![2.8 image](/images/2.8.png)

**2.9** Set the language, keyboard layout, and time zone as needed. Select Custom: Install Windows Only (Advanced) for the installation type. 
![2.9 image](/images/2.9.png)

**2.10** Select the disk partition and click Next to start the installation.  
![2.10 image](/images/2.10.png)

**2.11** After installation, select Domain Join instead and create a local user account.  
Domain Join is at the bottom-left corner of this screen.

**2.12** Complete the remaining setup options. You can turn off these non-essential settings for this lab.  

Congratulations! You have successfully installed the Client VM.

## Section 3: Installing the Server VM (Windows Server 2022)
- Installing the Server VM will follow the same steps as installing the Client VM.  
- This section will highlight the differences in the installation process.  
- If needed, refer back to Section 2.

**3.1** Create a new virtual machine as outlined in Section 2, but select Windows Server 2022 as the guest operating system.  

**3.2** Set the Maximum Disk Capacity.  
- Be sure to not set this too low especially for a server as it will not run/boot properly.  
- I set mine at 60 GB for future use.
![3.2 image](/images/3.2.png)

**3.3** Choose the Windows Server 2022 ISO in the VM’s CD/DVD settings before powering on the server.  
![3.3 image](/images/3.3.png)

**3.4** Power on the VM and choose Windows Server 2022 Standard Evaluation (Desktop Experience) to install the version with a GUI.  
- The other version does NOT include a GUI and is only command-line based.
- ![3.4 image](/images/3.4.png)

**3.5** Set up the Administrator password as prompted during the installation.  
![3.5 image](/images/3.5.png)

Congratulations! You have successfully installed the Server VM.

## Section 4: Setting Up the Server
**4.1** After logging into the server, open Server Manager and select Add Roles and Features.  
![4.1 image](/images/4.1.png)

**4.2** Leave the defaults selected until you reach the Server Roles page.  
![4.2 image](/images/4.2.png)

**4.3** Checkbox Active Directory Domain Services.  
You can select DHCP and DNS Server for later use.  
![4.3 image](/images/4.3.png)

**4.4** Continue through the feature options, then click Install at the confirmation window.  
![4.4 image](/images/4.4.png)

**4.5** After installation, select the flag icon in the Server Manager Dashboard and click Promote this server to a domain controller.  
![4.5 image](/images/4.5.png)

**4.6** Choose Add a new forest and enter a Root domain name (e.g., practice.local). 
![4.6 image](/images/4.6.png)

**4.7** Enter a DSRM password on the Domain Controller Options page and leave other options as default.  
![4.7 image](/images/4.7.png)

**4.8** Verify that the NetBIOS domain name matches your chosen domain name in all caps (e.g., PRACTICE).  
![4.8 image](/images/4.8.png)

**4.9** Go through the next few pages, leave the default options. Complete the setup by clicking Install on the Prerequisite Check page.  
![4.9 image](/images/4.9.png)

Congratulations! You have successfully set up the Server and Domain Controller.

## Section 5: Assigning a Static IP Address to the Server
- Assigning a static IP address to a server is best practice and important for consistent access and easy troubleshooting.

**5.1** Open the Command Prompt in the Server VM and type `ipconfig` to note the IPv4 Address, Subnet Mask, and Default Gateway.  
![5.1 image](/images/5.1.png)

**5.2** Open Ethernet Settings by typing Ethernet in the search bar, then go to Change adapter options.

**5.3** Right-click the Ethernet adapter and select Properties. 
![5.2 image](/images/5.2.png)

**5.4** Choose IPv4 Properties, select Use the following IP address, and enter your IP Address, Subnet Mask, and Default Gateway.  
![5.3 image](/images/5.3.png)

Congratulations! You have successfully assigned a static IP address to your server.

## Section 6: Creating Organizational Units (OUs), Groups, and Users
- **Organizational Units (OUs)** are containers that can hold users, groups, computers, and other OUs. They serve as a way to structure and organize directory objects.  
- **Groups** are collections of user accounts, computer accounts, or other groups that simplify the management of permissions and access to various resources of the organization.  
- **Users** are individual accounts that represent people or services. Each user account has unique credentials (username and password).  
- For this lab, I will be going off a naming system based on a common business-environment.  
	- You can name your OUs, Groups, and Users anything you wish. It is best to choose a naming pattern that makes sense to you logically.  
		- For example: OU = DC, Group = Justice League, User = Superman.

**6.1** Open Server Manager and select Tools > Active Directory Users and Computers.  
![6.1 image](/images/6.1.png)

**6.2** Right-click your domain (e.g., practice.local) and select New > Organizational Unit.  
![6.2 image](/images/6.2.png)

**6.3** Create OUs for locations like New York, Los Angeles, and Chicago. 
![6.3 image](/images/6.3.png)

**6.4** Create sub-OUs named Computers, Servers, and Users within each location OU.
![6.4 image](/images/6.4.png)
![6.4 second image](/images/6.4_2.png)

**6.5** Create a new group within the Users sub-OU (e.g., IT).  
- You will leave the Group Scope and Group Type as the defaults.  
- Just an overview of what these are:  
- Scopes-  
	- Domain Local Scope is restricted to the domain in which they are created.  
	- Global Scope is limited to the domain they are created but can be used across the entire forest.  
	- Universal Scope can be used across all domains within the forest.  
- Group Types-  
	- Security Groups are used to assign permissions to shared resources, allowing members to access specific files or applications.  
	- Distribution Groups are usually used for email distribution lists. They do not have security permissions.
 ![6.5 image](/images/6.5.png)
![6.5 image](/images/6.5_2.png)

**6.6** Create new users within the Users sub-OU (e.g., Mary Johnson).  
![6.6 image](/images/6.6.png)
![6.6 image](/images/6.6_2.png)

Congratulations! You have successfully created OUs, Groups, and Users.

## Section 7: Joining a Client to the Domain

**7.1** Log into the client VM. Go to Settings > About > Rename this PC (Advanced). 
![7.1 image](/images/7.1.png)

**7.2** In the System Properties window, click Change and select Domain. Enter your domain name (e.g., practice).  
- You may need to type in your domain name with ‘.local’ at the end if it fails to connect.
![7.2 image](/images/7.2.png)
![7.2 image](/images/7.2_2.png)

**7.3** Enter the Administrator credentials when prompted.  
**7.4** Restart the client VM to apply the changes.  
**7.5** Log in as a user created in Section 6 (“Mary Johnson”) to verify connectivity to the domain.  
- You should be able to login successfully as the new user you’ve created.  

- The following steps are optional but recommended for best practices to ensure that the computer objects are placed in the appropriate OU.  
**7.6** Switch to the Server VM and Open Server Manager. Select Tools > Active Directory Users and Computers.  
**7.7** Select .local > Computers. Right click the computer that was renamed and select Move.  
![7.7 image](/images/7.7.png)

**7.8** Select the sub-OU Computers of the New York OU.  
![7.8 image](/images/7.8.png)

Congratulations! The client VM is now joined to the domain.

## Section 8: Creating and Implementing a Group Policy Object (GPO)
- A **group policy object** can be thought of as a set of rules or setting that can be applied to groups of users/computers in active directory.  
- The group policy object that I will be creating will disable the Command Prompt for users in the New York OU.

**8.1** Open Server Manager on the Server VM and select Tools > Group Policy Management. 

**8.2** Right-click your domain (e.g., practice.local) and select Create a GPO in this domain and link it here.  
![8.2 image](/images/8.2.png)

**8.3** Name the GPO (e.g., Disable Command Prompt).  

**8.4** Right-click the GPO and select Edit. Navigate to User Configuration > Administrative Templates > System > Prevent access to the command prompt.  
![8.4 image](/images/8.4.png)

**8.5** Enable the policy to disable the Command Prompt for users.  

**8.6** Link the GPO to the Users OU in New York.  
- This is done by dragging the ‘Disable Command Prompt’ from the ‘Group Policy Objects’ to the ‘Users’ sub-OU.  
- This GPO is placed in the Users sub-OU because it specifically impacts individual users.
![8.6 image](/images/8.6.png)

**8.7** On the server, run `gpupdate` in Command Prompt to apply the GPO changes.  
- This can also be done by restarting the Server.

**8.8** Test the policy on the client VM by trying to open the Command Prompt.  
- You should see the message as shown below.
![8.8 image](/images/8.8.png)

Congratulations! You have successfully created and applied a GPO.

# Conclusion

In this lab, we successfully installed a server VM and a client VM, set up a server for Active Directory, created OUs, made a group and a user, and implemented a GPO. Now that we have the basics down, you can take this homelab even further by trying out more features of Active Directory. For example, you could create additional OUs to reflect a real-world organization, set up more GPO settings to control how users access their environments, and adjust user permissions to see how access controls work. You might also explore features like Active Directory Federation Services (ADFS) or role-based access control (RBAC) to learn how user management and security are handled in larger networks.
