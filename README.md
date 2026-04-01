# Enterprise-home-lab
This is a step-by-step explanation of how this lab was created as you see it.

# Phase 1: Building the Domain Controller/Creating the Central Authentication Server

Purpose: To build a domain controller to act as the central authentication server for the company in this lab.

## Server Info
- Name: DC01
- IP: 192.168.1.10
- Domain: company.local

## Roles Installed
- Active Directory Domain Services

## Step by Step
- Installed Windows Server 2019
- Created VM workstation
- Configured static IP
- Renamed server
- Installed Active Directory Domain Services
- Promoted to Domain Controller

## Verification
- Successfully logged in as company administrator

## Skills Learned
- Created a Virtual Machine
- Configuring IP addresses
- Setting the computer to Domain Controller

# Phase 2: Creating the Organization's Structure

Purpose: Recreate a company environment by creating Organizational Units, users, and security groups in Active Directory.

## Organizational Units Created
- Sales
- HR
- IT

## Users Created/Info
John Sale
Username: john.sales
Department: Sales

Mary HR
Username: mary.hr
Department: HR

IT Admin 
Username: it.admin
Department: IT

## Security Groups Created
- SalesUsers
- HRUsers

## Group Memberships
- john.sales - SalesUsers
- mary.hr - HRUsers

## Step by Step
- Opened Active Directory Users and Computers
- Created Organizational Units
- Created user accounts for each OU
- Created security groups for Sales and HR
- Added users to the group based on job responsibility 

## Verification
- Users appear in their assigned OU and groups

## Skills learned
- Active Directory management
- Administration management
- Organization structure
- Identity and access management

# Phase 3: Provisioning Employee Computers to the Company's Domain

Purpose: Attempting to assign employee computers and connect them to the central authentication server.

## Client Machines Created
- SALES-PC
- HR-PC
Both use the Windows 10 OS

## Network Configuration
- All machines connected via NAT
- Each machine is configured with a static IP within the same subnet
- Clients configured to DC01 as the DNS server

## Step by Step
- Created two Windows client virtual machines
- Renamed both client machines 
- Configured DNS on both clients to lead them to DC01
- Verified connectivity using ping
- Joined both machines to the company domain
- Restarted both machines after the domain join

## Verification
- Successfully logged into client machines using domain accounts:
- company\john.sales
- company\mary.hr
- Users authenticated via Domain Controller
- Command whoami confirmed domain login
- Command echo %logonserver% returned DC01

## Skills Learned
- Creating multiple client machines
- Configuring DNS for company environments
- Troubleshooting network connectivity(ping, DNS)
- Authentication testing

# Phase 4: Configuring the Shared Company Drive

Purpose: Create the company folder and manage file access based on department using shared folders and security groups

## Folder Structure
C:\CompanyShare
- Sales
- HR

## Shared Folder
- \DC01\Company Share

## Permissions Configurations
Sales Folder
- Group: SalesUsers
- Access: Modify

HR Folder
- Group: HRUsers
- Access: Modify

## Step by Step
- Created the CompanyShare folder on DC-01 on the local disk(C:)
- Added both the Sales and HR folders on CompanyShare
- Edited permissions for each folder to modify on the Security tab 
- Added SalesUsers to the HR folder to set permissions to deny and vice versa

## Testing
Test 1: john.sales
- Access Sales: Success
- Access HR: Access Denied

Test 2: mary.hr
- Access HR: Access
- Access Sales: Access Denied

## Verfication
- Permissions successfully restrict access by department
- Users can only access their department folders

## Skills Learned
- File sharing
- NTFS permission management
- Group-based access control
- Access testing

# Phase 5: Configuring Group Policy

Purpose: Create centralized IT management using Group Policy Objects

## Policies Configured
Password policy:
- Complexity enabled
- Minimum length: 8 characters

Drive Mapping:
- Network drive mapped to: \DC01\CompanyShare
- Drive letter: Z:

Restrictions
- Control access disabled
- Same wallpaper applied to all servers

## GPO Deployment
- GPO Name: Company Policy
- Linked to Sales and HR departments

## Step by Step
- Went to DC01 and Group Policy Management and created a GPO called Company Policy inside the company.local
- Enabled Password complexity and a minimum of 8 characters
- Created a new mapped drive
- Disabled control panel access
- Right-clicked both HR and Sales OU to link them to the company policy GPO
- Picked img10.jpg and added it to the CompanyShare, enabled desktop wallpaper, and set a network path
- Ran gpupdate /force and shutdown /r /t 0 on the command prompt

## Verification
- Drive successfully mapped on client machines
- Control Panel access restricted
- Password policy enforced
- Wallpaper shows in all servers
- Verified using gpresult

## Skills learned
- Creating a company group policy and deploying it
- Creating a user environment
- Implementing a central company system
- Troubleshooting GPO application

# Phase 6: Emulating Real Onboarding Workflow

Purpose: Create an onboarding process for a new employee

## User Created/Info
- Name: Juan Pope
- Username: juan.pope
- Department: Sales
- Group: SalesUsers

## Step by Step
- Opened Active Directory Users and Computers
- Went to the Sales OU and added juan.pope
- Added juan.pope to SalesUsers
- Logged on to SALES-PC with the juan.pope account
- Verify that the network share drive and the CompanyShare folder are there
- Checked if access to the Sales folder was allowed and the HR folder was not allowed

## Verfication
- User created and assigned
- Login confirmed
- Network access configured correctly

## Skills learned
- Configuring group membership
- Network share access control

# Phase 7: Emulating Real Offboarding Removal

Purpose: Create an offboarding process to prevent unauthorized access.

## User Deleted
- juan.pope

## Step by Step
- Opened Active Directory Users and Computers
- Went to the Sales OU and disabled juan.pope
- Removed juan.pope from SaleUsers
- Created user folder for each member
- Set permissions on juan.pope file and moved to a newly created archive folder
- Removed juan.pope from the archived file properties/security tab

## Verification
- juan.pope cannot log on
- User cannot authenticate
- No access to shared resources
- Data reserved for auditing

## Skills learned
- Security group management
- File permissions
- Data retention

# Phase 8: Remote Administration

Purpose: Create a Linux server to emulate IT support situations using remote tools

## New Server Created
- Linux

## Step by Step
- Enabled Remote Desktop by configuring RDP on client machines and allowing access through the firewall
- Remote Desktop Connection from DC-01 to SALES-PC and HR-PC using mstsc and logged in with domain credentials
- Created new Linux Server VM
- Installed OpenSSH on Linux machine and enabled it
- Connected to Linux server from DC-01 via SSH
- Verified remote connection
- Restarted SSH service and verified running

## Verfication
- RDP access to Windows clients
- SSH access to Linux and Windows servers
- Remote service management confirmed

## Skills learned
- Setting up Remote Desktop
- Linux administration
- Network troubleshooting
- Service management

# Screenshots
- Link: https://github.com/juan-rami/enterprise-home-lab/tree/main/screenshots
