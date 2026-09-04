\# 🔧 Enterprise Network Troubleshooting Report



\## 📌 Purpose



This report documents the major problems encountered while building the Scalable Enterprise Network Transformation project in Cisco Packet Tracer.



For every problem, it explains:



\* What failed

\* What symptoms appeared

\* Why it failed

\* How the problem was diagnosed

\* How it was fixed

\* How the solution was verified

\* What networking lesson was learned



Troubleshooting followed this general method:



1\. Identify the failed service.

2\. Start testing close to the affected device.

3\. Verify physical and interface status.

4\. Verify VLANs, IP addresses, gateways, and routes.

5\. Inspect protocol-specific information.

6\. Correct one problem at a time.

7\. Test again and save the configuration.



\---



\# 1. Initial Server Connectivity Failure



\## Problem



Some network devices could not communicate with the internal servers.



Important servers included:



| Server           |    IP address | Purpose          |

| ---------------- | ------------: | ---------------- |

| SRV-DHCP-DNS     | `10.10.60.10` | DHCP and DNS     |

| SRV-INTERNAL-WEB | `10.10.60.20` | Internal website |



\## Symptoms



\* Pings to the servers failed.

\* Clients could not receive DHCP addresses.

\* DNS queries failed.

\* The internal website could not be opened.



\## Root Cause



The servers did not initially have complete static IPv4 configurations.



A server needs three important settings:



\* IP address

\* Subnet mask

\* Default gateway



Without a correct default gateway, the server can communicate only with devices in its own subnet.



\## Solution



The server settings were corrected manually.



Example for the DHCP and DNS server:



```text

IP address:      10.10.60.10

Subnet mask:     255.255.255.0

Default gateway: 10.10.60.1

```



Example for the internal web server:



```text

IP address:      10.10.60.20

Subnet mask:     255.255.255.0

Default gateway: 10.10.60.1

```



\## Verification



```text

ping 10.10.60.1

ping 10.10.60.10

ping 10.10.60.20

```



\## Lesson Learned



A server should normally use a static IP address so its address does not unexpectedly change. Clients and network services must always know where to find it.



\---



\# 2. DNS Record Configured on the Wrong Server



\## Problem



Clients could reach the internal web server by IP address but could not reach it using its domain name.



\## Symptoms



This could work:



```text

ping 10.10.60.20

```



But this failed:



```text

nslookup intranet.entcorp.com

```



\## Root Cause



The DNS record for `intranet.entcorp.com` was created on the wrong server.



The clients were configured to use `10.10.60.10` as their DNS server. Therefore, the record had to exist on that device.



\## Solution



DNS was enabled on `SRV-DHCP-DNS`, and the following record was added:



```text

Name:    intranet.entcorp.com

Address: 10.10.60.20

```



\## Verification



```text

nslookup intranet.entcorp.com

```



Expected result:



```text

Name:    intranet.entcorp.com

Address: 10.10.60.20

```



\## Lesson Learned



DNS records must be created on the DNS server that clients actually query. Successful IP connectivity does not automatically mean DNS is working.



\---



\# 3. Branch DHCP Pool Addressing Error



\## Problem



Branch users could not obtain valid IP addresses automatically.



\## Symptoms



A branch PC either received no address or received an APIPA address beginning with `169.254`.



\## Root Cause



The start address in the branch user DHCP pool was entered incorrectly as:



```text

10.20.60.100

```



However, branch users belong to:



```text

10.20.10.0/24

```



The incorrect address was outside the branch user subnet.



\## Solution



The branch DHCP pool was corrected to use:



```text

Pool name:       BRANCH-USERS-POOL

Default gateway: 10.20.10.1

DNS server:      10.10.60.10

Start address:   10.20.10.100

Subnet mask:     255.255.255.0

```



DHCP relay was also verified on the branch router:



```cisco

interface GigabitEthernet0/0.110

&#x20;ip address 10.20.10.1 255.255.255.0

&#x20;ip helper-address 10.10.60.10

```



\## Verification



The branch PC successfully received:



