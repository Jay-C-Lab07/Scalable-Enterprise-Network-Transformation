\# 🎓 Enterprise Network Project — Interview Preparation



\## 📌 Purpose of This Document



This document explains the Scalable Enterprise Network Transformation project in simple, interview-ready language.



Use it to understand:



\* What the project is

\* What business problem it solves

\* How the network works

\* Why each technology was used

\* How packets move through the network

\* What problems were encountered

\* How to confidently explain the project in an interview

\* Common networking interview questions and answers



\---



\# 1. Project Explanation in One Sentence



I designed and implemented a secure, scalable, and fault-tolerant enterprise network in Cisco Packet Tracer that connects a three-floor headquarters, data center, branch office, DMZ, and simulated internet using VLANs, routing protocols, redundancy, firewall security, NAT, ACLs, wireless networks, DHCP, DNS, and SSH management.



\---



\# 2. Simple Explanation



Imagine that a company has:



\* Several departments

\* Three office floors

\* A data center

\* A branch office

\* Corporate and guest Wi-Fi

\* IP phones

\* Internal servers

\* A public website

\* Internet access



If every device were placed in one large network, the network would become difficult to manage, insecure, and unreliable.



This project divides the company into smaller networks called VLANs. Routers and Layer 3 switches connect those VLANs. Two distribution switches provide gateway redundancy. Dynamic routing protocols exchange routes automatically. A firewall protects the company from the internet. DHCP automatically provides IP addresses, while DNS translates names into IP addresses.



The design also continues operating when selected links or gateway devices fail.



\---



\# 3. Problem Statement



A growing company requires a network that can support multiple departments, floors, servers, wireless users, IP phones, a branch office, and internet-facing services.



A basic flat network creates several problems:



\* All users share one broadcast domain.

\* Department traffic is not separated.

\* Guest users may access private company resources.

\* Manual IP addressing wastes time and causes mistakes.

\* Failure of one gateway or link can interrupt the network.

\* A branch office may not know routes to HQ networks.

\* Internal addresses cannot directly access the public internet.

\* Public servers placed inside the private network create security risks.

\* Telnet and console-only administration are insecure or inconvenient.

\* Troubleshooting becomes difficult as the organization grows.



The project was designed to solve these problems using an enterprise-style hierarchical and segmented network.



\---



\# 4. Solution



The solution includes:



\* VLANs to separate departments

\* Trunks to transport multiple VLANs

\* Rapid PVST+ to prevent switching loops

\* LACP EtherChannel for bandwidth and redundancy

\* HSRP for redundant default gateways

\* Layer 3 switching for inter-VLAN routing

\* DHCP for automatic addressing

\* DHCP relay for centralized DHCP

\* DNS for name resolution

\* OSPF for dynamic HQ routing

\* EIGRP for branch routing

\* Route redistribution between OSPF and EIGRP

\* Static and default routes toward the firewall and internet

\* ASA firewall interfaces for inside, outside, and DMZ zones

\* PAT for internet access

\* Static NAT for publishing the DMZ website

\* ACLs for guest isolation and firewall security

\* WPA2-PSK wireless security

\* SSH version 2 for secure management

\* Voice VLANs for separating phone traffic

\* Failover testing for HSRP and EtherChannel



\---



\# 5. Main Technologies Used



| Technology             | Purpose                                                  |

| ---------------------- | -------------------------------------------------------- |

| Cisco Packet Tracer    | Network design and simulation                            |

| IPv4 and subnetting    | Provides logical device addressing                       |

| VLANs                  | Separates departments and traffic                        |

| 802.1Q trunking        | Carries multiple VLANs on one link                       |

| Rapid PVST+            | Prevents Layer 2 loops                                   |

| LACP EtherChannel      | Combines links for redundancy and bandwidth              |

| HSRP                   | Provides a redundant virtual default gateway             |

| Inter-VLAN routing     | Allows approved communication between VLANs              |

| DHCP                   | Automatically assigns client network settings            |

| DHCP relay             | Forwards DHCP requests to a remote server                |

| DNS                    | Converts domain names into IP addresses                  |

| OSPF                   | Dynamically exchanges HQ routes                          |

| EIGRP                  | Dynamically exchanges HQ–branch routes                   |

| Route redistribution   | Connects different routing domains                       |

| Static/default routing | Directs unknown traffic toward the firewall              |

| Cisco ASA              | Protects traffic between security zones                  |

| NAT/PAT                | Translates private and public IP addresses               |

