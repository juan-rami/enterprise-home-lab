# Phase 1
- This first phase involves creating a domain controller to be in service of a company's central authentication server.
- Initially tried to download VirtualBox, but the system was looking error-filled when opened in VSCode, abandoned that plan, and instead switched to VMWare Workstation Pro 17, which has proven to be a valuable asset.
- The next step was to download Windows Server 2022, and it was used to try to create the Workstation, but the error was "Windows cannot find the Microsoft License Terms. Make sure the installation sources are valid and restart the installation." I didn't understand and thought the server wasn't working.
- Pivoted off Windows Server to download Windows 10 iso file, accidentally put it in my OneDrive, and learned that iso files take up a ton of space, and it took a while to get it fixed.
- Took some time to adjust where the iso and virtual machine files can be put in a location where they can be accessed without affecting the computer.
- The virtual machine was created with the Windows 10 iso file, and learned that the file doesn't have Server Manager, which is needed to install Active Directory Domain Services, leading to promoting the server to a domain controller.
- I decided to create 2 client machines with the Windows 10 iso file that will be used for later phases.
- Downloaded Windows Server 2019 due to looking up research that it was more compatible with Windows 10, and faced the same error as before.
- Adjusted instead of picking the server file right away, I picked to install my own drive so that when the final adjustments showed up, I picked the server file and the machine was created.
- Went to the control panel - Network and Sharing center and clicked Change adapter settings; right-clicked Ethernet and selected Properties and picked IPv4 to set a static IP address, subnet mask, default gateway, and DNS server.
- Right-clicked This PC and properties, and clicked Rename the PC to DC01 and restarted the computer for the changes to come through.
- Opened Server Manager and clciked roles and Features and selected role-based installation to check Active Directory Domain Services and install it, then a notification flag came up and selected Promote this server to a domain controller, chose Add a new forest and entered the company.local domain and set the password.
- The administrator account was created and was successfully able to log in with the password that I set up

# Phase 2
- This second phase involves creating users and groups to recreate the feeling of a company.
- Opened Active Directory Users and Computers, where you can see the company domain that was initially created.
- Right-clicked on the company domain and clicked New - Organizational Unit to create organizational units for different departments: Sales, HR, and IT.
- Added users based on their job title and responsibilities, and checked off for the user to change their password the next time they log in.
- Right-clicked SalesOU and picked new - Group Created Security groups and added John Sales to the group by double-clicking SalesOU and went on the Members tab and clicked Add and typed in john.sales, and repeated the same steps for Mary Hr

# Phase 3
- The third phase involves creating two client machines and connecting them to a central authentication system via the domain controller previously created.
- The two client machines were created using the Windows 10 iso file, compared to the Windows 2019 server for the DC to replicate the client machines as employee computers.
- All machines are connected using NAT
- The client machines have 2 GB RAM minimum compared to the Domain controller's minimum of 4 GB RAM.
- Right-clicked on This PC and Properties - renamed this PC machine to SALES-PC and HR-PC, respectively, and then restarted both.
- First focused on SALES-PC and accessed the Ethernet properties for IPv4 by opening ncpa.pl and selecting Ethernet - Properties - IPv4, and initially only set the same Preferred IP address to the DC's IP address.
- Went to Settings and System - About to access Rename this PC(Advanced) and click Change to select Domain and entered the company domain, and got the error that it didn't exist.
- Went to the DC to check if everything was functioning and the domain was there, and there was confusion, so the next step was going to the command center from the SALES-PC to ping the DC's IP address, and it failed to contact the domain controller, giving the request timed out error.
- Back to the DC to check the IPv4 address, and they were as previously implemented, and ran ipconfig and got the same results.
- One mistake that was made initially was only putting the preferred IP address and not implementing a static IP address, subnet mask, and default gateway, so that was done by opening ncpa.pl and selecting Ethernet - Properties - IPv4.
- Went back to the command center to ping the DC's IP address and gathered a successful reply.
- The mistake was that due to DNS and IP misconfiguration, the domain controller could not be contacted. It was resolved by correcting the DNS and adding a static IP address to point to DC01.
- Going back to Settings and System to Rename this PC(Advanced), clicked Change and Domain, and typed in the company domain, and was directed to the administrator login.
- Had difficulty remembering the administrator username, so I went to the DC's login screen, and it showed the first half of the username in all caps, and typed in the username as it was shown, and it failed to log in, tried again, making sure nothing was misspelled, and it failed again.
- The next step was the DC's command center, and ran the whoami command and found that it was the username, but it was completely written in lower caps.
- Back to the credentials prompt, I typed in the username as written in the command center, and failed to log in due to mistyping the username and password. After multiple attempts and checking for any spelling errors, the computer successfully joined.
- Restarted the computer and logged in with the Sales user that was created in the last phase, and successfully logged in.
- The final step was going to the command center and running the whoami command, and it showed the results that were needed. Then ran echo %logonserver% and showed \\DC01
- Repeated the same process for HR-PC with the knowledge from the mistakes that were made previously, and now both clients are connected to the domain controller

