# Environment: VMware Workstation Pro 17

# Operating Systems: Windows Server 2019, Windows 10 Pro, Ubuntu 24.04 LTS

# Phase 1
- This first phase involves creating a domain controller to be in service of a company's central authentication server.

## Steps
1. Using VMware Workstation Player and created a Windows Server VM and named it `DC01` with a Windows Server ISO file.
2.  Went to the `control panel - Network and Sharing center and clicked Change adapter settings`; `right-clicked Ethernet and selected Properties and picked IPv4` to set a static IP address: `192.168.1.10`, subnet mask: `255.255.255.0`, default gateway: `192.168.1.1`, and DNS server: `192.168.1.10`.
3. Right-clicked This PC and properties, and clicked Rename the PC to `DC01`, and restarted the computer for the changes to take effect.
4. Opened `Server Manager and clicked roles and Features and selected role-based installation to check Active Directory Domain Services` and install it.
5. A notification flag came up, and selected `Promote this server to a domain controller, chose Add a new forest`, and entered the `company.local` domain and set the password.
6. The administrator account was created and was successfully able to log in with the password that I set up

## Troubleshooting: The "License Terms" & ISO Pivot
The Issue: During Windows Server 2022 installation, encountered the error: "Windows cannot find the Microsoft License Terms." * 

The Fix: This is often a handshake issue between VMware's "Easy Install" and the ISO. I bypassed this by selecting "I will install the operating system later" during VM creation, manually attaching the ISO to the virtual CD drive, and then booting.

Storage Lesson: Initially stored ISOs in OneDrive, causing sync delays and disk bloat. Moved all VM assets to a dedicated local SSD path to improve IOPS and stability.

# Phase 2
- This second phase involves creating users and groups to recreate the feeling of a company.

## Steps
1. Opened Active Directory Users and Computers, where you can see the company domain that was initially created.
2. Right-clicked on the company domain and clicked `New - Organizational Unit` to create organizational units for different departments: Sales, HR, and IT.
3. Added users based on their job title and responsibilities, and checked off for the user to change their password the next time they log in.
4. Right-clicked SalesOU and picked `new - Group Created Security groups` and added John Sales to the group by double-clicking SalesOU and went on the Members tab and clicked Add and typed in `john.sales`, and repeated the same steps for `Mary Hr`

# Phase 3
- The third phase involves creating two client machines and connecting them to a central authentication system via the domain controller previously created.

## Steps
1. The two client machines were created using the Windows 10 iso file, compared to the Windows 2019 server for the DC to replicate the client machines as employee computers.
- All machines are connected using NAT
- The client machines have 2 GB RAM minimum compared to the Domain controller's minimum of 4 GB RAM.
2. `Right-clicked on This PC and Properties - renamed this PC` machine to SALES-PC and HR-PC, respectively, and then restarted both.
3. First, focused on SALES-PC and accessed the Ethernet properties for IPv4 by opening `ncpa.pl` and selecting `Ethernet - Properties - IPv4`, and initially only set the same Preferred IP address to the DC's IP address.
4. Went to `Settings and System - About to access Rename this PC(Advanced) and click Change to select Domain`, and entered the company domain, and was directed to the administrator login
5. Entered the administrator credentials
6. Restarted the computer and logged in with the Sales user that was created in the last phase, and successfully logged in.
7. The final step was going to the command center and running the `whoami` command, and it showed the results that were needed. Then ran `echo %logonserver%` and showed `\\DC01`
8. Repeated the same process for HR-PC with the knowledge from the mistakes that were made previously, and now both clients are connected to the domain controller

## Troubleshooting: Connectivity & Credential Errors
Issue: Client could not find the domain.

- Cause: The client was using DHCP-assigned DNS from the NAT router instead of the DC.

- Fix: Manually set a static IP and pointed DNS strictly to the DC. Confirmed connectivity via ping `192.168.1.10`.

Issue: Domain Admin login failed repeatedly.

- Fix: Verified the exact NetBIOS name via `whoami` on the DC. Discovered a typo in the manual entry; consistency in case-sensitivity (though Windows is generally case-insensitive for logins) helped in maintaining clear documentation.

# Phase 4
- The fourth phase involves creating a company folder that contains the Sales and HR folders and creating departmental access using shared folders and security groups.