| Extended ACLs          | Controls traffic using source, destination, and protocol |

| DMZ                    | Isolates public-facing servers                           |

| WPA2-PSK and AES       | Secures wireless access                                  |

| SSH version 2          | Provides encrypted remote administration                 |

| Voice VLANs            | Separates phone and data traffic                         |

| Git and GitHub         | Stores and presents project documentation                |



\---



\# 6. Network Design



\## Headquarters



The headquarters contains:



\* Two Layer 3 distribution switches

\* Three floor access switches

\* One data center switch

\* Department PCs

\* Corporate and guest wireless networks

\* IP phones

\* Internal DHCP, DNS, and web servers



The distribution switches perform:



\* Inter-VLAN routing

\* HSRP

\* OSPF

\* ACL enforcement

\* Redundant gateway services



\## Branch Office



The branch contains:



\* One branch router

\* One branch access switch

\* Branch users

\* A branch voice VLAN

\* A management VLAN



The branch router uses router-on-a-stick to route between branch VLANs.



\## Internet Edge



The edge contains:



\* R-HQ

\* ASA firewall

\* ISP router

\* Internet server

\* DMZ web server



The ASA separates the network into:



\* Inside

\* Outside

\* DMZ



\---



\# 7. VLAN Plan



\## Headquarters VLANs



| VLAN | Name             | Network          | Purpose                  |

| ---: | ---------------- | ---------------- | ------------------------ |

|   10 | SALES            | `10.10.10.0/24`  | Sales users              |

|   20 | HR               | `10.10.20.0/24`  | Human Resources          |

|   30 | FINANCE          | `10.10.30.0/24`  | Finance users            |

|   40 | IT               | `10.10.40.0/24`  | IT department            |

|   50 | MANAGEMENT       | `10.10.50.0/24`  | Management users         |

|   60 | SERVERS          | `10.10.60.0/24`  | Internal servers         |

|   70 | VOICE            | `10.10.70.0/24`  | HQ IP phones             |

|   80 | CORP-WIFI        | `10.10.80.0/24`  | Corporate wireless users |

|   90 | GUEST-WIFI       | `10.10.90.0/24`  | Guest wireless users     |

|   99 | NETWORK-MGMT     | `10.10.99.0/24`  | Device management        |

|  999 | NATIVE-BLACKHOLE | No client subnet | Unused/native traffic    |



\## Branch VLANs



| VLAN | Name             | Network          | Purpose                 |

| ---: | ---------------- | ---------------- | ----------------------- |

|  110 | BRANCH-USERS     | `10.20.10.0/24`  | Branch users            |

|  120 | BRANCH-VOICE     | `10.20.20.0/24`  | Branch IP phones        |

|  199 | BRANCH-MGMT      | `10.20.99.0/24`  | Branch management       |

|  999 | NATIVE-BLACKHOLE | No client subnet | Native and unused ports |



\---



\# 8. Why VLANs Were Used



A VLAN is a logical network created inside a switch.



Without VLANs, every device belongs to one large broadcast domain. A broadcast sent by one device reaches every other device in that network.



VLANs were used to:



\* Separate departments

\* Reduce broadcasts

\* Improve security

\* Simplify troubleshooting

\* Apply different policies

\* Organize IP addressing

\* Separate guest, corporate, server, management, and voice traffic



For example, a Sales PC belongs to VLAN 10, while the DHCP server belongs to VLAN 60. They are physically connected to switches but logically belong to different networks.



\---



\# 9. Access Ports and Trunk Ports



\## Access Port



An access port carries one normal data VLAN and usually connects to an endpoint.



Example:



```cisco

interface FastEthernet0/1

&#x20;switchport mode access

&#x20;switchport access vlan 10

```



This places the connected PC in VLAN 10.



\## Trunk Port



A trunk carries multiple VLANs between network devices.



Example:



```cisco

interface GigabitEthernet0/1

&#x20;switchport mode trunk

&#x20;switchport trunk native vlan 999

&#x20;switchport trunk allowed vlan 10,20,30,40,50,60,70,80,90,99,999

```



802.1Q adds a VLAN tag to Ethernet frames so the receiving switch knows which VLAN each frame belongs to.



\## Native VLAN



Frames in the native VLAN are normally sent untagged.



VLAN 999 was used instead of VLAN 1 to reduce the security risk of using the default VLAN for normal traffic.



\---



\# 10. Layer 2 Loop Prevention



Redundant connections are useful, but they can create switching loops.



A switching loop can produce:



\* Broadcast storms

\* Duplicate frames

\* Unstable MAC address tables

\* Network failure



Rapid PVST+ prevents loops by placing selected redundant ports into a blocking or alternate state.



If an active path fails, Rapid PVST+ can activate a backup path.



Important terms:



\* Root bridge: central reference switch for the spanning tree

\* Root port: best path toward the root bridge

\* Designated port: forwarding port for a network segment

\* Alternate port: backup port that is temporarily blocked

\* BPDU: control message used by switches to build the tree



\---



\# 11. EtherChannel and LACP



Two physical links connected the distribution switches:



```text

DIST-SW1 Fa0/23 ↔ DIST-SW2 Fa0/23

DIST-SW1 Fa0/24 ↔ DIST-SW2 Fa0/24

```



LACP combined them into one logical interface:



```text

Port-channel1

```



Example:



```cisco

interface range FastEthernet0/23-24

&#x20;channel-group 1 mode active

```



Benefits:



\* More total bandwidth

\* Link redundancy

\* Spanning Tree sees one logical connection

\* Traffic can continue if one member link fails



Verification:



```cisco

show etherchannel summary

```



`Po1(SU)` means:



\* `S`: Layer 2 port channel

\* `U`: Port channel is in use



`P` beside a member means it is bundled in the port channel.



\---



\# 12. Inter-VLAN Routing



Devices in different VLANs belong to different IP networks. A Layer 3 device is required to route traffic between them.



At HQ, the distribution switches use Switch Virtual Interfaces.



Example:



```cisco

interface Vlan10

&#x20;ip address 10.10.10.2 255.255.255.0

&#x20;no shutdown

```



`ip routing` enables Layer 3 packet forwarding:



```cisco

ip routing

```



At the branch, router-on-a-stick was used.



Example:



```cisco

interface GigabitEthernet0/0.110

&#x20;encapsulation dot1Q 110

&#x20;ip address 10.20.10.1 255.255.255.0

```



One physical router interface carries multiple VLANs using subinterfaces.



\---



\# 13. HSRP Gateway Redundancy



Clients need a default gateway to reach other networks.



If only one physical switch acted as the gateway, its failure would disconnect the VLAN. HSRP solves this by creating a virtual gateway shared by two switches.



For VLAN 10:



```text

Virtual gateway: 10.10.10.1

DIST-SW1:        10.10.10.2

DIST-SW2:        10.10.10.3

```



Clients always use `10.10.10.1`.



Example configuration:



```cisco

standby 10 ip 10.10.10.1

standby 10 priority 110

standby 10 preempt

```



Meaning:



\* `standby 10`: HSRP group number

\* `ip 10.10.10.1`: virtual gateway

\* `priority 110`: higher priority is preferred

\* `preempt`: allows the higher-priority switch to regain active status



Verification:



```cisco

show standby brief

```



The project load-balanced VLAN gateway roles:



\* DIST-SW1 was preferred for selected VLANs.

\* DIST-SW2 was preferred for the remaining VLANs.



\---



\# 14. DHCP and DHCP Relay



DHCP automatically provides:



\* IP address

\* Subnet mask

\* Default gateway

\* DNS server



The DHCP process is commonly remembered as DORA:



1\. Discover

2\. Offer

3\. Request

4\. Acknowledge



The central DHCP server was `10.10.60.10`.



Because DHCP Discover is a broadcast, routers do not forward it automatically. Therefore, each remote SVI or subinterface used:



```cisco

ip helper-address 10.10.60.10

```



The helper converts and forwards the client DHCP request toward the centralized server.



\---



\# 15. DNS



DNS translates names into IP addresses.



Example:



```text

intranet.entcorp.com → 10.10.60.20

```



Without DNS, users would need to remember the server's IP address.



Verification:



```text

nslookup intranet.entcorp.com

```



DNS provides the name-to-address mapping. Routing still provides the path used to reach that address.



\---



\# 16. OSPF



OSPF was used inside the HQ network.



OSPF is a link-state dynamic routing protocol. It builds a view of the network and calculates the shortest path using cost.



The project formed OSPF adjacencies between `R-HQ` and the two distribution switches.



Verification:



```cisco

show ip ospf neighbor

show ip route ospf

```



A neighbor state of `FULL` means the routers successfully exchanged OSPF information.



Routes beginning with `O` were learned through OSPF.



\---



\# 17. EIGRP



EIGRP autonomous system 100 connected the branch router to `R-HQ`.



Verification:



```cisco

show ip eigrp neighbors

show ip route eigrp

```



Important route codes:



\* `D`: internal EIGRP route

\* `D EX`: external route redistributed into EIGRP



The branch learned the HQ networks through `R-HQ`, while HQ learned the branch VLAN networks from `R-BRANCH`.



\---



\# 18. Route Redistribution



OSPF and EIGRP are separate routing protocols. They do not automatically share routes.



`R-HQ` acted as the redistribution point.



EIGRP into OSPF:



```cisco

router ospf 1

&#x20;redistribute eigrp 100 subnets

```



OSPF into EIGRP:



```cisco

router eigrp 100

&#x20;redistribute ospf 1 metric 100000 100 255 1 1500

```



EIGRP requires a seed metric when importing routes.



The five metric values represent:



1\. Bandwidth

2\. Delay

3\. Reliability

4\. Load

5\. MTU



Redistribution allowed the branch to reach HQ networks and allowed HQ to return traffic to branch networks.



\---



\# 19. Default Routing



A default route is used when a router does not have a more specific route.



On `R-HQ`:



```cisco

ip route 0.0.0.0 0.0.0.0 10.255.2.2

```



This means:



> Send unknown destinations to the ASA firewall at `10.255.2.2`.



The default route was advertised into OSPF:



```cisco

router ospf 1

&#x20;default-information originate

```



It was redistributed into EIGRP:



```cisco

router eigrp 100

&#x20;redistribute static metric 100000 100 255 1 1500

```



Therefore, HQ and branch networks learned how to reach the internet without requiring separate static default routes on every device.



\---



\# 20. ASA Firewall Security Zones



The firewall used three security zones.



| Zone    |     Interface IP | Security level | Purpose                      |

| ------- | ---------------: | -------------: | ---------------------------- |

| Inside  |  `10.255.2.2/30` |            100 | Trusted internal network     |

| DMZ     | `172.16.10.1/24` |             50 | Public-facing server network |

| Outside | `203.0.113.2/29` |              0 | Simulated internet           |



Higher security levels are more trusted.



The DMZ prevents the public web server from being placed directly inside the internal company network.



\---



\# 21. NAT and PAT



Private IP addresses such as `10.10.10.100` are not used on the public internet.



PAT allowed many internal devices to share the ASA outside IP:



```cisco

object network ENTERPRISE-INTERNAL

&#x20;subnet 10.0.0.0 255.0.0.0

&#x20;nat (inside,outside) dynamic interface

```



PAT differentiates connections using port numbers or ICMP identifiers.



Verification:



```cisco

show nat

show xlate

```



Example translation:



```text

Inside local:  10.10.10.100

Outside global: 203.0.113.2

```



\---



\# 22. Static NAT and Public DMZ Website



The private DMZ web server used:



```text

172.16.10.10

```



It was mapped to public address:



```text

203.0.113.3

```



Configuration:



```cisco

object network DMZ-WEB-PUBLIC

&#x20;host 172.16.10.10

&#x20;nat (dmz,outside) static 203.0.113.3

```



External HTTP and HTTPS access was permitted:



```cisco

access-list OUTSIDE-IN extended permit tcp any host 172.16.10.10 eq 80

access-list OUTSIDE-IN extended permit tcp any host 172.16.10.10 eq 443

access-group OUTSIDE-IN in interface outside

```



External users accessed:



```text

http://203.0.113.3

```



\---



\# 23. Guest Wi-Fi Security



The guest network needed:



\* DHCP

\* DNS

\* Internet access

\* No access to private company networks



The ACL allowed DHCP and DNS, denied access to `10.0.0.0/8`, and then permitted other destinations.



```cisco

access-list 190 permit udp any eq bootpc any eq bootps

access-list 190 permit udp 10.10.90.0 0.0.0.255 host 10.10.60.10 eq domain

access-list 190 permit tcp 10.10.90.0 0.0.0.255 host 10.10.60.10 eq domain

access-list 190 deny ip 10.10.90.0 0.0.0.255 10.0.0.0 0.255.255.255

access-list 190 permit ip 10.10.90.0 0.0.0.255 any

```



This follows the rule:



> Permit required services first, deny private access second, and permit internet traffic last.



\---



\# 24. SSH Management



SSH provides encrypted remote device administration.



Main configuration:



```cisco

ip domain-name entcorp.local

username netadmin privilege 15 secret NetAdmin@2026

crypto key generate rsa

ip ssh version 2

line vty 0 15

&#x20;login local

&#x20;transport input ssh

&#x20;exec-timeout 10 0

```



