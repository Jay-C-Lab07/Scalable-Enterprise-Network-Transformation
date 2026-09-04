\# 🌐 IP Addressing and Physical Port Map



\## 📌 Document Purpose



This document provides the complete IPv4 addressing plan and physical connection map for the \*\*Scalable Enterprise Network Transformation\*\* project.



It explains:



\* Which device connects to which device

\* Which physical ports are used

\* What type of connection each link represents

\* Which VLAN or subnet uses the connection

\* Why each connection is required



> The Cisco Packet Tracer file `Enterprise-Network.pkt` remains the final source of truth for the implemented topology.



\---



\## 🗺️ High-Level Network Map



```text

SRV-INTERNET

&#x20;     │

&#x20;     │ 198.51.100.0/24

&#x20;     ▼

&#x20;   R-ISP

&#x20;     │

&#x20;     │ 203.0.113.0/29

&#x20;     ▼

&#x20;  FW-EDGE ───────── SRV-DMZ-WEB

&#x20;     │                  │

&#x20;     │                  └── 172.16.10.0/24

&#x20;     │ 10.255.2.0/30

&#x20;     ▼

&#x20;    R-HQ

&#x20;  ┌──┴───────────────┐

&#x20;  │                  │

&#x20;  ▼                  ▼

DIST-SW1 ════════ DIST-SW2

&#x20;  │                  │

&#x20;  └──────┬───────────┘

&#x20;         │

&#x20;  Floor and DC switches



R-HQ

&#x20; │

&#x20; │ 10.255.1.0/30 Serial WAN

&#x20; ▼

R-BRANCH

&#x20; │

&#x20; ▼

BRANCH-SW

```



\---



\# 1. IPv4 Addressing Plan



\## 🏢 Headquarters VLANs



| VLAN | Name             | Network            | Subnet mask     | Virtual gateway | Purpose                   |

| ---: | ---------------- | ------------------ | --------------- | --------------- | ------------------------- |

|   10 | SALES            | `10.10.10.0/24`    | `255.255.255.0` | `10.10.10.1`    | Sales users               |

|   20 | HR               | `10.10.20.0/24`    | `255.255.255.0` | `10.10.20.1`    | Human Resources           |

|   30 | FINANCE          | `10.10.30.0/24`    | `255.255.255.0` | `10.10.30.1`    | Finance users             |

|   40 | IT               | `10.10.40.0/24`    | `255.255.255.0` | `10.10.40.1`    | IT users                  |

|   50 | MANAGEMENT       | `10.10.50.0/24`    | `255.255.255.0` | `10.10.50.1`    | Management users          |

|   60 | SERVERS          | `10.10.60.0/24`    | `255.255.255.0` | `10.10.60.1`    | Internal servers          |

|   70 | VOICE            | `10.10.70.0/24`    | `255.255.255.0` | `10.10.70.1`    | HQ IP phones              |

|   80 | CORP-WIFI        | `10.10.80.0/24`    | `255.255.255.0` | `10.10.80.1`    | Corporate wireless        |

|   90 | GUEST-WIFI       | `10.10.90.0/24`    | `255.255.255.0` | `10.10.90.1`    | Guest wireless            |

|   99 | NETWORK-MGMT     | `10.10.99.0/24`    | `255.255.255.0` | `10.10.99.1`    | Network-device management |

|  999 | NATIVE-BLACKHOLE | No Layer 3 network | —               | —               | Native and unused ports   |



\## 🏬 Branch VLANs



| VLAN | Name             | Network            | Subnet mask     | Gateway      | Purpose                  |

| ---: | ---------------- | ------------------ | --------------- | ------------ | ------------------------ |

|  110 | BRANCH-USERS     | `10.20.10.0/24`    | `255.255.255.0` | `10.20.10.1` | Branch computers         |

|  120 | BRANCH-VOICE     | `10.20.20.0/24`    | `255.255.255.0` | `10.20.20.1` | Branch IP phones         |

|  199 | BRANCH-MGMT      | `10.20.99.0/24`    | `255.255.255.0` | `10.20.99.1` | Branch-device management |

|  999 | NATIVE-BLACKHOLE | No Layer 3 network | —               | —            | Native and unused ports  |



\## 🔗 Routed Networks



| Link               | Network           | Device A              | Device B                         |

