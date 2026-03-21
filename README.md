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

# Phase 2: Creating the Organization's Structure

Purpose: Recreate a company environment by creating Organizational Units, users, and security groups in Active Directory.

## Organizational Units Created
- Sales
- HR
- IT

## Users Created/Info
John Sale:
Username: john.sales
Department: Sales

Mary HR:
Username: mary.hr
Department: HR

IT Admin: 
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

# Phase 3: Provisioning Employee Computers to the Company's Domain


# Phase 4: Configuring the Shared Company Drive


# Phase 5: Configuring Group Policy


# Phase 6: Emulating Real Onboarding Workflow


# Phase 7: Emulating Real Offboarding Removal


# Phase 8: Remote Administration