Connection example:



```text

ssh -l netadmin 10.10.99.2

```



SSH was preferred over Telnet because Telnet sends information in clear text.



\---



\# 25. Voice VLANs



An IP phone port can carry two types of traffic:



\* PC data traffic

\* Phone voice traffic



HQ example:



```cisco

switchport access vlan 10

switchport voice vlan 70

```



Branch example:



```cisco

switchport access vlan 110

switchport voice vlan 120

```



Voice and data separation improves:



\* Security

\* Traffic management

\* Troubleshooting

\* Future Quality of Service configuration



The selected Packet Tracer router IOS did not support CME commands, so the project verified voice VLAN separation rather than phone call registration.



\---



\# 26. How a Packet Travels from a Branch PC to the Internet



Suppose `BR-PC1` sends traffic to `198.51.100.10`.



1\. BR-PC1 sees that the destination is outside `10.20.10.0/24`.

2\. It sends the frame to gateway `10.20.10.1`.

3\. BRANCH-SW forwards it through the VLAN 110 trunk.

4\. R-BRANCH receives it on subinterface G0/0.110.

5\. R-BRANCH uses its EIGRP default route.

6\. The packet crosses Serial0/3/0 to R-HQ.

7\. R-HQ uses its static default route to the ASA.

8\. The ASA translates the private source address using PAT.

9\. The ASA sends it to R-ISP through the outside interface.

10\. R-ISP forwards it to the internet server.

11\. The reply returns to the ASA.

12\. The ASA uses its translation table to restore the private destination.

13\. Routing sends the reply back through R-HQ and R-BRANCH.

14\. BR-PC1 receives the reply.



Every component has a separate responsibility:



\* Switches forward local Ethernet frames.

\* Routers select paths between IP networks.

\* Routing protocols teach routers about networks.

\* The firewall enforces security.

\* PAT translates private addresses.

\* The ISP represents the public network.



\---



\# 27. Major Problems Solved During the Project



| Problem                                 | Cause                            | Solution                                      |

| --------------------------------------- | -------------------------------- | --------------------------------------------- |

| Branch WAN failure                      | Duplicate or incorrect serial IP | Corrected HQ to `.1` and branch to `.2`       |

| Branch could see routes but ping failed | Missing return routes            | Configured bidirectional redistribution       |

| Branch DHCP failed                      | Wrong pool start address         | Changed it to `10.20.10.100`                  |

| Guest received APIPA                    | DHCP blocked by ACL              | Permitted UDP ports 68 to 67 on both switches |

| DNS name failed                         | Record on wrong server           | Added record to `10.10.60.10`                 |

| DMZ ping failed                         | Gateway typed as `176`           | Corrected it to `172.16.10.1`                 |

| DMZ nameif problem                      | ASA base-license restriction     | Used `no forward interface Vlan1`             |

| ASA commands failed                     | Commands entered in wrong mode   | Entered global configuration mode             |

| Service PAT failed                      | Unsupported Packet Tracer syntax | Used `/29` and one-to-one static NAT          |

| First ping failed                       | ARP or convergence               | Waited and repeated the ping                  |

| HSRP tracking test unclear              | Simulator limitation             | Shut the VLAN SVI to test failover            |

| CME commands failed                     | IOS feature unsupported          | Verified voice VLAN configuration             |

| Wireless connection failed              | Wireless module missing          | Installed the correct laptop module           |



\---



\# 28. How to Explain the Project in an Interview



\## 30-Second Answer



I built a multi-site enterprise network in Cisco Packet Tracer connecting a three-floor headquarters, data center, branch office, DMZ, and simulated internet. I segmented departments using VLANs, implemented inter-VLAN routing, HSRP, Rapid PVST+, and LACP EtherChannel for redundancy, and used OSPF and EIGRP with redistribution for dynamic routing. I also configured centralized DHCP and DNS, an ASA firewall with PAT and static NAT, guest Wi-Fi isolation using ACLs, SSH management, and tested HSRP and EtherChannel failures.



\## One-Minute Answer



I designed the network to solve the problems of a flat, insecure, and non-redundant company network. Each department was assigned a separate VLAN and subnet. Two Layer 3 distribution switches provided inter-VLAN routing and HSRP virtual gateways. LACP EtherChannel and Rapid PVST+ provided Layer 2 redundancy. OSPF handled HQ routing, EIGRP connected the branch, and R-HQ redistributed routes between them.



