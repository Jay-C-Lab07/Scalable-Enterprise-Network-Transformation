\# 🔍 Network Verification Command Reference



\## 📌 Purpose



This document lists the main commands used to verify, test and troubleshoot the \*\*Scalable Enterprise Network Transformation\*\* project.



Each section explains:



\* Where to run the command

\* What the command verifies

\* What a successful result should look like



\---



\# 1. Basic Device Verification



\## Display Interface Status



```text

show ip interface brief

```



Used on:



\* Routers

\* Multilayer switches

\* Cisco ASA



Verifies:



\* Interface IP addresses

\* Administrative status

\* Line-protocol status



Healthy routed interfaces should normally show:



```text

up    up

```



Possible results:



| Status                       | Meaning                                              |

| ---------------------------- | ---------------------------------------------------- |

| `up/up`                      | Physical and protocol layers are working             |

| `administratively down/down` | Interface is shut down                               |

| `up/down`                    | Physical link exists, but the protocol has a problem |

| `down/down`                  | Physical link is unavailable                         |



\## Display Switch-Port Status



```text

show interfaces status

```



Used on switches to verify:



\* Connected ports

\* Disabled ports

\* Access VLANs

\* Trunk ports

\* Speed and duplex



\## Display Running Configuration



```text

show running-config

```



Shows the active configuration stored in RAM.



\## Display Startup Configuration



```text

show startup-config

```



Shows the saved configuration loaded during startup.



\## Save Configuration



```text

write memory

```



Copies the running configuration into startup configuration.



\---



\# 2. VLAN Verification



\## Display VLANs



```text

show vlan brief

```



Verifies:



\* VLAN IDs

\* VLAN names

\* VLAN status

\* Access-port membership



Expected examples:



```text

10    SALES

20    HR

30    FINANCE

60    SERVERS

80    CORP-WIFI

90    GUEST-WIFI

99    NETWORK-MGMT

999   NATIVE-BLACKHOLE

```



\## Display Individual Port Configuration



```text

show interfaces fastEthernet0/3 switchport

```



Verifies:



\* Administrative switch-port mode

\* Operational mode

\* Access VLAN

\* Voice VLAN

\* Trunk configuration



Example HQ phone port:



```text

Access Mode VLAN: 10 (SALES)

Voice VLAN: 70

```



Example branch phone port:



```text

Access Mode VLAN: 110 (BRANCH-USERS)

Voice VLAN: 120

```



\---



\# 3. Trunk Verification



```text

show interfaces trunk

```



Verifies:



\* Trunking ports

\* 802.1Q encapsulation

\* Native VLAN

\* Allowed VLANs

\* Active VLANs

\* STP forwarding VLANs



Expected native VLAN:



```text

999

```



Important:



A VLAN must be:



1\. Created

2\. Allowed on the trunk

3\. Active

4\. In an STP forwarding state



\---



\# 4. VTP Verification



```text

show vtp status

```



Verifies:



\* VTP domain

\* Operating mode

\* VTP version

\* Configuration revision

\* Number of VLANs



Expected domain:



```text

ENT-CORP

```



Expected roles:



| Device             | VTP mode |

| ------------------ | -------- |

| DIST-SW1           | Server   |

| DIST-SW2           | Client   |

| HQ access switches | Client   |



Packet Tracer may display VTP version 1 on some 2960 clients even when version 2 is configured and VLAN synchronization works.



Operational success should be confirmed by checking synchronized VLANs and revision numbers.



\---



\# 5. EtherChannel Verification



```text

show etherchannel summary

```



Expected healthy state:



```text

Po1(SU)    LACP    Fa0/23(P) Fa0/24(P)

```



Flag meanings:



| Flag | Meaning                              |

| ---- | ------------------------------------ |

| `S`  | Layer 2 port-channel                 |

| `U`  | Port-channel is in use               |

| `P`  | Physical interface is bundled        |

| `D`  | Physical interface is down           |

| `I`  | Interface is operating independently |

| `s`  | Interface is suspended               |



Failure-test result:



```text

Po1(SU)    LACP    Fa0/23(D) Fa0/24(P)

```



This proves that the port-channel remains operational through one surviving member.



\---



\# 6. Spanning Tree Verification



\## Display STP Summary



```text

show spanning-tree summary

```



Verifies:



\* STP mode

\* Root-bridge ownership

\* Forwarding ports

\* Blocking ports

\* Active STP VLANs



Expected mode:



```text

rapid-pvst

```



\## Display STP for One VLAN



```text

show spanning-tree vlan 10

```



On DIST-SW1, VLAN 10 should show:



```text

This bridge is the root

```



```text

show spanning-tree vlan 20

```



On DIST-SW2, VLAN 20 should show:



```text

This bridge is the root

```



