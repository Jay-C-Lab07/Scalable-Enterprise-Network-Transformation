\# ⚙️ Device Configuration Summary



\## 📌 Purpose



This document summarizes the major configuration applied to every network device in the \*\*Scalable Enterprise Network Transformation\*\* project.



It is intended as a configuration reference rather than a replacement for the Packet Tracer file.



> Administrative passwords are intentionally represented using placeholders. Never publish real infrastructure credentials in a public GitHub repository.



\---



\# 1. Common Security Configuration



The following configuration pattern was applied to SSH-managed Cisco IOS devices:



```text

ip domain-name entcorp.local

username netadmin privilege 15 secret <ADMIN\_SECRET>

enable secret <ENABLE\_SECRET>

crypto key generate rsa

ip ssh version 2



line vty 0 15

&#x20;login local

&#x20;transport input ssh

&#x20;exec-timeout 10 0

```



Router VTY lines used:



```text

line vty 0 4

```



Purpose:



\* Create a local privileged administrator

\* Generate RSA encryption keys

\* Enable SSH version 2

\* Disable Telnet by allowing SSH only

\* Disconnect inactive sessions after 10 minutes



\---



\# 2. R-ISP



\## Role



R-ISP simulates the internet service provider.



\## Interface Configuration



```text

hostname R-ISP



interface GigabitEthernet0/0

&#x20;description LINK-TO-FW-EDGE

&#x20;ip address 203.0.113.1 255.255.255.248

&#x20;no shutdown



interface GigabitEthernet0/1

&#x20;description LINK-TO-INTERNET-SERVER

&#x20;ip address 198.51.100.1 255.255.255.0

&#x20;no shutdown

```



\## Connected Networks



| Interface | Network           | Connected device |

| --------- | ----------------- | ---------------- |

| G0/0      | `203.0.113.0/29`  | FW-EDGE          |

| G0/1      | `198.51.100.0/24` | SRV-INTERNET     |



No additional route was needed for the DMZ public address because `203.0.113.3` belongs to the directly connected `/29` network.



\---



\# 3. FW-EDGE



\## Role



The Cisco ASA separates the enterprise into:



\* Inside

\* Outside

\* DMZ



\## Physical Switch Ports



```text

interface Ethernet0/0

&#x20;switchport access vlan 2

&#x20;no shutdown



interface Ethernet0/1

&#x20;switchport access vlan 1

&#x20;no shutdown



interface Ethernet0/2

&#x20;switchport access vlan 3

&#x20;no shutdown

```



\## Inside Interface



```text

interface Vlan1

&#x20;nameif inside

&#x20;security-level 100

&#x20;ip address 10.255.2.2 255.255.255.252

&#x20;no shutdown

```



\## Outside Interface



```text

interface Vlan2

&#x20;nameif outside

&#x20;security-level 0

&#x20;ip address 203.0.113.2 255.255.255.248

&#x20;no shutdown

```



\## DMZ Interface



```text

interface Vlan3

&#x20;no forward interface Vlan1

&#x20;nameif dmz

&#x20;security-level 50

&#x20;ip address 172.16.10.1 255.255.255.0

&#x20;no shutdown

```



The `no forward interface Vlan1` command was required by the Packet Tracer ASA 5505 third-VLAN licence restriction.



It also prevents the DMZ from directly initiating traffic toward the Inside network.



\## Static Routes



```text

route outside 0.0.0.0 0.0.0.0 203.0.113.1

route inside 10.0.0.0 255.0.0.0 10.255.2.1

```



\## Dynamic PAT



```text

object network ENTERPRISE-INTERNAL

&#x20;subnet 10.0.0.0 255.0.0.0

&#x20;nat (inside,outside) dynamic interface

```



All internal `10.x.x.x` addresses share the ASA Outside address `203.0.113.2`.



\## Static NAT



```text

object network DMZ-WEB-PUBLIC

&#x20;host 172.16.10.10

&#x20;nat (dmz,outside) static 203.0.113.3

```



This publishes the DMZ server using public address `203.0.113.3`.



\## Outside ACL



```text

access-list OUTSIDE-IN extended permit tcp any host 172.16.10.10 eq 80

access-list OUTSIDE-IN extended permit tcp any host 172.16.10.10 eq 443

access-group OUTSIDE-IN in interface outside

```



Only HTTP and HTTPS traffic is permitted from the simulated internet to the DMZ web server.



\## ICMP Inspection