## Steps
1. Turned on the DC-PC and went to file explorer and created the CompanyShare folder on the local disk(`C:\`) and inside added the Sales and HR folders.
2. Right-clicked the CompanyShare folder and went to the `Properties - Sharing tab, and clicked Advanced Sharing` and initially allowed permissions for everyone.
3. Right-Clicked the sales folder and went to the `properties - security tab,` added the SalesUsers group, edited their permissions to modify, and proceeded to do the same steps for HRUsers and the HR folder
4. Turned on the SALES-PC and pressed `Windows + r and typed in \\DC01\CompanyShare` and clicked on it, and the CompanyShare folder was there along with the Sales and HR folders.
5. Logged in to SALES-PC, clicked on the sales folder, and gained access
6. Repeated the same steps for the HR-PC.

## Issues
- Logged in to SALES-PC, clicked on the sales folder and gained access, but was able to access the HR folder, which was not supposed to happen.
- The adjustment that was made is going back to the DC-PC and adjusting the security groups, where in the HR folder, the SalesUsers group was added, and their permissions were set to denied.
- Went back to the SALES-PC to access the HR folder and got the access denied message, and while double-checking, had access to the sales folder, which was successful.

# Phase 5
- The fifth phase involves creating centralized IT management using Group Policy Objects

## Steps
1. Went to `DC01` and to Group Policy Management to create a GPO by clicking on `Forest - Domains - company.local and right-clicking the domain, and clicked Create a GPO in this domain, and Link it here and called it Company Policy`
2. Proceeded to right-click and press `edit the GPO and Computer Configuration - Policies - Windows Settings- Security Settings- Account Policies - Password Policy` and enabled Password complexity requirements and length minimum of 8 characters
3. Next to `User Configuration - Preferences - Windows Settings - Drive Maps and right-click - new - mapped drive to create a new mapped drive with the settings Location: \\DC01\CompanyShare Drive Letter: Z: Action: Create`
4. To disable Control Panel, went to `User Configuration - Policies - Administrative Templates` and enabled Prohibit access to Control Panel
5. Went to `C:\Windows\Web\Wallpaper` and picked a jpg image and added it to `C:\CompanyShare`, and then in the GPO, click `User Configuration - Policies - Administrative Templates - Desktop - Desktop` and set a network path to the CompanyShare folder and chose Fill
7. Right-clicked Sales OU and HR OU and clicked link to an existing GPO to link them both to the company policy GPO
8. On both client PCs, went to the command center and ran `gpupdate /force` to apply the changes and then `shutdown /r /t 0 `to restart the computers.
9. Verify by seeing the `Z:` drive on file explorer, Wallpaper should be there, Control panel blocked, Password policy enforced

## Troubleshooting: The Wallpaper Refresh
Issue: All GPOs applied except the wallpaper.

- Fix: Verified the image was in a shared network location accessible by the Computer object, not just the user. Updated the path to a UNC format `(\DC01\CompanyShare\img4.jpg)` and ran `gpupdate /force`.

# Phase 6
- The sixth phase involves creating a IT onboarding workflow and adding a new user to the company.

## Steps
1. Went on DC-01 and opened `Active Directory Users and Computers and right-clicked on the Sales OU` to add the new member, Juan Pope; unchecked user must change password at next logon for later purposes.
2. `Double-clicked juan.pope and went on Member Of` and added `juan.pope` to the SalesUsers group.
3. Logged on to the SALES-PC using the `juan.pope` account and verified that the `Z:` drive was on File Explorer. Pressed `Windows + R` and confirmed that the CompanyShare folder was there.
4. Verified that the juan.pope account can access the Sales folder but cannot access the HR folder.
5. Used the command prompt and ran the command `net use` to verify that `Z`: is mapped

# Phase 7
- The seventh phase involves creating a IT offboarding workflow and removing the previously created new user from the company

## Steps
1. Went on DC-01 and opened `Active Directory Users and Computers and right-clicked on juan.pope and clicked Disable Account`.
2. Double-clicked on `juan.pope` and went to `Member Of and selected SalesUsers and clicked Remove`.
3. Tried logging in SALES-PC with `juan.pope` and account and got the message Your account has been disabled.
4. Created an archive folder inside the `CompanyShare` folder.
5. Moved `juan.pope` folder to the archive folder and adjusted the folder properties and security set, both HR and Sales users to remove access to archived files.
6. Folder is preserved in the archive folder, but no user's side can access it.

## Issues
- I didn't implement any user folders yet so I created new user folders for each user in each of their respective department folders
- Right-clicked on `juan.pope` folder and choose the Properties security tab and set full control to both `juan.pope` and the administrators group.
- Clicked on the advanced tab and picked Change next to where the owner was written, typed in Administrator, and then checked Replace owner on subcontainers and objects.
- Back to Advanced Security settings and clicked Disable Inheritance and chose Covert Inherited permissions into explicit permissions.
- Clicked Add and select a principal and picked locations to `company.local` and typed in `juan.pope` and checked names and set to full control; same method for administrators
  
# Phase 8
- The eighth phase involves creating a new VM for Linux and a remote desktop connection to create a IT scenario.

## Steps
1. Went on `SALES-PC` and `pressed Windows + R and typed sysdm.cpl`; went on remote tab and selected allow remote connections to this computer.
2. Opened `Windows Defender Firewall and clicked Allow an app through firewall, and made sure that Remote Desktop was selected`.
3. Went on `DC01` and pressed `Windows + R and typed mstsc, and entered SALES-PC IP, clicked connect`, and entered John Sales' credentials, and it connected
4. Repeated the same steps for `HR-PC`.
5. Downloaded Ubuntu Desktop 24.04.4 LTS.
6. Created a new VM with the Ubuntu iso file, and it was successfully created
7. Installed OpenSSH via `sudo apt update` and `sudo apt install openssh-server -y`
8. Started SSH via `sudo systemctl start ssh` and enabled at boot via `sudo systemctl enable ssh`
9. Ran `ssh james-linux@192.168.1.40 `and gained Linux server access.
10. Restarted the SSH service via `sudo systemctl status ssh` and then put in `sudo systemctl status ssh` and got active(running)

## Troubleshooting: Netplan Configuration
Issue: Ubuntu VM received an IP on the wrong subnet `(192.168.122.x)`.

- Fix: Edited `/etc/netplan/00-installer-config.yaml` to define the static IP and gateway manually.

- Verification: After sudo netplan apply, DC01 successfully pinged and established an SSH session to the Linux host.