| ------------------ | ----------------- | --------------------- | -------------------------------- |

| R-HQ ↔ DIST-SW1    | `10.255.10.0/30`  | R-HQ: `10.255.10.1`   | DIST-SW1: `10.255.10.2`          |

| R-HQ ↔ DIST-SW2    | `10.255.10.4/30`  | R-HQ: `10.255.10.5`   | DIST-SW2: `10.255.10.6`          |

| R-HQ ↔ R-BRANCH    | `10.255.1.0/30`   | R-HQ: `10.255.1.1`    | R-BRANCH: `10.255.1.2`           |

| R-HQ ↔ FW-EDGE     | `10.255.2.0/30`   | R-HQ: `10.255.2.1`    | ASA: `10.255.2.2`                |

| FW-EDGE ↔ R-ISP    | `203.0.113.0/29`  | R-ISP: `203.0.113.1`  | ASA: `203.0.113.2`               |

| DMZ                | `172.16.10.0/24`  | ASA: `172.16.10.1`    | DMZ server: `172.16.10.10`       |

| Simulated internet | `198.51.100.0/24` | R-ISP: `198.51.100.1` | Internet server: `198.51.100.10` |



\---



\# 2. Core and Edge Physical Connections



| Starting device | Starting port      | Destination device | Destination port   | Connection purpose                                |

| --------------- | ------------------ | ------------------ | ------------------ | ------------------------------------------------- |

| SRV-INTERNET    | FastEthernet0      | R-ISP              | GigabitEthernet0/1 | Connects the simulated external server to the ISP |

| R-ISP           | GigabitEthernet0/0 | FW-EDGE            | Ethernet0/0        | Connects the ISP to the ASA Outside zone          |

| FW-EDGE         | Ethernet0/1        | R-HQ               | GigabitEthernet0/0 | Connects the ASA Inside zone to the enterprise    |

| FW-EDGE         | Ethernet0/2        | SRV-DMZ-WEB        | FastEthernet0      | Places the public web server inside the DMZ       |

| R-HQ            | GigabitEthernet0/1 | DIST-SW1           | GigabitEthernet0/1 | First routed connection to the distribution layer |

| R-HQ            | GigabitEthernet0/2 | DIST-SW2           | GigabitEthernet0/1 | Second redundant routed connection                |

| R-HQ            | Serial0/3/0        | R-BRANCH           | Serial0/3/0        | Point-to-point WAN between HQ and branch          |

| R-BRANCH        | GigabitEthernet0/0 | BRANCH-SW          | GigabitEthernet0/1 | 802.1Q trunk carrying all branch VLANs            |



\---



\# 3. Distribution EtherChannel



| Starting device | Starting port    | Destination device | Destination port | Purpose                 |

| --------------- | ---------------- | ------------------ | ---------------- | ----------------------- |

| DIST-SW1        | FastEthernet0/23 | DIST-SW2           | FastEthernet0/23 | First LACP member link  |

| DIST-SW1        | FastEthernet0/24 | DIST-SW2           | FastEthernet0/24 | Second LACP member link |



The two physical links are combined into:



```text

Port-channel1

```



This provides:



\* Additional aggregate bandwidth

\* Link redundancy

\* One logical STP connection

\* Continued operation when one member fails



\---



\# 4. Access-Switch Uplinks



| Starting device | Starting port      | Destination device | Destination port | Link type                   |

| --------------- | ------------------ | ------------------ | ---------------- | --------------------------- |

| FLOOR1-SW       | GigabitEthernet0/1 | DIST-SW1           | FastEthernet0/1  | 802.1Q trunk                |

| FLOOR1-SW       | GigabitEthernet0/2 | DIST-SW2           | FastEthernet0/1  | Redundant trunk             |

| FLOOR2-SW       | GigabitEthernet0/1 | DIST-SW1           | FastEthernet0/2  | 802.1Q trunk                |

| FLOOR2-SW       | GigabitEthernet0/2 | DIST-SW2           | FastEthernet0/2  | Redundant trunk             |

| FLOOR3-SW       | GigabitEthernet0/1 | DIST-SW1           | FastEthernet0/3  | 802.1Q trunk                |

| FLOOR3-SW       | GigabitEthernet0/2 | DIST-SW2           | FastEthernet0/3  | Redundant trunk             |