# Phase 4
- The fourth phase involves creating a company folder that contains the Sales and HR folders and creating departmental access using shared folders and security groups.
- Turned on the DC-PC and went to file explorer and created the CompanyShare folder on the local disk(C:\) and inside added the Sales and HR folders.
- Right-clicked the CompanyShare folder and went to the Properties - Sharing tab, and clicked Advanced Sharing and initially allowed permissions for everyone.
- Right-Clicked the sales folder and went to the properties - security tab, added the SalesUsers group, edited their permissions to modify, and proceeded to do the same steps for HRUsers and the HR folder
- Turned on the SALES-PC and pressed Windows + r and typed in \\DC01\CompanyShare and clicked on it and the CompanyShare folder was there along with the Sales and HR folders.
- Logged in to SALES-PC clicked on the sales folder and gained access, but was able to access the HR folder, which was not supposed to happen.
- The adjustment that was made is going back to the DC-PC and adjusting the security groups, where in the HR folder, the SalesUsers group was added, and their permissions were set to denied.
- Went back to the SALES-PC to access the HR folder and got the access denied message, and while double-checking, had access to the sales folder, which was successful.
- Repeated the same steps for the HR-PC.

# Phase 5
- The fifth phase involves creating centralized IT management using Group Policy Objects
- Went to DC01 and to Group Policy Management to create a GPO inside the company.local called Company Policy
- Proceeded to edit the GPO and Computer Configuration - Policies - Windows Settings- Security Settings- Account Policies- Password Policy and enabled Password complexity requirements and length minimum of 8 characters
- Next to User Configuration - Preferences - Windows Settings - Drive Maps and created a new mapped drive with the settings Location: \\DC01\CompanyShare Drive Letter: Z: Action: Create
- To disable Control Panel, went to User Configuration - Policies - Administrative Templates and enabled Prohibit access to Control Panel
- Went to C:\Windows\Web\Wallpaper and picked img0.jpg and added it to C:\CompanyShare and renamed it to wallpaper.jpg
- User Configuration - Policies - Administrative Templates - Desktop - Desktop and set a network path: \\DC01\CompanyShare\wallpaper.jpg and chose Fill
- Right-clicked Sales OU and HR OU to link them both to the company policy GPO
- On both client PCs, went to the command center and ran gpupdate /force to apply the changes and then shutdown /r /t 0 to restart the computers.
- When turned back on, the Z: drive shows up; Control Panel access is restricted, password policy applies, but no wallpaper
- Double-checked to see if the wallpaper was in the company folder, and it shows on all 3 machines
- Checked if the network path is right, and it was, so I ran gpupdate / force and restarted, and there were no results
- Turned off Prohibit access to Control Panel to access Settings, and Show desktop background image was turned on, so running the same commands and no results
- For now, skipping the wallpaper, as everything else is solved, but that one.
- Update: inputted the wallpaper to all the Windows servers via picking img10.jpg and added it to the CompanyShare,  set a network path: \\DC01\CompanyShare\img10.jpg 