A centralized server provided DHCP and DNS through DHCP relay. An ASA firewall separated inside, outside, and DMZ zones. PAT provided internet access for private clients, while static NAT published the DMZ website. ACLs prevented guest Wi-Fi from accessing private company networks while still allowing DNS and internet access. I also configured SSH management and performed failure tests to prove that gateway and link redundancy worked.



\## Strong Troubleshooting Answer



The most useful part of the project was troubleshooting real configuration problems. For example, the guest laptop received an APIPA address because its DHCP request used source IP `0.0.0.0`, but the ACL did not permit UDP ports 68 to 67. I corrected the ACL on both distribution switches because either switch could become the HSRP active gateway. I then verified that the laptop received a valid VLAN 90 address, could resolve DNS and reach the internet, but could not access internal networks.



\---



\# 29. Interview Questions and Answers



\## Q1. What is a VLAN?



A VLAN is a logical separation inside a switch. It creates a separate broadcast domain even when devices use the same physical switching infrastructure.



\## Q2. Why did you use VLANs?



I used VLANs to separate departments, reduce broadcasts, improve security, organize addressing, and apply different policies to users, servers, guests, phones, and management devices.



\## Q3. What is the difference between an access port and a trunk port?



An access port normally carries one VLAN and connects to an endpoint. A trunk carries multiple VLANs and normally connects switches, routers, firewalls, or access points.



\## Q4. What is 802.1Q?



802.1Q is the Ethernet VLAN-tagging standard used on trunk links. It inserts VLAN information into frames.



\## Q5. What is the native VLAN?



The native VLAN carries untagged traffic on an 802.1Q trunk. Both ends of the trunk must use the same native VLAN.



\## Q6. Why did you use VLAN 999?



It was used as an unused native or blackhole VLAN so normal user traffic would not use the default VLAN 1.



\## Q7. What is a broadcast domain?



It is the group of devices that receive a Layer 2 broadcast. Each VLAN creates a separate broadcast domain.



\## Q8. What is inter-VLAN routing?



It is routing between different VLANs. A router or Layer 3 switch is required because each VLAN is a separate IP network.



\## Q9. What is an SVI?



An SVI is a logical Layer 3 interface for a VLAN on a multilayer switch. It can act as a VLAN gateway and participate in routing.



\## Q10. What is router-on-a-stick?



It is a design where one physical router interface is divided into subinterfaces. Each subinterface serves a different VLAN using 802.1Q tagging.



\## Q11. What is HSRP?



HSRP is a Cisco first-hop redundancy protocol. Two devices share a virtual IP address so clients retain gateway connectivity if the active gateway fails.



\## Q12. What do HSRP priority and preempt do?



The router with the higher priority is preferred as active. Preempt allows that device to take back the active role after recovering.



\## Q13. What is STP?



Spanning Tree Protocol prevents Layer 2 switching loops by blocking redundant paths and activating them if the main path fails.



\## Q14. Why are switching loops dangerous?



They can cause broadcast storms, duplicate frames, MAC-table instability, and complete network failure.



\## Q15. What is Rapid PVST+?



It is Cisco's rapid per-VLAN implementation of Spanning Tree. It builds a separate spanning-tree instance for each VLAN and converges faster than traditional STP.



\## Q16. What is EtherChannel?



EtherChannel combines multiple physical links into one logical link for additional bandwidth and redundancy.



\## Q17. What is LACP?



LACP is an open-standard protocol that dynamically negotiates EtherChannel membership.



\## Q18. What happens if one EtherChannel member fails?



The port channel can remain active through the surviving member. Traffic is redistributed across the available links.



\## Q19. What is DHCP?



DHCP automatically assigns IP addresses, subnet masks, gateways, DNS servers, and other settings to clients.



\## Q20. What is DHCP relay?



DHCP relay forwards DHCP messages between different subnets. Cisco uses `ip helper-address` to configure it.



\## Q21. What does APIPA mean?



APIPA is an automatic address in `169.254.0.0/16`. It normally indicates that the client could not contact a DHCP server.



\## Q22. What is DNS?



DNS translates human-readable names such as `intranet.entcorp.com` into IP addresses.



\## Q23. What is OSPF?



OSPF is a link-state interior gateway routing protocol. It uses cost and the shortest-path-first algorithm to select routes.



\## Q24. What is an OSPF adjacency?



It is a routing relationship between OSPF routers that allows them to exchange link-state information.



\## Q25. What does OSPF FULL state mean?