```text

IPv4 address:    10.20.10.100

Subnet mask:     255.255.255.0

Default gateway: 10.20.10.1

DNS server:      10.10.60.10

```



Connectivity was tested with:



```text

ping 10.20.10.1

ping 10.10.60.10

ping 10.10.10.100

```



\## Lesson Learned



The DHCP pool network must match the client VLAN. DHCP relay forwards requests, but it cannot correct an incorrectly configured address pool.



\---



\# 4. Branch WAN Address Conflict



\## Problem



The WAN link between `R-HQ` and `R-BRANCH` initially failed.



\## Symptoms



The branch router was incorrectly configured with `10.255.1.1`, which was also intended for `R-HQ`.



Pinging the local address created misleading results because a router can respond to its own IP address.



\## Root Cause



Both sides of the serial link were temporarily assigned the same address.



A `/30` subnet provides two usable host addresses:



```text

Network:   10.255.1.0

R-HQ:      10.255.1.1

R-BRANCH:  10.255.1.2

Broadcast: 10.255.1.3

```



\## Solution



`R-HQ` was configured as:



```cisco

interface Serial0/3/0

&#x20;description WAN-TO-R-BRANCH

&#x20;ip address 10.255.1.1 255.255.255.252

&#x20;no shutdown

```



`R-BRANCH` was configured as:



```cisco

interface Serial0/3/0

&#x20;description WAN-TO-R-HQ

&#x20;ip address 10.255.1.2 255.255.255.252

&#x20;clock rate 64000

&#x20;no shutdown

```



The clock rate was configured only on the DCE end.



\## Verification



```cisco

show ip interface brief

ping 10.255.1.1

ping 10.255.1.2

```



Both serial interfaces displayed `up/up`.



\## Lesson Learned



Each Layer 3 interface must have a unique IP address. On serial connections, only the DCE side supplies clocking.



\---



\# 5. Missing Return Routes Between HQ and Branch



\## Problem



`R-BRANCH` learned the HQ networks, but pings to HQ initially failed.



\## Symptoms



The branch routing table contained the HQ routes:



```text

D EX 10.10.10.0/24 via 10.255.1.1

D EX 10.10.60.0/24 via 10.255.1.1

```



However, pings still timed out.



\## Root Cause



Traffic could travel from the branch toward HQ, but some HQ devices did not initially know how to return traffic to the branch networks.



This is called a return-path problem.



\## Solution



Bidirectional redistribution was configured on `R-HQ`.



EIGRP routes were redistributed into OSPF:



```cisco

router ospf 1

&#x20;redistribute eigrp 100 subnets

```



OSPF routes were redistributed into EIGRP using an EIGRP seed metric:



```cisco

router eigrp 100

&#x20;redistribute ospf 1 metric 100000 100 255 1 1500

```



\## Verification



On `R-HQ`:



```cisco

show ip route eigrp

show ip protocols

```



On `R-BRANCH`:



```cisco

show ip route eigrp

ping 10.10.60.10

ping 10.10.10.1

```



The pings eventually succeeded.



\## Lesson Learned



Communication requires routes in both directions. Seeing a route on the source device does not prove the destination has a return route.



\---



\# 6. Guest Wi-Fi DHCP Failure



\## Problem



The guest laptop connected to the wireless network but failed to receive an IPv4 address.



\## Symptoms



The laptop received an APIPA address such as:



```text

169.254.225.13

```



An address beginning with `169.254` normally means DHCP failed.



\## Root Cause



Before receiving an address, a DHCP client sends a request using:



```text

Source IP:      0.0.0.0

Source UDP:     68

Destination UDP: 67

```



The guest ACL did not initially allow this DHCP request. The required rule also had to exist on both distribution switches because either switch could become the HSRP active gateway.



\## Solution



A numbered ACL was used because Packet Tracer did not reliably apply the named ACL to the SVI.



```cisco

access-list 190 permit udp any eq bootpc any eq bootps

access-list 190 permit udp 10.10.90.0 0.0.0.255 host 10.10.60.10 eq domain

access-list 190 permit tcp 10.10.90.0 0.0.0.255 host 10.10.60.10 eq domain

access-list 190 deny ip 10.10.90.0 0.0.0.255 10.0.0.0 0.255.255.255

access-list 190 permit ip 10.10.90.0 0.0.0.255 any

```