```text

class-map inspection\_default

&#x20;match default-inspection-traffic



policy-map global\_policy

&#x20;class inspection\_default

&#x20; inspect icmp



service-policy global\_policy global

```



\---



\# 4. R-HQ



\## Role



R-HQ connects:



\* The two headquarters distribution switches

\* The branch WAN

\* The ASA firewall

\* OSPF and EIGRP routing domains



\## Interfaces



```text

hostname R-HQ



interface GigabitEthernet0/0

&#x20;description LINK-TO-FW-EDGE

&#x20;ip address 10.255.2.1 255.255.255.252

&#x20;no shutdown



interface GigabitEthernet0/1

&#x20;description L3-LINK-TO-DIST-SW1

&#x20;ip address 10.255.10.1 255.255.255.252

&#x20;no shutdown



interface GigabitEthernet0/2

&#x20;description L3-LINK-TO-DIST-SW2

&#x20;ip address 10.255.10.5 255.255.255.252

&#x20;no shutdown



interface Serial0/3/0

&#x20;description WAN-TO-R-BRANCH

&#x20;ip address 10.255.1.1 255.255.255.252

&#x20;no shutdown

```



\## Default Route



```text

ip route 0.0.0.0 0.0.0.0 10.255.2.2

```



Unknown traffic is sent to the ASA Inside interface.



\## OSPF



```text

router ospf 1

&#x20;router-id 1.1.1.1

&#x20;passive-interface default

&#x20;no passive-interface GigabitEthernet0/1

&#x20;no passive-interface GigabitEthernet0/2

&#x20;network 10.255.10.0 0.0.0.3 area 0

&#x20;network 10.255.10.4 0.0.0.3 area 0

&#x20;network 10.255.1.0 0.0.0.3 area 0

&#x20;default-information originate

&#x20;redistribute eigrp 100 subnets

```



\## EIGRP



```text

router eigrp 100

&#x20;no auto-summary

&#x20;passive-interface default

&#x20;no passive-interface Serial0/3/0

&#x20;network 10.255.1.0 0.0.0.3

&#x20;redistribute ospf 1 metric 100000 100 255 1 1500

&#x20;redistribute static metric 100000 100 255 1 1500

```



\## Routing Relationship



\* OSPF operates toward DIST-SW1 and DIST-SW2.

\* EIGRP operates toward R-BRANCH.

\* R-HQ redistributes routes between the two protocols.

\* The static default route is redistributed to the branch.



\---



\# 5. R-BRANCH



\## Role



R-BRANCH provides:



\* Branch router-on-a-stick routing

\* Branch DHCP relay

\* Branch-to-HQ EIGRP routing

\* Branch WAN termination



\## Physical Trunk



```text

hostname R-BRANCH



interface GigabitEthernet0/0

&#x20;description TRUNK-TO-BRANCH-SW

&#x20;no ip address

&#x20;no shutdown

```



\## Branch Users



```text

interface GigabitEthernet0/0.110

&#x20;description BRANCH-USERS-GATEWAY

&#x20;encapsulation dot1Q 110

&#x20;ip address 10.20.10.1 255.255.255.0

&#x20;ip helper-address 10.10.60.10

```



\## Branch Voice



```text

interface GigabitEthernet0/0.120

&#x20;description BRANCH-VOICE-GATEWAY

&#x20;encapsulation dot1Q 120

&#x20;ip address 10.20.20.1 255.255.255.0

&#x20;ip helper-address 10.10.60.10

```



\## Branch Management



```text

interface GigabitEthernet0/0.199

&#x20;description BRANCH-MANAGEMENT-GATEWAY

&#x20;encapsulation dot1Q 199

&#x20;ip address 10.20.99.1 255.255.255.0

```



\## Native VLAN



```text

interface GigabitEthernet0/0.999

&#x20;description NATIVE-BLACKHOLE

&#x20;encapsulation dot1Q 999 native

```



\## WAN Interface



```text

interface Serial0/3/0

&#x20;description WAN-TO-R-HQ

&#x20;ip address 10.255.1.2 255.255.255.252

&#x20;clock rate 64000

&#x20;no shutdown

```



The clock rate was applied because R-BRANCH held the DCE end of the serial cable.



\## EIGRP



```text

router eigrp 100

&#x20;no auto-summary

&#x20;passive-interface default

&#x20;no passive-interface Serial0/3/0

&#x20;network 10.255.1.0 0.0.0.3

&#x20;network 10.20.10.0 0.0.0.255

&#x20;network 10.20.20.0 0.0.0.255

&#x20;network 10.20.99.0 0.0.0.255

```



