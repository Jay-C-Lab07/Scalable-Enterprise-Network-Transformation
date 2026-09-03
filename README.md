\# Scalable Enterprise Network Transformation



\## Secure Multi-Floor Campus, Data Center and Branch Infrastructure



!\[Cisco Packet Tracer](https://img.shields.io/badge/Cisco-Packet%20Tracer-1BA0D7?style=for-the-badge\\\&logo=cisco\\\&logoColor=white)

!\[Networking](https://img.shields.io/badge/Networking-Enterprise-blue?style=for-the-badge)

!\[Status](https://img.shields.io/badge/Status-Completed-success?style=for-the-badge)

!\[Routing](https://img.shields.io/badge/Routing-OSPF%20%7C%20EIGRP-orange?style=for-the-badge)

!\[Security](https://img.shields.io/badge/Security-ASA%20%7C%20ACL%20%7C%20SSH-red?style=for-the-badge)



\## Project Overview



This project simulates the transformation of a growing organization’s basic network into a scalable, secure and highly available enterprise infrastructure.



The solution includes a three-floor headquarters, redundant distribution layer, centralized data center, remote branch office, internet service provider, firewall-protected DMZ, corporate wireless network, isolated guest wireless network and voice VLANs.



The complete network was designed, configured and tested using Cisco Packet Tracer.



\[Download the Packet Tracer project](Enterprise-Network.pkt)



\## Business Problem



A growing organization requires reliable connectivity between departments, servers, wireless users and a remote branch office. A basic flat network creates several problems:



\* Broadcast traffic affects the entire organization.

\* Departments are not logically separated.

\* A single switch or gateway failure can interrupt operations.

\* Address assignment becomes difficult to manage manually.

\* Guest users may gain access to internal resources.

\* Public servers can expose the internal network.

\* Network devices may be managed through insecure protocols.

\* Branch and headquarters networks may lack dynamic route exchange.



\## Implemented Solution



The network was redesigned using a hierarchical and redundant enterprise architecture:



\* Department-based VLAN segmentation

\* Redundant multilayer distribution switches

\* Rapid-PVST+ traffic-path optimization

\* LACP EtherChannel between distribution switches

\* HSRP default-gateway redundancy

\* Centralized DHCP and DNS services

\* OSPF routing at headquarters

\* EIGRP routing across the branch WAN

\* Bidirectional OSPF–EIGRP redistribution

\* Cisco ASA firewall with Inside, Outside and DMZ zones

\* Dynamic PAT for enterprise internet access

\* Static NAT for publishing the DMZ web server

\* Guest Wi-Fi isolation with extended ACLs

\* WPA2-PSK Corporate and Guest wireless networks

\* SSH version 2 for secure device management

\* Separate voice VLANs for IP phones

\* Redundancy and failure-recovery testing



\## Physical Topology



!\[Enterprise Physical Topology](screenshots/01-Physical-Topology.png)



\## Network Architecture



| Layer/Site          | Devices                         | Purpose                                      |

| ------------------- | ------------------------------- | -------------------------------------------- |

| Internet edge       | R-ISP, FW-EDGE                  | ISP simulation, firewalling and NAT          |

| Headquarters core   | R-HQ                            | HQ edge routing and branch WAN termination   |

| Distribution        | DIST-SW1, DIST-SW2              | Layer 3 switching, HSRP, SVIs and OSPF       |

| Access              | FLOOR1-SW, FLOOR2-SW, FLOOR3-SW | Department and wireless connectivity         |

| Data center         | DC-SW                           | DHCP, DNS and internal web services          |

| Branch              | R-BRANCH, BRANCH-SW             | Remote users, voice and management           |

| DMZ                 | SRV-DMZ-WEB                     | Public-facing web service                    |

| Internet simulation | SRV-INTERNET                    | External connectivity and DMZ-access testing |



\## VLAN and IP Addressing Plan



\### Headquarters



| VLAN | Name             | Subnet                | Virtual Gateway |

| ---: | ---------------- | --------------------- | --------------- |

|   10 | SALES            | 10.10.10.0/24         | 10.10.10.1      |

|   20 | HR               | 10.10.20.0/24         | 10.10.20.1      |

|   30 | FINANCE          | 10.10.30.0/24         | 10.10.30.1      |

|   40 | IT               | 10.10.40.0/24         | 10.10.40.1      |

|   50 | MANAGEMENT       | 10.10.50.0/24         | 10.10.50.1      |

|   60 | SERVERS          | 10.10.60.0/24         | 10.10.60.1      |

|   70 | VOICE            | 10.10.70.0/24         | 10.10.70.1      |

|   80 | CORP-WIFI        | 10.10.80.0/24         | 10.10.80.1      |

|   90 | GUEST-WIFI       | 10.10.90.0/24         | 10.10.90.1      |

|   99 | NETWORK-MGMT     | 10.10.99.0/24         | 10.10.99.1      |

|  999 | NATIVE-BLACKHOLE | No Layer 3 addressing | None            |



\### Branch



| VLAN | Name             | Subnet                | Gateway    |

| ---: | ---------------- | --------------------- | ---------- |

|  110 | BRANCH-USERS     | 10.20.10.0/24         | 10.20.10.1 |

|  120 | BRANCH-VOICE     | 10.20.20.0/24         | 10.20.20.1 |

|  199 | BRANCH-MGMT      | 10.20.99.0/24         | 10.20.99.1 |

|  999 | NATIVE-BLACKHOLE | No Layer 3 addressing | None       |



\### Routed and Edge Networks



| Connection         | Network         |

| ------------------ | --------------- |

| R-HQ to DIST-SW1   | 10.255.10.0/30  |

| R-HQ to DIST-SW2   | 10.255.10.4/30  |

| R-HQ to R-BRANCH   | 10.255.1.0/30   |

| R-HQ to FW-EDGE    | 10.255.2.0/30   |

| FW-EDGE to R-ISP   | 203.0.113.0/29  |

| DMZ                | 172.16.10.0/24  |

| Simulated internet | 198.51.100.0/24 |



\## High Availability



\### HSRP



HSRP provides a virtual default gateway for every headquarters VLAN. If one distribution switch becomes unavailable, the second switch assumes the Active role.



Traffic ownership is distributed between both switches:



\* DIST-SW1 is preferred for VLANs 10, 30, 50, 70, 90 and 99.

\* DIST-SW2 is preferred for VLANs 20, 40, 60 and 80.

\* Preemption restores the preferred Active switch after recovery.



!\[HSRP Active and Standby Roles](screenshots/05-HSRP-Active-Standby.png)



\### Rapid-PVST+



Rapid-PVST+ provides loop prevention and per-VLAN path selection.



\* DIST-SW1 is the primary root for VLANs aligned with its HSRP Active role.

\* DIST-SW2 is the primary root for the remaining VLANs.

\* This alignment reduces unnecessary inter-switch traffic.



!\[VLAN 10 Root Bridge](screenshots/03-Rapid-PVST-VLAN10-Root.png)



!\[VLAN 20 Root Bridge](screenshots/04-Rapid-PVST-VLAN20-Root.png)



\### LACP EtherChannel



Two Fast Ethernet links between the distribution switches are bundled into Port-Channel 1 using LACP.



Benefits include:



\* Increased aggregate bandwidth

\* Link-level redundancy

\* Simplified STP topology

\* Continued operation after a member-link failure



!\[EtherChannel Verification](screenshots/02-EtherChannel-Verification.png)



\## Dynamic Routing



\### OSPF at Headquarters



OSPF Area 0 exchanges routes between:



\* R-HQ

\* DIST-SW1

\* DIST-SW2



The two distribution switches advertise all headquarters VLANs. R-HQ learns equal-cost routes through both distribution switches.



!\[OSPF Neighbors](screenshots/10-OSPF-Neighbors.png)



!\[OSPF Learned Routes](screenshots/11-OSPF-Learned-Routes.png)



\### EIGRP at the Branch



EIGRP autonomous system 100 exchanges routes between R-HQ and R-BRANCH.



Advertised branch networks include:



\* 10.20.10.0/24

\* 10.20.20.0/24

\* 10.20.99.0/24



!\[EIGRP Neighbor](screenshots/13-EIGRP-Neighbor.png)



\### Route Redistribution



R-HQ performs bidirectional redistribution between OSPF and EIGRP.



This allows:



\* Headquarters devices to reach branch networks

\* Branch devices to reach headquarters networks

\* The branch to learn the enterprise default route



!\[OSPF and EIGRP Redistribution](screenshots/12-OSPF-EIGRP-Redistribution.png)



\## Centralized Network Services



\### DHCP



SRV-DHCP-DNS provides centralized IPv4 addressing for:



\* Headquarters department VLANs

\* Corporate Wi-Fi

\* Guest Wi-Fi

\* Voice VLANs

\* Branch users

\* Branch voice devices



DHCP relay is implemented using `ip helper-address` on routed VLAN interfaces.



!\[DHCP Address Assignment](screenshots/06-DHCP-Sales-PC.png)



\### DNS



The internal DNS server resolves:



```text

intranet.entcorp.com → 10.10.60.20

```



!\[DNS Resolution](screenshots/08-DNS-Resolution.png)



\### Internal Web Service



The internal web server is hosted at:



```text

10.10.60.20

```



Users can access it through:



```text

http://intranet.entcorp.com

```



!\[Internal Web Portal](screenshots/09-Internal-Web-Portal.png)



\## Firewall, DMZ and NAT



The Cisco ASA firewall contains three security zones:



| Zone    | Security Level | Network                                  |

| ------- | -------------: | ---------------------------------------- |

| Inside  |            100 | 10.255.2.0/30 toward enterprise networks |

| DMZ     |             50 | 172.16.10.0/24                           |

| Outside |              0 | 203.0.113.0/29                           |



\### Dynamic PAT



All headquarters and branch `10.0.0.0/8` addresses are dynamically translated to the ASA outside interface address:



```text

203.0.113.2

```



\### Static NAT



The public address below is mapped to the DMZ web server:



```text

203.0.113.3 → 172.16.10.10

```



Only required web traffic is permitted from the outside:



\* TCP port 80

\* TCP port 443



!\[NAT and PAT Verification](screenshots/15-NAT-PAT-Verification.png)



!\[Public DMZ Web Access](screenshots/16-Public-DMZ-Web-Access.png)



\## Wireless Security



\### Corporate Wi-Fi



| Setting  | Value                         |

| -------- | ----------------------------- |

| SSID     | ENT-CORP                      |

| VLAN     | 80                            |

| Security | WPA2-PSK with AES             |

| Access   | Internal network and internet |



!\[Corporate Wi-Fi Connectivity](screenshots/18-Corporate-WiFi-Connectivity.png)



\### Guest Wi-Fi



| Setting    | Value                                     |

| ---------- | ----------------------------------------- |

| SSID       | ENT-GUEST                                 |

| VLAN       | 90                                        |

| Security   | WPA2-PSK with AES                         |

| Access     | DNS and internet only                     |

| Restricted | Headquarters and branch internal networks |



An extended ACL blocks Guest Wi-Fi traffic destined for private enterprise networks while permitting DHCP, DNS and internet access.



!\[Guest Wi-Fi Isolation](screenshots/17-Guest-WiFi-Isolation.png)



\## Secure Management



SSH version 2 is enabled using local administrator authentication and RSA keys.



Management addresses include:



| Device    | Management IP |

| --------- | ------------- |

| DIST-SW1  | 10.10.99.2    |

| DIST-SW2  | 10.10.99.3    |

| FLOOR1-SW | 10.10.99.11   |

| FLOOR2-SW | 10.10.99.12   |

| FLOOR3-SW | 10.10.99.13   |

| DC-SW     | 10.10.99.14   |

| BRANCH-SW | 10.20.99.2    |



Telnet is disabled on configured VTY lines by allowing SSH only.



!\[SSH Remote Management](screenshots/19-SSH-Remote-Management.png)



\## Voice Network



Separate voice VLANs were configured to isolate IP-phone traffic:



\* Headquarters Voice VLAN 70

\* Branch Voice VLAN 120



Switch access ports support separate data and voice VLANs on the same physical connection.



!\[HQ Voice VLAN](screenshots/20-HQ-Voice-VLAN-Verification.png)



!\[Branch Voice VLAN](screenshots/21-Branch-Voice-VLAN-Verification.png)



> Cisco CME extension registration was not implemented because the selected Packet Tracer 2911 IOS image does not support the `telephony-service` command. Voice VLANs, switch-port separation and DHCP/TFTP parameters were configured and verified.



\## Failure and Recovery Testing



\### HSRP Failover



DIST-SW1 VLAN 10 was administratively disabled to simulate a gateway failure. DIST-SW2 successfully became the Active HSRP router, and user connectivity remained available.



!\[HSRP Failover](screenshots/22-HSRP-Failover.png)



\### EtherChannel Member Failure



One LACP member link was shut down:



```text

Fa0/23(D)

Fa0/24(P)

Po1(SU)

```



Port-Channel 1 remained operational through the surviving member, and connectivity continued without interruption.



!\[EtherChannel Link Failure](screenshots/23-EtherChannel-Link-Failure.png)



\## Final End-to-End Validation



The completed network successfully demonstrated:



\* Branch-to-HQ communication

\* Inter-VLAN routing

\* Centralized DHCP and DNS

\* Corporate wireless access

\* Guest-network isolation

\* Headquarters and branch internet access

\* Public access to the DMZ website

\* Secure SSH management

\* HSRP gateway failover

\* EtherChannel link redundancy



!\[End-to-End Connectivity](screenshots/24-End-to-End-Connectivity.png)



\## Troubleshooting Performed



Several realistic network problems were identified and resolved during implementation:



| Problem                                     | Cause                                                                            | Resolution                                                         |

| ------------------------------------------- | -------------------------------------------------------------------------------- | ------------------------------------------------------------------ |

| Servers could not reach their gateway       | Static server addressing had not been configured                                 | Assigned correct addresses, subnet masks and gateways              |

| DNS requests timed out                      | DNS record was created on the web server instead of the DNS server               | Moved the record to SRV-DHCP-DNS                                   |

| Branch DHCP failed                          | Incorrect Branch Users pool start address                                        | Corrected the pool to the 10.20.10.0/24 network                    |

| Guest Wi-Fi received an APIPA address       | DHCP broadcast was blocked by the Guest ACL on the alternate distribution switch | Permitted DHCP client-to-server traffic on both switches           |

| DMZ gateway was unreachable                 | Incorrect gateway `176.16.10.1`                                                  | Corrected it to `172.16.10.1`                                      |

| ASA DMZ name was not retained               | ASA 5505 third-VLAN licence restriction                                          | Applied `no forward interface Vlan1` before assigning `nameif dmz` |

| Static service PAT was unsupported          | Packet Tracer ASA command limitation                                             | Expanded the outside subnet and used dedicated public static NAT   |

| HSRP tracking did not react in simulation   | Packet Tracer tracking limitation                                                | Validated failover by shutting the active VLAN interface           |

| IP phones remained on CM-list configuration | Selected router IOS did not support CME                                          | Verified voice VLAN and switch-port configuration                  |



\## Skills Demonstrated



\* Enterprise network planning

\* IPv4 addressing and subnetting

\* VLANs and inter-VLAN routing

\* VTP and 802.1Q trunking

\* Rapid-PVST+

\* LACP EtherChannel

\* HSRP

\* OSPF

\* EIGRP

\* Route redistribution

\* DHCP relay

\* DNS and HTTP services

\* Static and dynamic routing

\* Cisco ASA firewall configuration

\* NAT and PAT

\* Extended ACLs

\* Wireless security

\* SSH administration

\* Network troubleshooting

\* Redundancy testing

\* Technical documentation



\## Project Files



```text

Scalable-Enterprise-Network-Transformation/

├── Enterprise-Network.pkt

├── README.md

└── screenshots/

&#x20;   ├── 01-Physical-Topology.png

&#x20;   ├── 02-EtherChannel-Verification.png

&#x20;   ├── 03-Rapid-PVST-VLAN10-Root.png

&#x20;   ├── 04-Rapid-PVST-VLAN20-Root.png

&#x20;   ├── 05-HSRP-Active-Standby.png

&#x20;   ├── 06-DHCP-Sales-PC.png

&#x20;   ├── 07-Inter-VLAN-Routing.png

&#x20;   ├── 08-DNS-Resolution.png

&#x20;   ├── 09-Internal-Web-Portal.png

&#x20;   ├── 10-OSPF-Neighbors.png

&#x20;   ├── 11-OSPF-Learned-Routes.png

&#x20;   ├── 12-OSPF-EIGRP-Redistribution.png

&#x20;   ├── 13-EIGRP-Neighbor.png

&#x20;   ├── 14-Branch-to-HQ-Connectivity.png

&#x20;   ├── 15-NAT-PAT-Verification.png

&#x20;   ├── 16-Public-DMZ-Web-Access.png

&#x20;   ├── 17-Guest-WiFi-Isolation.png

&#x20;   ├── 18-Corporate-WiFi-Connectivity.png

&#x20;   ├── 19-SSH-Remote-Management.png

&#x20;   ├── 20-HQ-Voice-VLAN-Verification.png

&#x20;   ├── 21-Branch-Voice-VLAN-Verification.png

&#x20;   ├── 22-HSRP-Failover.png

&#x20;   ├── 23-EtherChannel-Link-Failure.png

&#x20;   └── 24-End-to-End-Connectivity.png

```



\## Author



\*\*Jay Kishan Choudhari\*\*



Electronics and Communication Engineering graduate focused on networking, cloud infrastructure, Linux, Windows Server and IT support.



\---



If you found this project useful, consider giving the repository a star.