It was applied inbound to VLAN 90:



```cisco

interface Vlan90

&#x20;ip access-group 190 in

```



The same ACL was configured on both distribution switches.



\## Verification



The guest laptop received:



```text

IPv4 address:    10.10.90.109

Subnet mask:     255.255.255.0

Default gateway: 10.10.90.1

DNS server:      10.10.60.10

```



Tests showed the intended security policy:



```text

ping 10.10.10.100

```



Result: blocked.



```text

ping 198.51.100.10

```



Result: successful.



```text

nslookup intranet.entcorp.com

```



Result: DNS resolution successful.



\## Lesson Learned



ACL order is extremely important. Rules are processed from top to bottom, and the first match is used. Required services such as DHCP and DNS must be permitted before a broader deny rule.



\---



\# 7. DMZ Server Gateway Typing Error



\## Problem



The ASA firewall could not ping the DMZ web server.



\## Symptoms



The server used:



```text

Default gateway: 176.16.10.1

```



But the firewall DMZ interface was:



```text

172.16.10.1

```



\## Root Cause



The first part of the gateway address was typed as `176` instead of `172`.



Even a one-digit error places the gateway in a different network.



\## Solution



The DMZ server was corrected to:



```text

IP address:      172.16.10.10

Subnet mask:     255.255.255.0

Default gateway: 172.16.10.1

```



\## Verification



From the server:



```text

ping 172.16.10.1

```



From the ASA:



```text

ping 172.16.10.10

```



Both tests succeeded.



\## Lesson Learned



Always compare the host IP address and gateway carefully. They must belong to the same local subnet unless a special design is being used.



\---



\# 8. ASA DMZ Interface Licensing Restriction



\## Problem



The ASA VLAN 3 interface did not operate correctly as a named DMZ interface.



\## Symptoms



The configuration showed:



```cisco

interface Vlan3

&#x20;no nameif

&#x20;security-level 50

&#x20;ip address 172.16.10.1 255.255.255.0

```



The `nameif dmz` command was not retained normally.



\## Root Cause



The Packet Tracer ASA model uses a restricted base license for the third VLAN. It requires the third VLAN to be prevented from forwarding directly to one of the other VLANs.



\## Solution



VLAN 3 was configured as a restricted DMZ:



```cisco

interface Vlan3

&#x20;no forward interface Vlan1

&#x20;nameif dmz

&#x20;security-level 50

&#x20;ip address 172.16.10.1 255.255.255.0

&#x20;no shutdown

```



Ethernet0/2 was assigned to VLAN 3:



```cisco

interface Ethernet0/2

&#x20;switchport access vlan 3

&#x20;no shutdown

```



\## Verification



```cisco

show running-config interface vlan 3

show switch vlan

ping 172.16.10.10

```



The final interface configuration showed:



```text

no forward interface Vlan1

nameif dmz

security-level 50

ip address 172.16.10.1 255.255.255.0

```



\## Lesson Learned



Device models and software licenses can affect which commands and designs are supported. Troubleshooting must consider platform limitations, not only configuration mistakes.



\---



\# 9. ASA Commands Entered in the Wrong Mode



\## Problem



Several valid ASA commands returned invalid-input errors.



\## Symptoms



Commands such as these failed at the privileged EXEC prompt:



```cisco

policy-map global\_policy

class inspection\_default

inspect icmp

access-list OUTSIDE-IN ...

access-group OUTSIDE-IN ...

```



\## Root Cause



These are configuration commands, but they were entered at the `ciscoasa#` prompt instead of the `ciscoasa(config)#` prompt.



Additionally, `class inspection\_default` could not be selected before the class map existed.



\## Solution



Configuration mode was entered first:



```cisco

enable

configure terminal

```



The missing class map was created:



```cisco

class-map inspection\_default

&#x20;match default-inspection-traffic

&#x20;exit

```



