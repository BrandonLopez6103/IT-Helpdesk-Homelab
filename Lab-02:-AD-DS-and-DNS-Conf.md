# Lab 02: Active Directory and DNS 
Date: 11/24/2026
Status: In Progress 

## Objectives: 
Install AD DS
Promote server to domain controller
Configure DNS

Environment:

Hypervisor: Oracle VirtualBox
Hostname: DC01

### Objective 1 Install AD DS:
Snapshot created titled "DC01-Baseline" to rollback to if need be 
Ran through pre-implementation checks consisting of verifying Windows Server version, hostname, static IPv4 config, confirmed connectivity to 192.168.56.1, confirmed configuration of DNS server address

Hostname: DC01
Windows Server Version: Windows Server 2022 Version 21H2
Static IPv4 Address: 192.168.56.10
Connectivity to 192.168.56.1: 4 packets sent with 0% packet loss
DNS Server Address: 192.168.56.10

Selected Active Directory Domain Services (AD DS) through the "Add Roles and Features Wizard". Included the required supporting features and administrative management tools. DNS Server installation was deferred to the domain controller promotion process.

Active Directory Domain Services (AD DS) successfully installed onto DC01, now moving onto the next objective of promoting the server to domain controller 

### Objective 2 Promote Server to Domain Controller:

Selected the "Add a new forest" deployment option because the lab environment contains no exiting Active Directory domains or forests. DC01 will server as the initial domain controller and DNS server for the newly established forest.

Configured DC01 to function as both a domain controller and DNS server. This configuration allows domain-joined devices to locate Active Directory services and facilitates centralized authentication, directory management, and internal name resolution. 

Global Catalog functionality was enabled, and a DSRM password was established for disaster recovery.

### Prerequisite Validation:

All prerequisite checks passed successfully

DNS delegation warning was reviewed and determined to be non-blocking due to the isolated nature of the homelab

Server is ready for domain controller promotion.

An automatic reboot is expected following successful promotion.

### Notes:

**DNS Domain Name:** lab.example.com
**NetBIOS Domain Name:** LAB
**Domain Controller Hostname:** DC01

Retained the automatically generated NetBIOS domain name for compatibility with Windows authentication and legacy domain naming conventions.

Challenges and Troubleshooting 
--------------------------------------------------------------------------------------------------

Observation 1: DNS Delegation Warning 

Observation: The configuration wizard displayed a warning stating that the authoritative parent DNS zone could not be found

Cause: The homelab is an isolated environment without an existing internal parent DNS zone

Assessment: Expected behavior when establishing the independent Active Directory forest. 

Action: Proceeded without creating DNS delegation. 

Verification: Pending post-installation DNS testing. 
-------------------------------------------------------------------------------------------------
