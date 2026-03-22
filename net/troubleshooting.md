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
- Went to the control panel to set a static IP address, subnet mask, default gateway, and DNS server.
- Renamed the PC to DC01 and restarted the computer for the changes to come through.
- Opened Server Manager to install Active Directory Domain Services, which led to promoting the server to be a domain controller, creating a domain and the password for it, and then restarting the computer.
- The administrator account was created and was successfully able to log in with the password that I set up

# Phase 2
- This second phase involves creating users and groups to recreate the feeling of a company.
- Opened Active Directory Users and Computers, where you can see the company domain that was initially created.
- Right-clicked on the company domain to create organizational units for different departments: Sales, HR, and IT.
- Added users based on their job title and responsibilities, and checked off for the user to change their password the next time they log in.
- Created Security groups and added users to their respective groups, and applied access control to the users based on their group

# Phase 3
- The third phase involves creating two client machines and connecting them to a central authentication system via the domain controller previously created.
- The two client machines were created using the Windows 10 iso file, compared to the Windows 2019 server for the DC to replicate the client machines as employee computers.
- All machines are connected using NAT
- The client machines have 2 GB RAM minimum compared to the Domain controller's minimum of 4 GB RAM.
- Right-clicked on This PC and renamed each client machine to SALES-PC and HR-PC, respectively, and then restarted both.
- First focused on SALES-PC and accessed the Ethernet properties for IPv4, and initially only set the same Preferred IP address to the DC's IP address.
- Went to Settings and System to access Rename this PC(Advanced) and click Change to select Domain and entered the company domain, and got the error that it didn't exist.
- Went to the DC to check if everything was functioning and the domain was there, and there was confusion, so the next step was going to the command center from the SALES-PC to ping the DC's IP address, and it failed to contact the domain controller, giving the request timed out error.
- Back to the DC to check the IPv4 address, and they were as previously implemented, and ran ipconfig and got the same results.
- One mistake that was made initially was only putting the preferred IP address and not implementing a static IP address, subnet mask, and default gateway.
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

# Phase 5

# Phase 6

# Phase 7

# Phase 8