The policy was then configured:



```cisco

policy-map global\_policy

&#x20;class inspection\_default

&#x20; inspect icmp

```



\## Verification



The ASA accepted the commands without an invalid-input message.



\## Lesson Learned



Cisco commands are organized into modes. A correct command entered in the wrong mode will still fail.



Important prompts include:



| Prompt                     | Mode                           |

| -------------------------- | ------------------------------ |

| `ciscoasa>`                | User EXEC                      |

| `ciscoasa#`                | Privileged EXEC                |

| `ciscoasa(config)#`        | Global configuration           |

| `ciscoasa(config-if)#`     | Interface configuration        |

| `ciscoasa(config-pmap)#`   | Policy-map configuration       |

| `ciscoasa(config-pmap-c)#` | Policy-map class configuration |



\---



\# 10. Static Service PAT Unsupported in Packet Tracer



\## Problem



The ASA rejected the intended HTTP port-forwarding command:



```cisco

nat (dmz,outside) static interface service tcp 80 80

```



\## Root Cause



The Packet Tracer ASA implementation did not support that exact service NAT syntax.



\## Solution



The outside subnet was expanded from `/30` to `/29`:



```text

ASA outside: 203.0.113.2/29

ISP:         203.0.113.1/29

DMZ public:  203.0.113.3

```



A one-to-one static NAT mapping was created:



```cisco

object network DMZ-WEB-PUBLIC

&#x20;host 172.16.10.10

&#x20;nat (dmz,outside) static 203.0.113.3

```



HTTP and HTTPS were permitted:



```cisco

access-list OUTSIDE-IN extended permit tcp any host 172.16.10.10 eq 80

access-list OUTSIDE-IN extended permit tcp any host 172.16.10.10 eq 443

access-group OUTSIDE-IN in interface outside

```



\## Verification



From `SRV-INTERNET`, the following URL successfully opened the DMZ website:



```text

http://203.0.113.3

```



The ACL counter confirmed external HTTP traffic:



```cisco

show access-list

```



Example result:



```text

permit tcp any host 172.16.10.10 eq www (hitcnt=2)

```



\## Lesson Learned



When a simulator does not support a specific production command, an equivalent supported design can be used. One-to-one static NAT was a suitable alternative for this lab.



\---



\# 11. NAT Counters Appeared to Remain at Zero



\## Problem



The DMZ website opened successfully, but the static NAT counters still displayed zero.



\## Symptoms



```text

translate\_hits = 0

untranslate\_hits = 0

```



At the same time, the outside ACL showed HTTP matches.



\## Explanation



Packet Tracer does not always update every ASA NAT counter exactly like real ASA software.



The successful webpage and increasing ACL hit counter provided stronger evidence that the connection worked.



\## Verification



```cisco

show nat

show access-list

```



The ACL contained:



```text

eq www (hitcnt=2)

```



\## Lesson Learned



Verification should use multiple sources of evidence. One simulator counter should not overrule successful application testing and ACL matches.



\---



\# 12. First Ping Packet Failed



\## Problem



Many tests initially returned `.!!!!`, meaning the first ping failed but the remaining four succeeded.



\## Root Cause



Before sending traffic on an Ethernet network, a device may need to learn the destination MAC address through ARP.



The first packet can be delayed or lost while ARP, spanning tree, HSRP, or routing protocol convergence completes.



\## Solution



The network was allowed a few seconds to converge, and the ping was repeated.



\## Verification



The second attempt normally showed:



```text

!!!!!

Success rate is 100 percent

```



\## Lesson Learned



One failed initial ping does not always indicate a broken network. Repeat the test after checking interface state and allowing control protocols time to converge.



\---



\# 13. HSRP Uplink Tracking Did Not Behave as Expected



\## Problem



Shutting down the routed uplink on `DIST-SW1` did not immediately cause all expected HSRP roles to move to `DIST-SW2`.



\## Root Cause



Packet Tracer does not reproduce every HSRP tracking behavior exactly like physical Cisco equipment.



Additionally, shutting a routed uplink does not automatically bring down every local SVI.