\---



\# 6. DIST-SW1



\## Role



DIST-SW1 provides:



\* VTP server operation

\* Inter-VLAN routing

\* HSRP gateway redundancy

\* OSPF routing

\* Rapid-PVST+ root-bridge functions

\* LACP EtherChannel

\* Guest ACL enforcement

\* SSH management



\## Global Configuration



```text

hostname DIST-SW1

no ip domain-lookup

ip routing

spanning-tree mode rapid-pvst

```



\## VTP



```text

vtp domain ENT-CORP

vtp password <VTP\_SECRET>

vtp version 2

vtp mode server

```



\## Routed Uplink



```text

interface GigabitEthernet0/1

&#x20;description L3-LINK-TO-R-HQ

&#x20;no switchport

&#x20;ip address 10.255.10.2 255.255.255.252

&#x20;no shutdown

```



\## Access-Switch Trunks



```text

interface range FastEthernet0/1 - 4

&#x20;switchport mode trunk

&#x20;switchport trunk native vlan 999

&#x20;switchport trunk allowed vlan 10,20,30,40,50,60,70,80,90,99,999

&#x20;no shutdown

```



\## EtherChannel Members



```text

interface range FastEthernet0/23 - 24

&#x20;switchport mode trunk

&#x20;switchport trunk native vlan 999

&#x20;switchport trunk allowed vlan 10,20,30,40,50,60,70,80,90,99,999

&#x20;channel-group 1 mode active

&#x20;no shutdown

```



\## Port-Channel



```text

interface Port-channel1

&#x20;switchport mode trunk

&#x20;switchport trunk native vlan 999

&#x20;switchport trunk allowed vlan 10,20,30,40,50,60,70,80,90,99,999

```



\## OSPF



```text

router ospf 1

&#x20;router-id 2.2.2.2

&#x20;passive-interface default

&#x20;no passive-interface GigabitEthernet0/1

&#x20;network 10.10.0.0 0.0.255.255 area 0

&#x20;network 10.255.10.0 0.0.0.3 area 0

```



\## STP Preference



DIST-SW1 is the preferred STP root for:



```text

VLANs 10,30,50,70,90,99,999

```



It is the secondary root for:



```text

VLANs 20,40,60,80

```



\---



\# 7. DIST-SW2



\## Role



DIST-SW2 provides redundant Layer 3 gateway, routing, switching and STP services.



\## Global Configuration



```text

hostname DIST-SW2

no ip domain-lookup

ip routing

spanning-tree mode rapid-pvst

```



\## VTP



```text

vtp domain ENT-CORP

vtp password <VTP\_SECRET>

vtp version 2

vtp mode client

```



\## Routed Uplink



```text

interface GigabitEthernet0/1

&#x20;description L3-LINK-TO-R-HQ

&#x20;no switchport

&#x20;ip address 10.255.10.6 255.255.255.252

&#x20;no shutdown

```



\## Access-Switch Trunks



```text

interface range FastEthernet0/1 - 4

&#x20;switchport mode trunk

&#x20;switchport trunk native vlan 999

&#x20;switchport trunk allowed vlan 10,20,30,40,50,60,70,80,90,99,999

&#x20;no shutdown

```



\## EtherChannel



```text

interface range FastEthernet0/23 - 24

&#x20;switchport mode trunk

&#x20;switchport trunk native vlan 999

&#x20;switchport trunk allowed vlan 10,20,30,40,50,60,70,80,90,99,999

&#x20;channel-group 1 mode active

&#x20;no shutdown



interface Port-channel1

&#x20;switchport mode trunk

&#x20;switchport trunk native vlan 999

&#x20;switchport trunk allowed vlan 10,20,30,40,50,60,70,80,90,99,999

```



\## OSPF



```text

router ospf 1

&#x20;router-id 3.3.3.3

&#x20;passive-interface default

&#x20;no passive-interface GigabitEthernet0/1

&#x20;network 10.10.0.0 0.0.255.255 area 0

&#x20;network 10.255.10.4 0.0.0.3 area 0

```



\## STP Preference



DIST-SW2 is the preferred STP root for:



```text

VLANs 20,40,60,80

```



It is the secondary root for the remaining headquarters VLANs.



\---



\# 8. Headquarters SVI and HSRP Pattern



The following address pattern is used:



| Address | Purpose              |

| ------- | -------------------- |

| `.1`    | HSRP virtual gateway |

| `.2`    | DIST-SW1             |

| `.3`    | DIST-SW2             |



\## Example: VLAN 10 on DIST-SW1



```text

interface Vlan10

&#x20;description SALES-GATEWAY

&#x20;ip address 10.10.10.2 255.255.255.0

&#x20;ip helper-address 10.10.60.10

&#x20;standby 10 ip 10.10.10.1

&#x20;standby 10 priority 110

&#x20;standby 10 preempt

&#x20;standby 10 track GigabitEthernet0/1 20

&#x20;no shutdown

```



\## Example: VLAN 10 on DIST-SW2



```text

interface Vlan10

&#x20;description SALES-GATEWAY

&#x20;ip address 10.10.10.3 255.255.255.0

&#x20;ip helper-address 10.10.60.10

&#x20;standby 10 ip 10.10.10.1

&#x20;standby 10 priority 100

&#x20;standby 10 preempt

&#x20;standby 10 track GigabitEthernet0/1 20

&#x20;no shutdown

```



The same pattern is repeated for VLANs:



```text

10,20,30,40,50,60,70,80,90,99

```



HSRP priority 110 determines the preferred Active switch.



\---



\# 9. Guest Wi-Fi ACL



The following numbered extended ACL was configured on both distribution switches:



```text

access-list 190 permit udp any eq 68 any eq 67

access-list 190 permit udp 10.10.90.0 0.0.0.255 host 10.10.60.10 eq 53

access-list 190 permit tcp 10.10.90.0 0.0.0.255 host 10.10.60.10 eq 53

access-list 190 deny ip 10.10.90.0 0.0.0.255 10.0.0.0 0.255.255.255

access-list 190 permit ip 10.10.90.0 0.0.0.255 any

```



Application:



```text

interface Vlan90

&#x20;ip access-group 190 in

```



The ACL:



1\. Permits DHCP

2\. Permits DNS

3\. Blocks internal enterprise networks

4\. Permits other destinations, including the internet



\---



\# 10. FLOOR1-SW



\## Role



Connects Sales users, HQ-PHONE1 and Guest Wi-Fi.



\## Trunks



```text

interface range GigabitEthernet0/1 - 2

&#x20;switchport mode trunk

&#x20;switchport trunk native vlan 999

&#x20;switchport trunk allowed vlan 10,20,30,40,50,60,70,80,90,99,999

&#x20;no shutdown

```



\## Sales Ports



```text

interface range FastEthernet0/1 - 2

&#x20;switchport mode access

&#x20;switchport access vlan 10

&#x20;spanning-tree portfast

```



\## Phone Port



```text

interface FastEthernet0/3

&#x20;switchport mode access

&#x20;switchport access vlan 10

&#x20;switchport voice vlan 70

&#x20;spanning-tree portfast

```



\## Guest Access Point



```text

interface FastEthernet0/4

&#x20;switchport mode access

&#x20;switchport access vlan 90

&#x20;spanning-tree portfast

```



\## Management



```text

interface Vlan99

&#x20;description NETWORK-MANAGEMENT

&#x20;ip address 10.10.99.11 255.255.255.0

&#x20;no shutdown



ip default-gateway 10.10.99.1

```



\## Unused Ports



```text

interface range FastEthernet0/5 - 24

&#x20;description UNUSED-SHUTDOWN

&#x20;switchport mode access

&#x20;switchport access vlan 999

&#x20;shutdown

```



\---



\# 11. FLOOR2-SW



\## Role



Connects HR, Finance and HQ-PHONE2.



```text

interface range FastEthernet0/1 - 2

&#x20;switchport mode access

&#x20;switchport access vlan 20

&#x20;spanning-tree portfast



interface range FastEthernet0/3 - 4

&#x20;switchport mode access

&#x20;switchport access vlan 30

&#x20;spanning-tree portfast



interface FastEthernet0/5

&#x20;switchport mode access

&#x20;switchport access vlan 20

&#x20;switchport voice vlan 70

&#x20;spanning-tree portfast

```



Management:



```text

interface Vlan99

&#x20;ip address 10.10.99.12 255.255.255.0

&#x20;no shutdown



ip default-gateway 10.10.99.1

```



Unused ports Fa0/6–24 were assigned to VLAN 999 and shut down.



\---



\# 12. FLOOR3-SW



\## Role



