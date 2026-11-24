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

-All prerequisite checks passed successfully

-DNS delegation warning was reviewed and determined to be non-blocking due to the isolated nature of the homelab

-Server is ready for domain controller promotion.

-An automatic reboot is expected following successful promotion.

### Notes:

**DNS Domain Name:** lab.example.com
**NetBIOS Domain Name:** LAB
**Domain Controller Hostname:** DC01

Retained the automatically generated NetBIOS domain name for compatibility with Windows authentication and legacy domain naming conventions.

### Domain Controller Promotion Results:

-Successfully promoted DC01 to a domain controller
-Established the Active Directory forest and root domain lab.example.com
-Forest Functional Level1: Windows Server 2016.
-Domain Functional Level1: Windows Server 2016.
-Successfully installed and configured the DNS Server role.
-Server successfully rebooted following promotion.

-Verified the presence of AD DS and DNS within Server Manager and accessed Active Directory Users and Computers to inspect the newly established domain environment.

## Objective 3: Configure and Verify DNS

Verified DNS configuration following successful domain controller promotion. 

Utilized the following PowerShell commands to validate DNS functionality: 
-'ipconfig /all'
-'nslookup lab.example.com'
-'nslookup dc01.lab.example.com'
-'nslookup -type=SRV _ldap._tcp.dc._msdcs.lab.example.com'
-'Get-Services DNS'
-'Get-DnsServerZone'

Confirmed the presence of the following Active Directory-integrated forward lookup zones:

-lab.example.com
-_msdcs.lab.example.com

-Successfully resolved DC01 to it's assigned IPv4 address of 192.168.56.10

-Verified the presence of LDAP SRV records identifying DC01 as a domain controller providing LDAP services on port 389.

-During verification, repeated DNS request timeouts were observed despite successful name resolution. 

Refer to Issue 2 within "Challenges and Troubleshooting" for diagnostic methodology and remediation.

Challenges and Troubleshooting  
--------------------------------------------------------------------------------------------------

Observation 1: DNS Delegation Warning 

Observation: The configuration wizard displayed a warning stating that the authoritative parent DNS zone could not be found

Cause: The homelab is an isolated environment without an existing internal parent DNS zone

Assessment: Expected behavior when establishing the independent Active Directory forest. 

Action: Proceeded without creating DNS delegation. 

Verification: Post-installation DNS diagnostics completed successfully. The DNS delegation test returned a PASS result, confirming that the initial warning did not prevent successful DNS implementaiton.

### Issue 2: DNS Request Timeouts and Unknown DNS Server:

**Problem:**

Initial DNS verification using nslookup returned
correct DNS records but experienced repeated
two-second request timeouts. The DNS server was
also identified as Unknown.

**Initial Observations:**

- Forward DNS resolution was successful.
- LDAP SRV records were successfully retrieved.
- DNS requests experienced repeated timeouts.
- DNS server hostname could not be identified.

**Troubleshooting Methodology:**

1. Verified network configuration using `ipconfig /all`.
2. Identified IPv4 and IPv6 loopback addresses as
   the configured DNS servers following promotion.
3. Explicitly queried the DNS server using its
   static IPv4 address of 192.168.56.10.
4. Observed that timeouts persisted, indicating
   the issue was not exclusive to IPv6.
5. Verified that the DNS service was running
   using `Get-Service DNS`.
6. Inspected existing DNS zones using
   `Get-DnsServerZone`.
7. Identified the absence of a reverse lookup
   zone for the 192.168.56.0/24 network.

**Root Cause Analysis:**

The absence of a reverse lookup zone and an appropriate
PTR record prevented DNS from resolving DC01's IPv4
address to its fully qualified domain name.

The isolated network configuration was also considered
a potential contributing factor to DNS timeouts.

**Resolution:**

Created an Active Directory-integrated IPv4 reverse
lookup zone through DNS Manager.

- Zone Type: Primary, AD-integrated
- Replication Scope: Domain-wide
- Network ID: 192.168.56
- Zone Name: 56.168.192.in-addr.arpa
- Dynamic Updates: Secure only

Created and corrected a PTR record with the following
configuration:

- IP Address: 192.168.56.10
- Hostname: dc01.lab.example.com

**Post-Resolution Verification:**

- DNS server successfully identified by its FQDN.
- Forward DNS resolution returned the correct IP address.
- No timeouts observed during the final explicit
  IPv4 forward lookup test.
- All executed DNS diagnostic categories passed.

The DNS server identification issue was resolved.
The exact cause of all initial DNS timeouts was
not conclusively established.

-------------------------------------------------------------------------------------------------
