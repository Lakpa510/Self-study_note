१५)	स्विचहरूमा VLAN 2,3,5,6,7,10 लाई मात्र जाने बनाउने र VLAN 1, 4, 8 र 9 लाई बन्द गर्ने
```
यसको लागि स्विचमा pruned option हुन्छ, जस्ले sw1 देखि sw2 विच सम्पर्क स्थापना गर्न बन्द गर्छ । 
sw1(config)# int g0/1
sw1(config-if)# switchport trunk allowed VLAN none
sw1(config-if)# switchport trunk allowed vlan 2,3,5,6,7,10

sw2(config)# int g0/1
sw2(config-if)# switchport trunk allowed VLAN none
sw2(config-if)# switchport trunk allowed vlan 2,3,5,6,7,10
```
१६)	VLAN 1 लाई किन हटाउने भन्ने बारेमा

```
VLAN 1 डिफल्ट VLAN हो र यसलाई explicitly prune नगरेसम्म trunk link मा सधैं रहन्छ। यो प्रायः attack surface को रूपमा प्रयोग हुन्छ विशेष गरी native VLAN mismatch attack मा, जहाँ कुनै attacker ले untagged traffic पठाएर VLAN boundary बाइपास गर्ने प्रयास गर्न सक्छ। यसैले VLAN 1 लाई trunk बाट हटाउनु र management traffic को लागि कहिल्यै प्रयोग नगर्नु एउटा standard hardening practice हो।

Sw1(config)# interface gig0/1
Sw1(config-if)# switchport trunk allowed vlan remove 1

Sw2(config)# interface gig0/1
Sw2(config-if)# switchport trunk allowed vlan remove 1
```
१७)	VLAN 5 को traffic लाई स्विच १ देखि २ मा जान नदिने (pruned) बनाउने र अर्को तर्फ रहेको PC4 लाई ping गर्ने/हेर्ने र पुनः traffic जाने बनाउने
```
Sw1(config)# int g0/1
sw1(config-if)# switchport trunk allowed VLAN remove 5
यस्तो गर्ने वित्तिकै VLAN 5 को switchport बन्द हुन्छ र  traffic लाइ अर्को switch जान सक्दैन ।

	पुनः traffic pass हुने बनाउनको लागि
	Sw1(config)# int g0/1
sw1(config-if)# switchport trunk allowed VLAN add 5
```

१८)	VLAN 5 लाई delete गरेर हेर्ने/ बुझ्ने र पुनः VLAN ५ बनाउने
```
स्विच १ मा मैले VLAN 5 delete गर्दा VLAN 5 मा जोडिएको PC2 को port स्वत बन्द भयो । 
Show VLAN brief गरेर हेर्दा VLAN 5 देखाएन । जस्ले गर्दा VLAN 5 ले अर्को स्विच २ मा रहेको VLAN 5 संग सम्पर्क स्थापना गर्न सक्दैन । delete गरेपछि show interface trunk गरेर हेर्दा VLAN 5 लाई pruned गरिएको भनि देखायो । show int f0/2 switchport गरेर हेर्दा access mode VLAN: 5 (Inactive) भनि देखायो । मैले स्विच २ मा रहेको PC4 देखि PC2 मा ping गरेर हेर्दा ping fail भयो । VLAN 5 पुनः सञ्चालनको लागि मैले बनाएर दुवै तर्फ ping गरेर हेरे र सम्पर्क स्थापना हुन पुग्यो र पहिलेको जस्तो काम गर्न सुरू गर्यो ।
```
१९)	native-VLAN लाई पनि delete गरेर हेर्ने/ बुझ्ने
```
VLAN 10, Native-VLAN लाई delete गर्दा VLAN table बाट सबै port सहित हट्छ, pruned हुन्छ, access mode VLAN: 10 (INACTIVE) भनि देखाउछ ।
```