Connects IT, Management and Corporate Wi-Fi.



```text

interface range FastEthernet0/1 - 2

&#x20;switchport mode access

&#x20;switchport access vlan 40

&#x20;spanning-tree portfast



interface range FastEthernet0/3 - 4

&#x20;switchport mode access

&#x20;switchport access vlan 50

&#x20;spanning-tree portfast



interface FastEthernet0/5

&#x20;switchport mode access

&#x20;switchport access vlan 80

&#x20;spanning-tree portfast

```



Management:



```text

interface Vlan99

&#x20;ip address 10.10.99.13 255.255.255.0

&#x20;no shutdown



ip default-gateway 10.10.99.1

```



Unused ports Fa0/6–24 were assigned to VLAN 999 and shut down.



\---



\# 13. DC-SW



\## Role



Connects the internal DHCP/DNS and web servers.



```text

interface range FastEthernet0/1 - 2

&#x20;description DATA-CENTER-SERVERS

&#x20;switchport mode access

&#x20;switchport access vlan 60

&#x20;spanning-tree portfast

&#x20;no shutdown

```



Management:



```text

interface Vlan99

&#x20;ip address 10.10.99.14 255.255.255.0

&#x20;no shutdown



ip default-gateway 10.10.99.1

```



Unused ports Fa0/3–24 were assigned to VLAN 999 and shut down.



\---



\# 14. BRANCH-SW



\## Role



Connects branch users and the branch phone.



\## VLANs



```text

vlan 110

&#x20;name BRANCH-USERS



vlan 120

&#x20;name BRANCH-VOICE



vlan 199

&#x20;name BRANCH-MGMT



vlan 999

&#x20;name NATIVE-BLACKHOLE

```



\## Router Trunk



```text

interface GigabitEthernet0/1

&#x20;switchport mode trunk

&#x20;switchport trunk native vlan 999

&#x20;switchport trunk allowed vlan 110,120,199,999

&#x20;no shutdown

```



\## User Ports



```text

interface range FastEthernet0/1 - 2

&#x20;switchport mode access

&#x20;switchport access vlan 110

&#x20;spanning-tree portfast

```



\## Phone Port



```text

interface FastEthernet0/3

&#x20;switchport mode access

&#x20;switchport access vlan 110

&#x20;switchport voice vlan 120

&#x20;spanning-tree portfast

```



\## Management



```text

interface Vlan199

&#x20;ip address 10.20.99.2 255.255.255.0

&#x20;no shutdown



ip default-gateway 10.20.99.1

```



Unused ports Fa0/4–24 were assigned to VLAN 999 and shut down.



\---



\# 15. Server Configuration



\## SRV-DHCP-DNS



```text

IP address:      10.10.60.10

Subnet mask:     255.255.255.0

Default gateway: 10.10.60.1

DNS server:      10.10.60.10

```



Services:



\* DHCP: On

\* DNS: On



DNS record:



```text

intranet.entcorp.com → 10.10.60.20

```



\## SRV-INTERNAL-WEB



```text

IP address:      10.10.60.20

Subnet mask:     255.255.255.0

Default gateway: 10.10.60.1

DNS server:      10.10.60.10

```



Services:



\* HTTP: On

\* HTTPS: On



\## SRV-DMZ-WEB



```text

IP address:      172.16.10.10

Subnet mask:     255.255.255.0

Default gateway: 172.16.10.1

DNS server:      10.10.60.10

```



Services:



\* HTTP: On

\* HTTPS: On



\## SRV-INTERNET



```text

IP address:      198.51.100.10

Subnet mask:     255.255.255.0

Default gateway: 198.51.100.1

```



\---



\# 16. Wireless Configuration



\## AP-HQ-CORP



```text

SSID:           ENT-CORP

VLAN:           80

Authentication: WPA2-PSK

Encryption:     AES

```



\## AP-HQ-GUEST



```text

SSID:           ENT-GUEST

VLAN:           90

Authentication: WPA2-PSK

Encryption:     AES

```



Wireless passphrases are intentionally excluded from this public document.



\---



\# 17. Configuration-Saving Commands



Cisco IOS devices:



```text

end

write memory

```



Cisco ASA:



```text

write memory

```



Verification:



```text

show running-config

show startup-config

```



\---



\## Final Note



This document is a structured summary of the important configuration used in the project.



The complete implemented configuration, including Packet Tracer–specific behaviour, can be inspected inside:



```text

Enterprise-Network.pkt

```



