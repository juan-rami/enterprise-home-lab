# Architecture Diagram

## Servers
- DC01(Windows Server):
- Active Directory
-  DNS
-  File Server
- Client Machines
- SALES-PC
- HR-PC
- Linux Server
- OpenSSH enabled

## Network
- VMware NAT network
- IP Range: 192.168.1.xx

## Diagram

                (Your Host Computer)

                        │

                        │

                Virtual Network

                        │

     ┌──────────────────┼──────────────────┐

     │                  │                  │

  DC01              SALES-PC            HR-PC

Windows Server       Windows             Windows

Domain Controller    Client              Client

                        │

                        │

                   LINUX-FILE

                   Ubuntu Server