| DC-SW           | GigabitEthernet0/1 | DIST-SW1           | FastEthernet0/4  | Data-center trunk           |

| DC-SW           | GigabitEthernet0/2 | DIST-SW2           | FastEthernet0/4  | Redundant data-center trunk |



Each access switch has two uplinks so the switch can remain connected if one distribution path becomes unavailable.



Rapid-PVST+ prevents these redundant links from creating Layer 2 loops.



\---



\# 5. FLOOR1-SW Port Map



| Switch port | Connected device | Data VLAN | Voice VLAN | Configuration              |

| ----------- | ---------------- | --------: | ---------: | -------------------------- |

| Fa0/1       | SALES-PC1        |        10 |          — | Access port                |

| Fa0/2       | SALES-PC2        |        10 |          — | Access port                |

| Fa0/3       | HQ-PHONE1        |        10 |         70 | Data and voice port        |

| Fa0/4       | AP-HQ-GUEST      |        90 |          — | Guest wireless access port |

| Fa0/5–24    | Unused           |       999 |          — | Administratively shut down |

| Gi0/1       | DIST-SW1         |  Multiple |   Multiple | Trunk                      |

| Gi0/2       | DIST-SW2         |  Multiple |   Multiple | Redundant trunk            |



Management address:



```text

10.10.99.11/24

```



Default gateway:



```text

10.10.99.1

```



\---



\# 6. FLOOR2-SW Port Map



| Switch port | Connected device | Data VLAN | Voice VLAN | Configuration              |

| ----------- | ---------------- | --------: | ---------: | -------------------------- |

| Fa0/1       | HR-PC1           |        20 |          — | Access port                |

| Fa0/2       | HR-PC2           |        20 |          — | Access port                |

| Fa0/3       | FIN-PC1          |        30 |          — | Access port                |

| Fa0/4       | FIN-PC2          |        30 |          — | Access port                |

| Fa0/5       | HQ-PHONE2        |        20 |         70 | Data and voice port        |

| Fa0/6–24    | Unused           |       999 |          — | Administratively shut down |

| Gi0/1       | DIST-SW1         |  Multiple |   Multiple | Trunk                      |

| Gi0/2       | DIST-SW2         |  Multiple |   Multiple | Redundant trunk            |



Management address:



```text

10.10.99.12/24

```



\---



\# 7. FLOOR3-SW Port Map



| Switch port | Connected device |     VLAN | Configuration                  |

| ----------- | ---------------- | -------: | ------------------------------ |

| Fa0/1       | IT-PC1           |       40 | Access port                    |

| Fa0/2       | IT-PC2           |       40 | Access port                    |

| Fa0/3       | MGMT-PC1         |       50 | Access port                    |

| Fa0/4       | MGMT-PC2         |       50 | Access port                    |

| Fa0/5       | AP-HQ-CORP       |       80 | Corporate wireless access port |

| Fa0/6–24    | Unused           |      999 | Administratively shut down     |

| Gi0/1       | DIST-SW1         | Multiple | Trunk                          |

| Gi0/2       | DIST-SW2         | Multiple | Redundant trunk                |



Management address:



```text

10.10.99.13/24

```



\---



\# 8. DC-SW Port Map



| Switch port | Connected device |     VLAN | Purpose                    |

| ----------- | ---------------- | -------: | -------------------------- |

| Fa0/1       | SRV-DHCP-DNS     |       60 | DHCP and DNS server        |

| Fa0/2       | SRV-INTERNAL-WEB |       60 | Internal web server        |

| Fa0/3–24    | Unused           |      999 | Administratively shut down |

| Gi0/1       | DIST-SW1         | Multiple | Primary trunk              |

| Gi0/2       | DIST-SW2         | Multiple | Redundant trunk            |



Management address:



```text

10.10.99.14/24

```



\---



\# 9. BRANCH-SW Port Map



| Switch port | Connected device | Data VLAN | Voice VLAN | Configuration              |

| ----------- | ---------------- | --------: | ---------: | -------------------------- |

| Fa0/1       | BR-PC1           |       110 |          — | Access port                |

| Fa0/2       | BR-PC2           |       110 |          — | Access port                |

| Fa0/3       | BR-PHONE1        |       110 |        120 | Branch data and voice      |

| Fa0/4–24    | Unused           |       999 |          — | Administratively shut down |