Common STP roles:



| Role       | Meaning                               |

| ---------- | ------------------------------------- |

| Root       | Best path toward the root bridge      |

| Designated | Forwarding port for a network segment |

| Alternate  | Backup path currently blocked         |



Common states:



| State | Meaning                            |

| ----- | ---------------------------------- |

| FWD   | Forwarding traffic                 |

| BLK   | Blocking traffic to prevent a loop |



\---



\# 7. HSRP Verification



```text

show standby brief

```



Verifies:



\* HSRP group

\* Priority

\* Preemption

\* Active router

\* Standby router

\* Virtual IP address



Example:



```text

Vl10   10   110 P Active   local       10.10.10.3   10.10.10.1

```



Meaning:



\* Local switch is Active.

\* Peer `10.10.10.3` is Standby.

\* `10.10.10.1` is the virtual gateway.

\* `P` means preemption is enabled.



During failover, DIST-SW2 showed:



```text

Vl10   10   100 P Active   local

```



This confirmed successful gateway failover.



\---



\# 8. OSPF Verification



\## Display OSPF Neighbours



```text

show ip ospf neighbor

```



Expected on R-HQ:



```text

2.2.2.2    FULL

3.3.3.3    FULL

```



`FULL` means the routers completely exchanged their link-state databases.



\## Display OSPF Routes



```text

show ip route ospf

```



Common OSPF route codes:



| Code   | Meaning                     |

| ------ | --------------------------- |

| `O`    | OSPF intra-area route       |

| `O IA` | OSPF inter-area route       |

| `O E1` | External Type 1             |

| `O E2` | External Type 2             |

| `O\*E2` | OSPF external default route |



Branch networks appeared on the distribution switches as:



```text

O E2 10.20.10.0/24

O E2 10.20.20.0/24

O E2 10.20.99.0/24

```



\## Display OSPF Configuration



```text

show ip protocols

```



Verifies:



\* OSPF process

\* Router ID

\* Advertised networks

\* Passive interfaces

\* Routing sources

\* Redistribution



\---



\# 9. EIGRP Verification



\## Display EIGRP Neighbours



```text

show ip eigrp neighbors

```



Expected neighbour relationship:



```text

R-HQ       → 10.255.1.2

R-BRANCH   → 10.255.1.1

```



A stable neighbour should have:



\* An uptime value

\* Queue count of `0`

\* Valid sequence numbers



\## Display EIGRP Routes



```text

show ip route eigrp

```



EIGRP route codes:



| Code   | Meaning                                 |

| ------ | --------------------------------------- |

| `D`    | Internal EIGRP route                    |

| `D EX` | External route redistributed into EIGRP |

| `D\*EX` | External EIGRP default route            |



On R-HQ, branch routes appeared as internal EIGRP routes.



On R-BRANCH, headquarters routes appeared as external EIGRP routes.



\---



\# 10. Route Verification



\## Display Complete Routing Table



```text

show ip route

```



Route-code examples:



| Code | Meaning                 |

| ---- | ----------------------- |

| `C`  | Directly connected      |

| `L`  | Local interface address |

| `S`  | Static route            |

| `S\*` | Static default route    |

| `O`  | OSPF                    |

| `D`  | EIGRP                   |



\## Check One Destination



```text

show ip route 10.10.60.0

```



Shows the route used to reach the server network.



\## Check Default Route



```text

show ip route 0.0.0.0

```



Expected on R-HQ:



```text

Known via "static"

Next hop: 10.255.2.2

```



Expected on DIST-SW1:



```text

Known via "ospf 1"

Next hop: 10.255.10.1

```



Expected on R-BRANCH:



```text

Known via "eigrp 100"

Next hop: 10.255.1.1

```



\---



\# 11. DHCP Verification



\## Client Address Verification



On a Packet Tracer PC or laptop:



```text

ipconfig

```



Verify:



\* Correct network address

\* `/24` subnet mask

\* `.1` default gateway

\* DNS server `10.10.60.10`



Example Sales client:



```text

IPv4 Address:    10.10.10.100

Subnet Mask:     255.255.255.0

Default Gateway: 10.10.10.1

DNS Server:      10.10.60.10

```



Example Guest laptop:



```text

IPv4 Address:    10.10.90.x

Default Gateway: 10.10.90.1

DNS Server:      10.10.60.10

```



An address beginning with `169.254` is an APIPA address and indicates that DHCP failed.



\## Verify DHCP Relay



```text

show ip interface vlan 90

```



Expected:



```text

Helper address is 10.10.60.10

```



\---



\# 12. DNS Verification



```text

nslookup intranet.entcorp.com

```



Expected result:



```text

Server:  10.10.60.10

Name:    intranet.entcorp.com

Address: 10.10.60.20

```