It means the neighboring routers have successfully synchronized their OSPF databases.



\## Q26. What is EIGRP?



EIGRP is an advanced distance-vector routing protocol commonly used on Cisco networks. It uses DUAL to calculate loop-free paths.



\## Q27. What is route redistribution?



Route redistribution imports routes learned by one routing protocol into another routing protocol.



\## Q28. What risk does redistribution introduce?



Incorrect redistribution can cause routing loops, poor route selection, or repeated route injection. It should be planned and controlled carefully.



\## Q29. Why did EIGRP redistribution require a metric?



EIGRP needs bandwidth, delay, reliability, load, and MTU values to calculate a route metric. Routes from another protocol do not automatically contain all those EIGRP values.



\## Q30. What is a default route?



A default route is used when no more specific route exists. It is represented by `0.0.0.0/0`.



\## Q31. What is the difference between static and dynamic routing?



Static routes are manually configured. Dynamic routes are learned automatically through protocols such as OSPF or EIGRP.



\## Q32. What is an ACL?



An ACL is an ordered list of permit and deny rules used to control network traffic.



\## Q33. Why does ACL order matter?



ACLs are processed from top to bottom. The first matching rule decides what happens to the packet.



\## Q34. What is the implicit deny?



Every ACL has an invisible `deny any` at the end. Traffic that does not match a permit rule is denied.



\## Q35. What is the difference between standard and extended ACLs?



A standard ACL mainly checks source IP addresses. An extended ACL can check source, destination, protocol, and port numbers.



\## Q36. Why was the guest ACL applied inbound?



Applying it inbound on the guest VLAN stops unwanted traffic close to its source before it travels through the network.



\## Q37. What is NAT?



NAT translates one IP address into another, commonly between private and public address spaces.



\## Q38. What is PAT?



PAT allows multiple private devices to share one public IP address by distinguishing their sessions using port numbers or identifiers.



\## Q39. What is static NAT?



Static NAT creates a permanent one-to-one mapping between a private and public IP address.



\## Q40. What is a DMZ?



A DMZ is a separate security zone for public-facing services. It reduces the risk of exposing the trusted internal network.



\## Q41. Why not place the public web server inside the internal network?



If the public server is compromised, an attacker would be closer to sensitive internal systems. A DMZ provides isolation.



\## Q42. What do ASA security levels mean?



A higher number represents a more trusted interface. In this project, inside used 100, DMZ used 50, and outside used 0.



\## Q43. What is SSH?



SSH is an encrypted protocol for remotely managing network devices.



\## Q44. Why is SSH better than Telnet?



SSH encrypts usernames, passwords, commands, and session traffic. Telnet sends them in clear text.



\## Q45. Why use a management VLAN?



It separates network administration traffic from ordinary user traffic, improving organization and security.



\## Q46. What is a voice VLAN?



A voice VLAN is a dedicated VLAN used for IP phone traffic, separate from normal data traffic.



\## Q47. Why might the first ping fail?



The device may first need to learn a MAC address through ARP, or routing, spanning tree, wireless, or HSRP may still be converging.



\## Q48. What is ARP?



ARP maps a local IPv4 address to a MAC address so an Ethernet frame can be delivered on the local network.



\## Q49. What is the difference between a MAC address and an IP address?



A MAC address identifies an interface at Layer 2 on the local network. An IP address provides logical Layer 3 addressing across networks.



\## Q50. How did you test redundancy?



I shut down the VLAN 10 SVI on the active HSRP switch and confirmed that DIST-SW2 became active. I also shut one EtherChannel member and verified that connectivity continued through the remaining link.



\---



\# 30. Questions About Troubleshooting



\## Q1. How do you troubleshoot a client with no connectivity?



I follow a layered approach:



1\. Check cables and interface status.

2\. Check the client's IP configuration.

3\. Ping the loopback address if available.

4\. Ping the local gateway.

5\. Check VLAN membership and trunks.

6\. Check HSRP and Layer 3 interfaces.

7\. Check routes.

8\. Check ACL and firewall rules.

9\. Check NAT if internet access is involved.

10\. Test DNS and the final application.



\## Q2. A client has a `169.254` address. What would you check?



I would check:



\* Physical or wireless connection

\* Correct VLAN

\* DHCP server status

\* DHCP pool

\* `ip helper-address`

\* ACL permission for UDP 68 to UDP 67

\* Routing between the gateway and DHCP server



\## Q3. A client can ping an IP but not a domain name. What is wrong?