| Gi0/1       | R-BRANCH         |  Multiple |   Multiple | Router-on-a-stick trunk    |



Management address:



```text

10.20.99.2/24

```



Default gateway:



```text

10.20.99.1

```



\---



\# 10. Distribution-Switch Port Map



\## DIST-SW1



| Port   | Connected device | Configuration                 |

| ------ | ---------------- | ----------------------------- |

| G0/1   | R-HQ G0/1        | Routed port: `10.255.10.2/30` |

| Fa0/1  | FLOOR1-SW Gi0/1  | Trunk                         |

| Fa0/2  | FLOOR2-SW Gi0/1  | Trunk                         |

| Fa0/3  | FLOOR3-SW Gi0/1  | Trunk                         |

| Fa0/4  | DC-SW Gi0/1      | Trunk                         |

| Fa0/23 | DIST-SW2 Fa0/23  | LACP member                   |

| Fa0/24 | DIST-SW2 Fa0/24  | LACP member                   |



Management SVI:



```text

VLAN 99: 10.10.99.2/24

```



\## DIST-SW2



| Port   | Connected device | Configuration                 |

| ------ | ---------------- | ----------------------------- |

| G0/1   | R-HQ G0/2        | Routed port: `10.255.10.6/30` |

| Fa0/1  | FLOOR1-SW Gi0/2  | Trunk                         |

| Fa0/2  | FLOOR2-SW Gi0/2  | Trunk                         |

| Fa0/3  | FLOOR3-SW Gi0/2  | Trunk                         |

| Fa0/4  | DC-SW Gi0/2      | Trunk                         |

| Fa0/23 | DIST-SW1 Fa0/23  | LACP member                   |

| Fa0/24 | DIST-SW1 Fa0/24  | LACP member                   |



Management SVI:



```text

VLAN 99: 10.10.99.3/24

```



\---



\# 11. Router Interface Map



\## R-HQ



| Interface | Address          | Connected device | Function             |

| --------- | ---------------- | ---------------- | -------------------- |

| G0/0      | `10.255.2.1/30`  | FW-EDGE E0/1     | Firewall Inside link |

| G0/1      | `10.255.10.1/30` | DIST-SW1 G0/1    | First OSPF link      |

| G0/2      | `10.255.10.5/30` | DIST-SW2 G0/1    | Second OSPF link     |

| S0/3/0    | `10.255.1.1/30`  | R-BRANCH S0/3/0  | Branch WAN           |



\## R-BRANCH



| Interface | Address         | Connected device | Function                  |

| --------- | --------------- | ---------------- | ------------------------- |

| G0/0      | No IP           | BRANCH-SW Gi0/1  | Physical 802.1Q trunk     |

| G0/0.110  | `10.20.10.1/24` | VLAN 110         | Branch Users gateway      |

| G0/0.120  | `10.20.20.1/24` | VLAN 120         | Branch Voice gateway      |

| G0/0.199  | `10.20.99.1/24` | VLAN 199         | Branch Management gateway |

| G0/0.999  | No IP           | VLAN 999         | Native black-hole VLAN    |

| S0/3/0    | `10.255.1.2/30` | R-HQ S0/3/0      | Branch WAN                |



\## R-ISP



| Interface | Address           | Connected device | Function                   |

| --------- | ----------------- | ---------------- | -------------------------- |

| G0/0      | `203.0.113.1/29`  | FW-EDGE E0/0     | Public firewall network    |

| G0/1      | `198.51.100.1/24` | SRV-INTERNET     | Simulated internet gateway |



\---



\# 12. Firewall Interface Map



| ASA physical port | VLAN interface | Name    | Security level | Address          | Connected device |

| ----------------- | -------------- | ------- | -------------: | ---------------- | ---------------- |

| E0/0              | VLAN 2         | outside |              0 | `203.0.113.2/29` | R-ISP            |

| E0/1              | VLAN 1         | inside  |            100 | `10.255.2.2/30`  | R-HQ             |

| E0/2              | VLAN 3         | dmz     |             50 | `172.16.10.1/24` | SRV-DMZ-WEB      |



Public static NAT address:



```text

203.0.113.3

```



Internal DMZ server address:



```text

172.16.10.10

```



Translation:



```text

203.0.113.3 ↔ 172.16.10.10

```



\---



\# 13. Server Addressing



| Server           | Address         | Mask            | Gateway        | DNS                |

