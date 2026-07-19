Layer 2 VLAN Switching Lab

Configuration, Security Hardening & Troubleshooting (Dual-Stack IPv4/IPv6)


Author: Lakpa Sherpa
Platform: Cisco Packet Tracer + PnetLab
Level: CCNA / Associate-level Network Engineering
GitHub: github.com/lakpa510




Overview

यो lab मैले Layer 2 VLAN switching को concept हात्तेकाम (hands-on) मार्फत सिक्नको लागि बनाएको हो। VLAN creation देखि trunk configuration, security hardening, र real-packet Wireshark analysis सम्मको पूरै journey यहाँ documentation गरिएको छ।

यो lab ले केवल "command run गर्ने" भन्दा बढी — किन हुन्छ, के हुन्छ, र गल्ती भएमा के देखिन्छ — भन्ने कुरामा ध्यान दिएको छ।


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

Key Findings & Highlights

1. Pre-trunk ping failure
VLAN create गरेपछि स्विचले छुट्टै broadcast domain बनाउँछ। Trunk port नभएसम्म VLAN ID tag गरेर पठाउन सकिँदैन — frame drop हुन्छ।

2. Mismatched Access Port — Silent Drop (Step 7)
SW1 Gig0/1 = VLAN 2, SW2 Gig0/1 = VLAN 5 राख्दा:


VLAN 2 traffic → SW2 मा VLAN 5 मा misdeliver हुन्छ
VLAN 5 traffic → SW1 भित्रै drop हुन्छ (SW2 सम्म पुग्दैन)
STP ले कुनै error detect गर्दैन — यसैले native VLAN mismatch भन्दा troubleshoot गर्न गाह्रो


3. NDP Solicited-Node Multicast Collision (Wireshark Finding)

Step 7 मा PC1 → PC3 IPv6 ping गर्दा PC4 ले NA reply दियो किनभने:

PC3: 2006:A:AAA:1::3 → Solicited-Node: ff02::1:ff00:0003
PC4: 2006:A:AAA:2::3 → Solicited-Node: ff02::1:ff00:0003
                                        ↑ last 24 bits उस्तै!

PC1 को NDP cache मा PC4 को MAC बस्यो तर ping fail नै रह्यो। यो IPv4 को ARP failure भन्दा बढी misleading छ।


4. Native VLAN Mismatch (Step 20)

%CDP-4-NATIVE_VLAN_MISMATCH: Native VLAN mismatch discovered on
GigabitEthernet0/1 (2), with sw2 GigabitEthernet0/1 (10).

%SPANTREE-2-BLOCK_PVID_LOCAL: Blocking GigabitEthernet0/1 on VLAN0002.

- SW1: native VLAN 2 → VLAN 2 traffic untagged पठाउँछ
- SW2: native VLAN 10 → untagged frame लाई VLAN 10 मा classify गर्छ
- STP ले SW2 Gig0/1 लाई VLAN 2 को लागि block गर्छ
- VLAN 5 भने tagged भएकोले ping success हुन्छ


5. DTP Auto-negotiation (Step 13)
Trunk हटाउँदा पनि trunk बन्यो किनभने dynamic auto mode मा DTP ले negotiate गर्छ। Fix:
sw1(config-if)# switchport nonegotiate

Mistakes I Made & What I Learned

#    गल्ती                                                                 सिकाइ
1   switchport trunk allowed vlan X लाई append ठाने                प्रत्येक call ले list replace गर्छ — add keyword चाहिन्छ
2   Port security मा violation restrict राखेर "port बन्द हुन्छ" भनें    restrict = drop + log, port up नै रहन्छ। बन्द गर्न shutdown चाहिन्छ
3   VLAN 1 लाई trunk मा ignore गरें                                 Default VLAN = attack surface, explicitly prune गर्नुपर्छ 
4   Trunk हटाउँदा पनि trunk बन्यो — थाहा भएन                         DTP dynamic auto ले auto-negotiate गर्छ — nonegotiate राख्नुपर्छ
5   Native VLAN mismatch को effect theoretically थाहा थियो         Lab गरेपछि STP block, MAC table impact, ping behavior — सबै व्यावहारिक रूपमा बुझें
6   Voice VLAN मा PortFast/BPDU Guard थपिनँ                        End-device port मा STP 30–50 sec delay हुन्छ — PortFast अनिवार्य हो