If the DNS server can be pinged but `nslookup` times out, check:



\* DNS service is On

\* The A record exists

\* The client uses `10.10.60.10` as DNS

\* UDP/TCP port 53 is permitted



\---



\# 13. Connectivity Testing



\## Test Local Gateway



```text

ping 10.10.10.1

```



Confirms local VLAN gateway connectivity.



\## Test Internal Server



```text

ping 10.10.60.10

ping 10.10.60.20

```



Confirms internal routing and server connectivity.



\## Test Branch-to-HQ Communication



```text

ping 10.10.10.100

```



Run from a branch PC.



\## Test Simulated Internet



```text

ping 198.51.100.10

```



Confirms:



\* Local gateway

\* Dynamic routing

\* Default routing

\* ASA forwarding

\* PAT

\* ISP connectivity



The first ping may fail while ARP and NAT information is learned. Repeat the test before concluding that the connection is broken.



\---



\# 14. Guest Wi-Fi ACL Verification



```text

show access-lists 190

```



Expected logic:



```text

permit DHCP

permit UDP DNS

permit TCP DNS

deny Guest-to-internal traffic

permit Guest-to-other destinations

```



Tests from GUEST-LAPTOP:



```text

ping 10.10.10.100

```



Expected: Failure.



```text

ping 198.51.100.10

```



Expected: Success.



```text

nslookup intranet.entcorp.com

```



Expected: DNS resolution succeeds because DNS is specifically permitted.



\---



\# 15. SSH Verification



\## Verify SSH Status



```text

show ip ssh

```



Expected:



```text

SSH Enabled - version 2.0

```



\## Test SSH from a Management PC



```text

ssh -l netadmin 10.10.99.2

```



A successful login displays:



```text

DIST-SW1#

```



Do not publish real SSH passwords in documentation or screenshots.



\---



\# 16. ASA Verification



\## Display ASA Interfaces



```text

show interface ip brief

```



Expected:



| Interface | Address       | Status |

| --------- | ------------- | ------ |

| Vlan1     | `10.255.2.2`  | up/up  |

| Vlan2     | `203.0.113.2` | up/up  |

| Vlan3     | `172.16.10.1` | up/up  |



\## Display ASA Routes



```text

show route

```



Expected:



\* Default route through `203.0.113.1`

\* Internal `10.0.0.0/8` route through `10.255.2.1`

\* Directly connected Inside, Outside and DMZ networks



\## Display NAT Rules



```text

show nat

```



Expected rules:



\* Static NAT for the DMZ server

\* Dynamic PAT for enterprise clients



\## Display Active Translations



```text

show xlate

```



Shows active translations between private and public addresses.



\## Display Firewall ACLs



```text

show access-list

```



Expected:



```text

OUTSIDE-IN permit TCP port 80

OUTSIDE-IN permit TCP port 443

```



ACL hit counters prove that traffic matched a rule.



\---



\# 17. Public DMZ Verification



From SRV-INTERNET, open the web browser and browse to:



```text

http://203.0.113.3

```



Expected path:



```text

SRV-INTERNET

→ R-ISP

→ ASA Outside

→ OUTSIDE-IN ACL

→ Static NAT

→ SRV-DMZ-WEB

```



The webpage opening successfully proves that the outside ACL and static NAT are working.



\---



\# 18. Failure-Test Verification



\## HSRP Failure



Failure action:



```text

interface Vlan10

&#x20;shutdown

```



Verification on DIST-SW2:



```text

show standby brief

```



Expected:



```text

Vlan10 Active local

```



Connectivity test:



```text

ping 10.10.10.1

ping 198.51.100.10

```



Restoration:



```text

interface Vlan10

&#x20;no shutdown

```



\## EtherChannel Member Failure



Failure action:



```text

interface FastEthernet0/23

&#x20;shutdown

```



Verification:



```text

show etherchannel summary

```



Expected:



```text

Po1(SU)    Fa0/23(D) Fa0/24(P)

```



Restoration:



```text

interface FastEthernet0/23

&#x20;no shutdown

```



Healthy state:



```text

Po1(SU)    Fa0/23(P) Fa0/24(P)

```



> Never save the configuration while a port is intentionally shut down for temporary failure testing.



\---



\# 19. Recommended Troubleshooting Order



When a connection fails, verify in this order:



1\. Physical cable and link status

2\. Interface administrative status

3\. IP address and subnet mask

4\. Default gateway

5\. VLAN assignment

6\. Trunk allowed VLANs

7\. STP port state

8\. HSRP gateway state

9\. Routing-table entry

10\. ACL permit or deny rules

11\. Firewall interface and routes

12\. NAT translation

13\. DHCP, DNS or HTTP service



This prevents random configuration changes and helps locate the exact failing layer.



