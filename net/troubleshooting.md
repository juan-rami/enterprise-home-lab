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

# Phase 4

# Phase 5

# Phase 6

# Phase 7

# Phase 8
