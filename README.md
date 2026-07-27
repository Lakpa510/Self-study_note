Layer 2 VLAN Switching Lab
```
Configuration, Security Hardening & Troubleshooting (Dual-Stack IPv4/IPv6)
```
```
Author: Lakpa Sherpa
Platform: Cisco Packet Tracer + PnetLab + Wireshark
Level: CCNA / Associate-level Network Engineering
```



Overview
```
यो lab मैले Layer 2 VLAN switching को concept हात्तेकाम (hands-on) मार्फत सिक्नको लागि बनाएको हो। VLAN creation
देखि trunk configuration, security hardening, र real-packet Wireshark analysis सम्मको पूरै journey यहाँ
documentation गरिएको छ।

यो lab ले केवल "command run गर्ने" भन्दा बढी — किन हुन्छ, के हुन्छ, र गल्ती भएमा के देखिन्छ — भन्ने कुरामा ध्यान दिएको छ।
```

Lab Topology
```
  [PC1]──Fa0/1──┐                    ┌──Fa0/1──[PC3]
  VLAN 2        │                    │          VLAN 2
                |                    |
  1.10 /::2   Sw1 ──Gig0/1────  Gig0/1 Sw2     1.11 /::3
            0004.9ACB.C9D5      0001.9622.6DBD
                 │                    │
  VLAN 5         |                    |
  2.10 / ::2     │                    │
  [PC2]──Fa0/2 ──┘                    └──Fa0/2──[PC4]
                                                 VLAN 5
                                                 2.11 /::3
 ```
```
Device       Interface     VLAN        IPv4              IPv6 
PC1---Sw1     Fa0/1        VLAN 2  192.168.1.10/24  2006:A:AAA:1::2/64
PC2---Sw1     Fa0/2        VLAN 5  192.168.2.10/24  2006:A:AAA:2::2/64
PC3---Sw2     Fa0/1        VLAN 2  192.168.1.11/24  2006:A:AAA:1::3/64
PC4---Sw2     Fa0/2        VLAN 5  192.168.2.11/24  2006:A:AAA:2::3/64
```
```
VLAN      Network                IPv6 Prefix 
VLAN 2    192.168.1.0/24        2006:A:AAA:1::/64
VLAN 5    192.168.2.0/24        2006:A:AAA:2::/64
VLAN 7    Voice VLAN            —
VLAN 10   Native + Parking Lot  —
```

Lab Objectives
```
#  TopicKey                           Concept
1  Hostname config                    Basic switch setup
2  VLAN 2–10 creation                 VLAN database
3  Access port assignment             VLAN–port binding
4  Dual-stack IP addressing           IPv4 + IPv6
5  Pre-trunk ping test                Broadcast domain isolation
6  Same VLAN access on trunk link     Single VLAN access port behavior
7  Mismatched VLAN access ports       Silent drop + NDP collision (advanced)
8  Unused port hardening              Parking lot VLAN
9  Voice VLAN + PortFast/BPDU Guard   VoIP security
10 STP/CDP/LLDP without trunk         Protocol independence from port mode
11 STP root bridge election           PVST+ per-VLAN instance
12 Trunk configuration + Native VLAN  802.1Q, dot1q encapsulation
13 DTP auto-negotiation discovery     switchport nonegotiate hardening
14 Trunk port & Native VLAN concepts  Theory + verification
15 VLAN pruning on trunk              Allowed VLAN list management
16 VLAN 1 removal                     Attack surface reduction
17 VLAN 5 traffic pruning             Dynamic trunk control
18 VLAN delete & restore              Inactive port behavior
19 Native VLAN delete                 VLAN table impact
20 Native VLAN mismatch               STP PVID error + MAC table analysis
21 Port security                      MAC-based access control
22 Troubleshooting                    show commands + Wireshark
```


Tools Used
```
Tool                          Purpose
Cisco Packet Tracer           Initial lab build & config    
PnetLab                       Real packet capture environment
Wireshark                     Frame-level analysis (ARP, NDP, 802.1Q, STP BPDU)
```
Future Labs (Planned)
```
Layer 3 inter-VLAN routing (Router-on-a-stick / SVI)
DTP deep-dive lab
DHCP Snooping + Dynamic ARP Inspection (DAI)
VTP mode comparison (server / client / transparent)
EtherChannel (LACP/PAgP)
```

```
यो lab पहिलो प्रयास हो — गल्ती गर्दै, Wireshark मा frame हेर्दै, र root cause पत्ता लगाउँदै सिकिएको हो।
```