Troubleshooting Commands

VLAN verification
show vlan brief
show interface trunk
show interface f0/1 switchport

# STP
show spanning-tree vlan 2
show spanning-tree vlan 5

# Discovery protocols
show cdp neighbors detail
show lldp neighbors detail

# Security
show port-security interface f0/1
show port-security address

# IPv6 / NDP
show ipv6 neighbors

# MAC table
show mac address-table
show mac address-table vlan 2

# Ping tests
ping 192.168.1.11
ping 2006:A:AAA:1::3


Wireshark Captures (PnetLab)

FileInterfaceWhat to observe01-pre-trunk-arp-fail.pcapSW1 Gig0/1ARP broadcast — no reply02-post-trunk-dot1q-tag.pcapSW1 Gig0/1802.1Q tag, VLAN ID visible03-native-vlan-mismatch.pcapSW2 Gig0/1Untagged frame → wrong VLAN04-ndp-solicited-node-collision.pcapSW1 Fa0/1PC4 ले PC1 को NS को reply दिएको05-ndp-vs-arp.pcapSW1 Fa0/1NDP multicast vs ARP broadcast06-port-security-violation.pcapSW1 Fa0/2MAC violation — restrict mode


💡 Notable: 04-ndp-solicited-node-collision.pcap मा PC3 र PC4 को IPv6 address को last 24 bits (::3) उस्तै भएकोले Solicited-Node Multicast collision भयो — PC4 ले NA reply दियो तर ping fail नै रह्यो। NDP cache corrupt भएको show ipv6 neighbors बाट verify गर्न सकिन्छ।




Repository Structure

L2-VLAN-Switching-Lab/
├── README.md
├── topology/
│   └── lab.pkt      # Packet Tracer file
├── configs/
│   ├── sw1-final-config.txt
│   └── sw2-final-config.txt
├── docs/
│   ├── 01-vlan-creation.md
│   ├── 02-access-ports.md
│   ├── 03-trunk-and-native-vlan.md
│   ├── 04-pruning.md
│   ├── 05-voice-vlan.md
│   ├── 06-security-hardening.md
│   └── 07-troubleshooting.md
├── captures/
│   ├── 01-pre-trunk-arp-fail.pcap
│   ├── 02-post-trunk-dot1q-tag.pcap
│   ├── 03-native-vlan-mismatch.pcap
│   ├── 04-ndp-solicited-node-collision.pcap
│   ├── 05-ndp-vs-arp.pcap
│   └── 06-port-security-violation.pcap
└── screenshots/
    ├── ping-fail-pre-trunk.png
    ├── ping-success-post-trunk.png
    ├── show-trunk-output.png
    ├── native-vlan-mismatch-error.png
    └── wireshark-dot1q-tag.png


Tools Used

ToolPurposeCisco Packet TracerInitial lab build & configPnetLabReal packet capture environmentWiresharkFrame-level analysis (ARP, NDP, 802.1Q, STP BPDU)

Future Labs (Planned)
Layer 3 inter-VLAN routing (Router-on-a-stick / SVI)
DTP deep-dive lab
DHCP Snooping + Dynamic ARP Inspection (DAI)
VTP mode comparison (server / client / transparent)
EtherChannel (LACP/PAgP)



यो lab पहिलो प्रयास हो — गल्ती गर्दै, Wireshark मा frame हेर्दै, र root cause पत्ता लगाउँदै सिकिएको हो।