# Phase 6
- The sixth phase involves creating a IT onboarding workflow and adding a new user to the company.
- Went on DC-01 and opened Active Directory Users and Computers and right-clicked on the Sales OU to add the new member, Juan Pope; unchecked user must change password at next logon for later purposes.
- Double-clicked juan.pope and went on Member Of and added juan.pope to the SalesUsers group.
- Logged on to the SALES-PC using the juan.pope account and verified that the Z: drive was on File Explorer.
- Pressed Windows + R and confirmed that the CompanyShare folder was there.
- Verified that the juan.pope account can access the Sales folder but cannot access the HR folder.
- Used the command prompt and ran the command net use to verify that Z: is mapped

# Phase 7
- The seventh phase involves creating a IT offboarding workflow and removing the previously created new user from the company
- Went on DC-01 and opened Active Directory Users and Computers and right-clicked on juan.pope and clicked Disable Account.
- Double-clicked on juan.pope and went to Member Of and selected SalesUsers and clicked Remove.
- Tried logging in SALES-PC with juan.pope and account and got the message Your account has been disabled.
- Created new user folders for each user in each of their respective department folders and created an archive folder inside the CompanyShare folder.
- Right-clicked on juan.pope folder and choose the Properties security tab and set full control to both juan.pope and the administrators group.
- Clicked on the advanced tab and picked Change next to where the owner was written, typed in Administrator, and then checked Replace owner on subcontainers and objects.
- Back to Advanced Security settings and clicked Disable Inheritance and chose Covert Inherited permissions into explicit permissions.
- Clicked Add and select a principal and picked locations to company.local and typed in juan.pope and checked names and set to full control; same method for administrators
- Moved juan.pope folder to the archive folder and adjusted the folder properties and security set, both HR and Sales users to remove access to archived files.
- Folder is preserved in the archive folder, but no user's side can access it.
  
# Phase 8
- The eighth phase involves creating a new VM for Linux and a remote desktop connection to create a IT scenario.
- Went on SALES-PC and pressed Windows + R and typed sysdm.cpl; went on remote tab and selected allow remote connections to this computer.
- Opened Windows Defender Firewall and clicked Allow an app through firewall and made sure that Remote Desktop was selected.
- Went on DC01 and pressed Windows + R and typed mstsc, and entered SALES-PC IP, and it failed.
- Rechecked everything and one change made was unchecking the remote desktop, and rechecked it, and the second attempt worked and accessed SALES-PC from DC01.
- Repeated the same steps for HR-PC.
- Was confused at first about whether to install Linux on a Windows server and realized there was no internet on the servers, and tried to fix the internet to no avail.
- Read the instructions that were given to me again, and clearly realized that the next step was to make a new VM for Linux.
- Downloaded Ubuntu Desktop 24.04.4 LTS.
- Created a new VM with the Ubuntu iso file, and it was successfully created, only to realize that the file version was put on the OneDrive, which is a no, so it was deleted and put in a new file away from OneDrive and was successfully recreated.
- Installed OpenSSH via sudo apt update and sudo apt install openssh-server -y
- Started SSH via sudo systemctl start ssh and enabled at boot via sudo systemctl enable ssh
- Ran the ip a command to find the IP address, and it gave me 192.168.122.129, and went on DC01 and opened the command prompt to run ssh james-linux@192.168.122.129, and it failed to connect.
- Tried to ping 192.168.122.129, and it gave the request timed out error
- Shut off all the machines to make sure they are all set to NAT, and they were, and turned back on the Linux server and ran sudo system status ssh and got the running status.
- Recognized that the Linux IP address is not set to the same network as the Windows Servers and ran sudo dhclient -v and got back that the command was not found, so next is to run sudo apt update and sudo apt install isc-dhcp-client -y and then put in sudo dhclient -v again and put ip a and it's still the same IP address as before.
- Ran sudo netplan apply and sudo reboot, and after rebooting and relogging in, ran ip a and got the same results as before.
- Opened the config file and ran sudo nano /etc/netplan/00-installer-config.yaml and wrote in network: version: 2 eternets: ens33 and wrote in the new IP address for the Linux server along with the default gateway of the network and saved my changes.
- Ran ip a again and this time, the new IP address shows up.
- Pinged the new IP address on DC-01, and it was a success.
- Ran ssh james-linux@192.168.1.40 and gained Linux server access.
- Restarted the SSH service via sudo systemctl status ssh and then put in sudo systemctl status ssh and got active(running)
