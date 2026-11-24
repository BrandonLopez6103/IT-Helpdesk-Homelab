Lab 01: Windows Server Configuration/Implementation 
Date: 11/23/2026
Time: 7:48PM CST

Objectives: Successfully setup and configure a virtual machine running
Windows Server 2025 with 4-6GB of RAM 

Environment:
Host OS: Windows 11 Home
Hypervisor: Oracle VirtualBox
Guest OS: Windows Server 2022 Evaluation
VM Name: DC01
RAM: 4-6GB (Planned)
Storage: 60GB (Planned)
Network: Isolated virtual network

Initial Implementation:
Successfully installed and configured Oracle Virtualbox
Windows Server user credentials set prior to install as "Admin"
VM name set as DC01
RAM set to 4GB, storage to 60GB, may adjust in the future
Successful boot and selection of disk for install of 
Windows Server 2025 after slight troubleshooting (see section labeled "Issue 1" within "Challenges and Troubleshooting" for details)

First Boot-up:
Windows Server successfully initialized after install 
Although I made the mistake of installing the "Core" version
I will shutdown this version and create a new VM titled as DC01-Desktop 

Second Implementation: 
VM name set as DC01-Desktop 
Set the desired operating system for install to be the desktop version of Windows Server 2022
Admin credentials set, login successful 

DC01-Desktop Configuration: 
Renamed the Computer name to DC01-Desktop within the properties section 
Restarted with the reasoning of "Other:Planned" at 8:32PM 11/23/2026
Verified that the change was properly implemented 
DC01-Desktop shutdown to move onto the next step of network configuration 

Configure Virtualbox Networking:
Set IPv4 address of Virtualbox Network adapter to 192.168.56.1/24
Configured DHCP to be disabled to prevent automatic IP addressing 
Set DC01-Desktop VM to be attached to the VirtualBox Host-Only Ethernet Adapter 
Initialized DC01-Desktop to configure network settings within Windows Server
DC01-Desktop initially assigned APIPA address of 169.254.42.153
Assigned DC01-Desktop the IPv4 address of 192.168.56.10 at 10:53PM 11/23/2026
Confirmed IPv4 address assignment by running the command "ipconfig /all" 
Confirm connectivity with Network adapter by pinging 192.168.56.1 with no success 
See "Issue 2" within "Challenges and Troubleshooting" for resolution and troubleshooting methodology
After resolving the connectivity issue, I was able to successfully ping the Network adapter

Challenges and Troubleshooting:

Issue 1 (Minimal/User Error)
-------------------------------------------------------------------------------
Initial selection of disk for install had a slight hiccup,
upon selection, the primary 60GB disk was already in use. 
Simply clearing the disk space allowed me to select the disk for installation
-------------------------------------------------------------------------------

Issue 2: ICMP Connectivity Failure
-------------------------------------------------------------------------------
Problem: 
Attempts to ping between the Windows Server VM and the physical host resulted in 100% packet loss despite both devices being configured on the same subnet.

Troubleshooting:
Verified static IPv4 addressing using ipconfig /all
Utilized arp -a to confirm successfull MAC address resolution
Identified Windows Defender Firewall as the suspected cause
Investigated inbound ICMPv4 firewall rules on both operating systems

Root Cause:
Windows Defender Firewall was blocking inbound ICMPv4 Echo Requests on both the physical host and Windows Server VM

Resolution: 
Enabled inbound ICMPv4 Echo Requests rules on both systems and restricted their scope to the appropriate homelab IP addresses, maintaining firewall protection

Verification: 
Successfully tested bidirectional connectivity using the ping command. Both systems returned four successful replies with 0% packet loss.

Status: 
Resolved

Notes:
Initial pinging of 192.168.56.1 failed due to what I believed to be an outbound firewall rule 
By enabling ICMP requests for File and Printer sharing with the scope of 192.168.56.0/24 I believed this would resolve the issue, but to no avail 
After review, I realized that I had enabled inbound traffic to DC01-Desktop, but not inbound traffic to 192.168.56.1, or the address of the Virtualbox Network Adapter
By now enabling ICMP requests for File and Printer sharing for both the Virtualbox Network Adapter as well as DC01-Desktop, I was able to resolve the connectivity issue
-------------------------------------------------------------------------------