| ---------------- | --------------- | --------------- | -------------- | ------------------ |

| SRV-DHCP-DNS     | `10.10.60.10`   | `255.255.255.0` | `10.10.60.1`   | `10.10.60.10`      |

| SRV-INTERNAL-WEB | `10.10.60.20`   | `255.255.255.0` | `10.10.60.1`   | `10.10.60.10`      |

| SRV-DMZ-WEB      | `172.16.10.10`  | `255.255.255.0` | `172.16.10.1`  | `10.10.60.10`      |

| SRV-INTERNET     | `198.51.100.10` | `255.255.255.0` | `198.51.100.1` | Test/local setting |



Internal DNS record:



```text

intranet.entcorp.com → 10.10.60.20

```



\---



\# 14. Wireless Connection Map



\## Corporate Wireless



```text

CORP-LAPTOP

&#x20;  → ENT-CORP SSID

&#x20;  → AP-HQ-CORP

&#x20;  → FLOOR3-SW Fa0/5

&#x20;  → VLAN 80

&#x20;  → HSRP gateway 10.10.80.1

```



Corporate users can access internal services and the internet.



\## Guest Wireless



```text

GUEST-LAPTOP

&#x20;  → ENT-GUEST SSID

&#x20;  → AP-HQ-GUEST

&#x20;  → FLOOR1-SW Fa0/4

&#x20;  → VLAN 90

&#x20;  → HSRP gateway 10.10.90.1

&#x20;  → Guest ACL

```



Guest users can access DHCP, DNS and the internet but cannot access protected internal networks.



\---



\# 15. Understanding Each Link Type



| Link type                  | Where used                    | Why                                               |

| -------------------------- | ----------------------------- | ------------------------------------------------- |

| Access link                | PC/server/AP to switch        | Carries one data VLAN                             |

| Data and voice access link | Phone to switch               | Carries separate data and voice VLANs             |

| 802.1Q trunk               | Access switch to distribution | Carries multiple VLANs                            |

| Routed Ethernet link       | R-HQ to distribution          | Forms Layer 3 OSPF neighbour relationships        |

| EtherChannel trunk         | DIST-SW1 to DIST-SW2          | Provides bandwidth and redundancy                 |

| Serial WAN                 | R-HQ to R-BRANCH              | Simulates a point-to-point WAN                    |

| Firewall Inside link       | R-HQ to ASA                   | Carries enterprise internet-bound traffic         |

| Firewall Outside link      | ASA to R-ISP                  | Connects the enterprise to the simulated internet |

| DMZ access link            | ASA to DMZ server             | Isolates the public web server                    |



\---



\# 16. Packet-Flow Reference



\## HQ user to internal server



```text

PC

→ Access switch

→ Distribution switch

→ Inter-VLAN routing

→ DC-SW

→ Internal server

```



\## Branch user to HQ server



```text

Branch PC

→ BRANCH-SW

→ R-BRANCH

→ Serial WAN

→ R-HQ

→ Distribution switch

→ DC-SW

→ HQ server

```



\## Internal user to internet



```text

User

→ Access switch

→ Distribution switch

→ R-HQ

→ FW-EDGE

→ Dynamic PAT

→ R-ISP

→ Internet server

```



\## Internet user to public DMZ server



```text

Internet server

→ R-ISP

→ ASA Outside interface

→ Outside ACL

→ Static NAT

→ ASA DMZ interface

→ SRV-DMZ-WEB

```



\## Guest user attempting internal access



```text

Guest laptop

→ Guest access point

→ FLOOR1-SW

→ VLAN 90 gateway

→ Guest ACL

→ Packet denied

```



\---



\# 17. Troubleshooting Checklist



For every connectivity problem, check in this order:



1\. Physical cable and link light

2\. Interface status

3\. Device IPv4 address

4\. Subnet mask

5\. Default gateway

6\. Access VLAN

7\. Trunk status

8\. STP state

9\. HSRP state

10\. Routing table

11\. ACL rules

12\. Firewall rules

13\. NAT translation

14\. DHCP, DNS or HTTP service status



Useful commands:



```text

show ip interface brief

show interfaces status

show vlan brief

show interfaces trunk

show spanning-tree

show standby brief

show etherchannel summary

show ip route

show access-lists

show nat

show xlate

ping

nslookup

```



