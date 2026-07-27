१२)	स्विचहरूविचमा जोडिएको तारलाई Trunk config. गर्ने र VLAN 10 लाई native-VLAN बनाउने 
```
Sw1(config)# interface g0/1
sw1(config-if)# switchport trunk encapsulation dot1q
sw1(config-if)# switchport mode trunk
sw1(config-if)# switchport trunk native VLAN 10

sw2(config)# interface g0/1
sw2(config-if)# switchport trunk encapsulation dot1q
sw2(config-if)# switchport mode trunk
sw2(config-if)# switchport trunk native VLAN 10 
```

१३)	स्विच १ को interface Gig0/1 मा Trunk हटाएर हेर्ने के हुन्छ 
```
स्विच १ को interface gig0/1 मा रहेको Trunk हटाउदा समेत Trunking ले काम गर्यो । मैले यस्तो किन भएको रहेछ भनेर show interface gig0/1 switchport गरेर हेर्दा Administrative Mode: dynamic auto र Operational Mode: trunk रहेको पाए । यसको अर्थ मैले Trunk हटाउदा समेत आपसि समझ्दारीमा मिलेर Trunk बन्छ । यस्लाई हटाउनु पर्छ किनकि यो switch hardening को महत्वपुर्ण पाटो हो ।
Sw1(config)# interface gig0/1
sw1(config-if)#switchport nonegotiate

Sw2(config)# interface gig0/1
Sw2(config-if)#switchport nonegotiate
यति गरियो भने आफै trunking बनाउन सक्दैन ।
```
१४)	Trunk port र native-VLAN भनेको के हो बुझ्ने
```
Trunk port: multiple VLAN traffic carry गर्छ, फरक VLAN संग कुराकानी गराउछ, 802.1Q tag प्रयोग गर्छ
Native-VLAN: विना VLAN-tag traffic pass गर्छ, VLAN 10 मा भएको interfaces लाई shutdown गर्यो भने पनि Native-VLAN को काम गर्छ र Native-VLAN लाई delete गर्नु हुन्न ।
```
