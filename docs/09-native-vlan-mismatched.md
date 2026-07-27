२०)	native-VLAN mismatched
```
मैले स्विच १ मा native-VLAN २ राखे र स्विच २ मा native-VLAN १० राखेर हेर्दा निम्नानुसारको नतिजा पाए ।
sw1(config-if)#switchport trunk native vlan 2
%SPANTREE-2-RECV_PVID_ERR: Received BPDU with inconsistent peer vlan id 10 on GigabitEthernet0/1 VLAN2.

%SPANTREE-2-BLOCK_PVID_LOCAL: Blocking GigabitEthernet0/1 on VLAN0002. Inconsistent local vlan.

%CDP-4-NATIVE_VLAN_MISMATCH: Native VLAN mismatch discovered on GigabitEthernet0/1 (2), with sw2 GigabitEthernet0/1 (10).

Sw1 को interface gig0/1 मा मैले native-vlan 2 बनाउदा vlan 2 को PC1 ले अर्को PC3 लाइ ping गर्न सक्दैन तर vlan 5 ले ping गर्न सक्छ । PC1 बाट ping 2006:a:aaa:1::3 गर्दा frame sw1 मा पठाउछ । PC1 बाट ping गर्दा frame SW1 मा पुग्छ। SW1 को Gig0/1 trunk port भएकोले र native VLAN 2 भएको हुदा VLAN 2 को traffic untagged पठाउँछ। SW2 ले untagged frame पाउँदा आफ्नो native VLAN 10 मा classify गर्छ, VLAN 2 हो भनेर थाहा पाउँदैन। STP ले यो PVID inconsistency detect गरेर SW2 को Gig0/1 लाई VLAN 2 को लागि block गर्छ। IPv6 को NDP ले multicast मार्फत neighbor discovery गर्ने भए पनि SW2 ले गलत VLAN मा classify गर्ने भएकोले यो पनि असफल हुन्छ। SW1 को MAC table मा SW2 को MAC address VLAN 2 अन्तर्गत देखिन्छ भने SW2 को MAC table मा VLAN 2 सम्बन्धित कुनै entry रहँदैन किनभने STP block पछि trunk पार गरेर कुनै पनि VLAN 2 frame जान सक्दैन ।
IPv4 को हकमा: PC1 ले PC3 को MAC address थाहा पाउन पहिले ARP request broadcast पठाउँछ। SW1 ले यो ARP frame लाई untagged trunk बाट SW2 मा पठाउँछ, SW2 ले VLAN 10 मा classify गर्छ। VLAN 10 मा कुनै PC नभएकोले ARP reply कहिल्यै आउँदैन, त्यसैले PC1 ले PC3 को MAC address सिक्न सक्दैन र ping असफल हुन्छ।
साथै SW1 मा native-vlan 5 बनाउदा VLAN 5 को traffic पनि untagged पठाउँछ, SW2 ले VLAN 10 मा misclassify गर्छ, STP ले SW2 को Gig0/1 लाई VLAN 5 को लागि पनि block गर्छ र PC2 ले PC4 लाई ping गर्न सक्दैन।
```
