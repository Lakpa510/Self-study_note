यस ल्याबमा Topology निर्माण सम्पन्न गरि मैले गर्ने कार्यहरूः
```
१)	स्विच १ र २ को hostname क्रमशः sw1 र sw2 राख्ने । 
२)	स्विच १ र २ मा VLAN 2 देखि 10 सम्म बनाउने,
३)	स्विच १ र २ मा बनाएको VLAN हरूमा VLAN २ र VLAN ५ मा PC को लागि १/१ वटा इन्टरफेस (Interface) दिने
४)	प्रत्येक PC मा VLAN अनुसार Static IPv4, IPv6 Address दिने 
५)	IP Address दिएपश्चात sw1 को VLAN २ मा राखेको PC1 ले sw2 को VLAN २ मा राखेको PC3 विच सम्पर्क हुन सक्छ सक्दैन भनेर ping 192.168.1.11/ ping  2006:A:AAA:1::3 गर्ने र VLAN ५ को पनि यसरी नै हेर्ने
६)	स्विच १ को interface g0/1 मा VLAN २ access-port assign गर्ने, स्विच २ मा ping हुन्छ हुदैन हेर्ने र VLAN ५ को पनि ping  हुन्छ हुदैन हेर्ने 
७)	स्विच १ को interface g0/1 मा VLAN २ access-port assign गर्ने र स्विच २ को interface g0/1 मा VLAN ५ access-port assign गर्ने र VLAN मा रहेको आफ्नो PC ले एक अर्कामा ping गर्न सक्छ सक्दैन हेर्ने 
८)	sw1 र sw2 मा बाकि रहेको port लाई VLAN 10 मा राखेर सबैलाई बन्द गर्ने
९)	VLAN ७ मा voice VLAN बनाउने र VLAN २ को interface संगै राख्ने 
१०)	Switch-Switch विच जडान गरिएको तारमा trunk config. नहुदाँ पनि STP र CDP/LLDP ले काम गर्छ गर्दैन हेर्ने 
११)	STP (Spanning Tree Protocol) बारे केहि जानकारी
१२)	स्विचहरूविचमा जोडिएको तारलाई Trunk config. गर्ने र VLAN 10 लाई native-VLAN बनाउने
१३)	स्विच १ को interface g0/1 मा Trunk हटाएर हेर्ने 
१४)	Trunk port र native-VLAN बारे केहि जानकारी
१५)	स्विचहरूमा VLAN 2,3,5,6,7,10 लाई मात्र जाने बनाउने र VLAN  4,8 र 9 लाई बन्द गर्ने
१६)	VLAN 1 लाई किन हटाउने 
१७)	VLAN ५ को traffic लाई स्विच १ देखि २ मा जान नदिने (prued) बनाउने र अर्को तर्फ रहेको PC4 लाई ping गर्ने/हेर्ने र पुनः traffic जाने बनाउने
१८)	VLAN ५ लाई delete गरेर हेर्ने/ बुझ्ने र पुनः VLAN ५लाइ पहिलेको जस्तो काम गर्न बनाउने
१९)	native-VLAN लाई पनि delete गरेर हेर्ने/ बुझ्ने
२०)	native-VLAN mismatched
२१)	Port security
२२)	अन्त्यमा Troubleshoot गर्ने
```