\## Solution



HSRP failover was tested by shutting the actual VLAN 10 SVI:



```cisco

interface Vlan10

&#x20;shutdown

```



`DIST-SW2` then became active for VLAN 10.



Connectivity was tested while `DIST-SW1` VLAN 10 was unavailable.



The SVI was restored afterward:



```cisco

interface Vlan10

&#x20;no shutdown

```



Because `DIST-SW1` had a higher priority and preemption was enabled, it became active again.



\## Verification



```cisco

show standby brief

```



During the failure, `DIST-SW2` displayed:



```text

Vl10 Active local

```



After restoration, `DIST-SW1` displayed:



```text

Vl10 110 P Active local

```



\## Lesson Learned



A failover test must disable the component responsible for the tested function. Always restore the interface after testing.



\---



\# 14. EtherChannel Member-Link Failure



\## Test



One member of the LACP EtherChannel was shut down:



```cisco

interface FastEthernet0/23

&#x20;shutdown

```



\## Result



```cisco

show etherchannel summary

```



Displayed:



```text

Po1(SU) LACP Fa0/23(D) Fa0/24(P)

```



Meaning:



\* `Po1(SU)` — Layer 2 port channel in use

\* `Fa0/23(D)` — member link down

\* `Fa0/24(P)` — member link still bundled



Network connectivity continued through FastEthernet0/24.



\## Restoration



```cisco

interface FastEthernet0/23

&#x20;no shutdown

```



The final result was:



```text

Po1(SU) LACP Fa0/23(P) Fa0/24(P)

```



\## Lesson Learned



EtherChannel provides both additional bandwidth and link redundancy. If one member fails, the logical port channel can remain operational through the surviving link.



\---



\# 15. Voice Configuration Unsupported on R-HQ



\## Problem



Commands such as the following were rejected:



```cisco

telephony-service

max-ephones 5

ephone-dn 1

number 7001

```



\## Root Cause



The selected Packet Tracer router and IOS image did not support Cisco Unified Communications Manager Express commands.



\## Solution



Instead of implementing call processing, the project verified the switching foundation required for IP phones.



HQ phone port:



```text

Access VLAN: 10

Voice VLAN:  70

```



Branch phone port:



```text

Access VLAN: 110

Voice VLAN:  120

```



\## Verification



```cisco

show interfaces FastEthernet0/3 switchport

```



\## Lesson Learned



Voice VLAN configuration and call-processing services are different functions.



\* The switch separates phone traffic using the voice VLAN.

\* A call manager registers phones and manages telephone numbers and calls.



This project successfully verified the voice VLAN infrastructure, while CME was documented as a simulator limitation.



\---



\# 16. Incorrect or Unsupported Verification Commands



\## Problem



Some commands returned invalid-input errors.



Examples included:



```cisco

how ip route 10.10.60.0

show running-config interface GigabitEthernet0/0.110

show access-list OUTSIDE-IN

show mac-address-table

```



\## Causes



\* `how` was a typing mistake; the correct command is `show`.

\* Some Packet Tracer IOS versions do not support interface filtering after `show running-config`.

\* Packet Tracer ASA supports `show access-list`, but not always the named-filter variation.

\* The Packet Tracer ASA did not support the same MAC-table command as an IOS switch.



\## Alternative Commands



For the router subinterface:



```cisco

show ip interface GigabitEthernet0/0.110

```



For the route:



```cisco

show ip route 10.10.60.0

```



For ASA ACLs:



```cisco

show access-list

```



For ASA Layer 2 VLAN assignments:



```cisco

show switch vlan

```



For learned Layer 3 neighbors:



```cisco

show arp

```



\## Lesson Learned



Command support depends on the device platform, IOS version, and simulator. Use alternative verification commands when the desired command is unavailable.



\---



\# 17. Native VLAN Mismatch



\## Problem



A trunk initially used different native VLAN settings on its two ends.



\## Risk



A native VLAN mismatch can cause:



\* Spanning-tree warnings

\* Untagged traffic entering the wrong VLAN

\* Security weaknesses

\* Unexpected connectivity behavior



