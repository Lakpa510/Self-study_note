६)	दुबै स्विचको interface Gig0/1 मा VLAN 2 access-port assign गर्ने, स्विच २ मा ping हुन्छ हुदैन हेर्ने र VLAN ५ को पनि ping  हुन्छ हुदैन हेर्ने 
```
स्विच १ र स्विच २ जोड्ने interface Gig0/1 लाई access-port assign गर्दा,
Sw1:
sw1(config)# int gig0/1
sw1(config-if)# switchport mode access
sw1(config-if)#switchport access vlan 2

sw2:
sw2(config)# int gig0/1
sw2(config-if)# switchport mode access
sw2(config-if)# switchport access vlan 2

स्विच १ र स्विच २ जोड्ने interface Gig0/1 लाई access-port assign गर्दा VLAN 2 को traffic pass भयो तर VLAN 5 को traffic pass भएन । VLAN २ को PC1 ले echo request पठाएपछि स्विच १ को interface gig0/1 मा access vlan 2 बनाएको कारण PC3 सम्म सिधै पुग्छ र echo response गर्छ ।तर VLAN 5 को traffic छुट्टै subnet को भएको हुदाँ अर्को स्विचमा जान सक्दैन । VLAN 5 को traffic pass हुनको लागि access-port vlan 5 वा trunk port बनाउनु पर्छ ।
```
७)	स्विच १ को interface gig0/1 मा VLAN 2 access-port assign गर्ने र स्विच २ को interface gig0/1 मा VLAN ५ access-port assign गर्ने र VLAN मा रहेको आ-आफ्नो PC ले एक अर्कामा ping गर्न सक्छ सक्दैन हेर्ने 
```
sw1:
sw1(config)# int gig0/1
sw1(config-if)# switchport mode access
sw1(config-if)# switchport access vlan 2

sw2
sw2(config)#int gig0/1
sw2(config-if)# switchport mode access
sw2(config-if)# switchport access vlan 5

	IPv4 को नतिजाः
VLAN 2 को case (PC1 → PC3):
PC1 को frame sw1 को Fa0/1 (VLAN 2) मा आउँछ। sw1 ले Gig0/1 access VLAN 2 बाट sw2 मा untagged पठाउँछ। sw2 ले Gig0/1 access VLAN 5 मा receive गर्ने भएकोले frame VLAN 5 मा misdeliver हुन्छ। PC3 VLAN 2 मा रहेकोले VLAN 5 मा आएको frame लाई accept गर्दैन। ARP request को reply कहिल्यै आउँदैन र ping fail हुन्छ।
VLAN 5 को case (PC2 → PC4):
PC2 को frame sw1 को Fa0/2 (VLAN 5) मा आउँछ। sw1 ले Gig0/1 हेर्छ तर Gig0/1 access VLAN 2 भएकोले VLAN 5 को frame त्यहाँबाट निस्कन नै सक्दैन र sw1 भित्रै drop हुन्छ। sw2 सम्म frame पुग्दैन र ping fail हुन्छ।
यो scenario troubleshoot गर्न निक्कै गाह्रो भयो । show interface gig0/1 switchport गरेर दुवै स्विचको access VLAN manually verify गर्नु पर्ने रहेछ ।

	IPv6 को नतिजाः (Wireshark capture)
VLAN 2 को case (PC1 → PC3):
PC1 ले PC3 को MAC Address थाहा पाउन Neighbor Solicitation (NS) message पठाउँछ । यो IPv4 को ARP जस्तै काम गर्छ तर multicast को रूपमा । NS frame sw1 gig0/1 vlan 2 बाट untagged भएर  sw2 gig0/1 मा पुग्दा vlan 5 मा misdeliver हुन पुग्छ । जस्ले गर्दा multicast collision हुन्छ । multicast collision हुनुको कारण भने मैले २ वटा IPv6 को network address बाट एउटै प्रकारको address हुने बन्ने राखेको मैले यहाँ दिएको छु । जस्मा पछाडिको २४ बिट (:3) उस्तै बन्न पुग्छ र एउटै SN multicast group मा पर्छ । vlan 5 मा यसकारण misdeliver भएर पनि PC4 ले multicast group मा परेको कारण Neighbor Advertisement replay पठाउछ । यस्लाई wireshark capture हेर्दा  PC4 ले आफ्नो packet भित्र flag: Solicited + Override राखि NA replay गरेको पाए । यस्लाई मैले ping success भयो भनेको होइन रहेछ । 
यहाँ PC1 ले Echo request PC4 को लागि multicast मा पठाउँछ । sw2 ले vlan 5 मा पाउछ र multicast एउटै भएको कारण PC4 ले packet खोलेर हेर्दा dest. IP 2006:a:aaa:1::3 भएको थाहा पाउँछ । त्यसपछि आफ्नो लागि होइन रहेछ भनेर echo repaly पठाउदैन ।
```