The most likely problem is DNS. I would verify the DNS server address, DNS service, DNS record, and connectivity to the DNS server.



\## Q4. A router has a route, but the ping fails. What would you check?



I would check the return route, interface status, ACLs, firewall policy, NAT, ARP, and whether the destination itself has the correct gateway.



\## Q5. How did you prove PAT was working?



I generated internet traffic from an inside client and checked:



```cisco

show nat

show xlate

```



The ASA showed the private inside address being translated to the outside interface address.



\## Q6. How did you prove guest isolation?



The guest laptop successfully reached DNS and the internet but received a destination-unreachable response when attempting to reach an internal Sales PC.



\## Q7. What was your most important troubleshooting lesson?



End-to-end communication depends on every layer. A visible route alone does not guarantee success. Interfaces, VLANs, gateways, return routes, ACLs, NAT, services, and end-device settings must all be correct.



\---



\# 31. Commands Worth Remembering



\## Switching



```cisco

show vlan brief

show interfaces trunk

show interfaces switchport

show spanning-tree

show etherchannel summary

```



\## Gateway Redundancy



```cisco

show standby brief

```



\## Routing



```cisco

show ip interface brief

show ip route

show ip protocols

show ip ospf neighbor

show ip route ospf

show ip eigrp neighbors

show ip route eigrp

```



\## Security



```cisco

show access-lists

show ip interface

show ip ssh

```



\## ASA



```cisco

show interface ip brief

show switch vlan

show route

show nat

show xlate

show access-list

show arp

```



\## End Devices



```text

ipconfig

ping <IP-address>

nslookup <domain-name>

```



\---



\# 32. Resume-Ready Project Description



\## Project Title



\*\*Scalable Enterprise Network Transformation | Cisco Packet Tracer\*\*



\## Resume Bullets



\* Designed and implemented a multi-site enterprise network connecting a three-floor headquarters, data center, branch office, DMZ, and simulated internet.

\* Configured VLANs, 802.1Q trunks, Rapid PVST+, LACP EtherChannel, HSRP, inter-VLAN routing, OSPF, EIGRP, and bidirectional route redistribution.

\* Deployed centralized DHCP, DHCP relay, DNS, corporate and guest Wi-Fi, voice VLANs, and SSH-based device management.

\* Implemented Cisco ASA security zones, dynamic PAT, static NAT, and extended ACLs for secure internet access, public DMZ services, and guest-network isolation.

\* Performed end-to-end validation and simulated HSRP and EtherChannel failures to verify gateway and link redundancy.

\* Documented device configurations, addressing, port mappings, verification commands, screenshots, and troubleshooting outcomes using Git and GitHub.



\---



\# 33. Advantages of the Project



\* Department traffic is logically separated.

\* Broadcast traffic is reduced.

\* Guest users cannot access private company networks.

\* Centralized DHCP reduces manual configuration.

\* DNS makes services easier to access.

\* HSRP protects default gateway availability.

\* EtherChannel protects against a member-link failure.

\* Dynamic routing reduces manual route configuration.

\* The branch can communicate with HQ.

\* PAT allows many users to share one public IP.

\* The DMZ protects the internal network.

\* Static NAT makes the public website reachable.

\* SSH provides encrypted management.

\* The design can support future departments and branches.

\* Documentation makes the project easier to maintain and explain.



\---



\# 34. Honest Limitations



Because the project was built in Cisco Packet Tracer:



\* It does not reproduce every feature of physical Cisco equipment.

\* Some ASA commands and counters behave differently.

\* The selected router IOS did not support CME telephony commands.

\* HSRP tracking behavior was limited.

\* Real-world monitoring, logging, authentication servers, VPNs, QoS, and advanced firewall inspection could be added later.



Mentioning limitations honestly shows technical maturity. It does not reduce the value of the project.



\---



\# 35. Final Interview Closing Statement



This project taught me that networking is not only about entering commands. Every technology solves a specific problem.



VLANs provide separation. Trunks carry VLANs. Spanning Tree prevents loops. EtherChannel provides link redundancy. HSRP protects gateways. OSPF and EIGRP exchange routes. Redistribution connects routing domains. DHCP provides addresses. DNS resolves names. ACLs control traffic. NAT connects private networks to public networks. The firewall creates trust boundaries. SSH protects management access.



Most importantly, I learned to troubleshoot systematically by checking physical connectivity, VLANs, addressing, gateways, routing, security rules, translations, and application services instead of making random configuration changes.