\## Solution



Native VLAN 999 was configured consistently on both sides of trunk links:



```cisco

switchport trunk native vlan 999

```



Only required VLANs were allowed:



```cisco

switchport trunk allowed vlan 110,120,199,999

```



\## Verification



```cisco

show interfaces trunk

```



\## Lesson Learned



Both ends of an 802.1Q trunk must agree on the native VLAN and allowed VLAN list.



\---



\# 18. Wireless Adapter Missing



\## Problem



The guest laptop could not connect wirelessly and displayed:



```text

A WMP300N or WPC300N Wireless interface is required to connect.

```



\## Root Cause



The laptop did not have a compatible wireless network module installed.



\## Solution



The laptop was powered off in the Physical tab, the correct wireless module was installed, and the laptop was powered on again.



It was then connected to:



```text

SSID:           ENT-GUEST

Authentication: WPA2-PSK

Encryption:     AES

```



\## Verification



The `Wireless0` interface appeared and successfully received a DHCP address from VLAN 90.



\## Lesson Learned



Software configuration cannot replace missing hardware. Always verify that a device has the correct network interface.



\---



\# 19. Corporate Wi-Fi Initial Packet Loss



\## Problem



The corporate laptop initially lost some packets while pinging the simulated internet server.



\## Root Cause



The network was learning ARP entries and completing wireless and routing convergence.



\## Solution



The ping was repeated after a short wait.



\## Verification



The next test showed:



```text

Packets: Sent = 4, Received = 4, Lost = 0

```



DNS also worked:



```text

nslookup intranet.entcorp.com

```



\## Lesson Learned



Repeat connectivity tests after allowing a newly connected wireless client and the network infrastructure time to learn required information.



\---



\# 🧰 Troubleshooting Command Toolkit



\## Switches



```cisco

show vlan brief

show interfaces trunk

show interfaces switchport

show etherchannel summary

show spanning-tree

show standby brief

show ip interface brief

show access-lists

show ip ssh

```



\## Routers and Layer 3 Switches



```cisco

show ip interface brief

show ip route

show ip route ospf

show ip route eigrp

show ip ospf neighbor

show ip eigrp neighbors

show ip protocols

ping <destination>

traceroute <destination>

```



\## ASA Firewall



```cisco

show interface ip brief

show switch vlan

show route

show nat

show xlate

show access-list

show arp

ping <destination>

```



\## End Devices



```text

ipconfig

ping <destination>

nslookup <domain-name>

```



\---



\# 🧠 Troubleshooting Strategy Used



When a connection failed, troubleshooting moved through the network in order:



1\. Check the device IP configuration.

2\. Ping the local default gateway.

3\. Ping the local distribution or router interface.

4\. Check VLAN membership.

5\. Check trunk status.

6\. Check HSRP status.

7\. Check the routing table.

8\. Check OSPF or EIGRP neighbors.

9\. Check redistribution.

10\. Check firewall routes.

11\. Check NAT and ACL rules.

12\. Test the final application, such as DNS or HTTP.



This prevents random changes and helps identify exactly where the packet stops.



\---



\# ✅ Final Result



All major project services were successfully verified:



\* VLAN segmentation

\* 802.1Q trunks

\* Rapid PVST+

\* LACP EtherChannel

\* HSRP gateway redundancy

\* Inter-VLAN routing

\* Centralized DHCP

\* DHCP relay

\* DNS resolution

\* Internal web access

\* OSPF routing

\* EIGRP routing

\* OSPF–EIGRP redistribution

\* Branch-to-HQ communication

\* Default route distribution

\* ASA inside, outside, and DMZ connectivity

\* Dynamic PAT

\* Static NAT

\* Public DMZ website access

\* Guest Wi-Fi isolation

\* Corporate Wi-Fi connectivity

\* SSH remote management

\* Voice VLAN separation

\* HSRP failover

\* EtherChannel member-link redundancy

\* End-to-end enterprise connectivity



The problems encountered were not wasted time. They demonstrated practical troubleshooting skills, which are among the most important abilities required from a network engineer.



