९)	VLAN ७ मा voice VLAN बनाउने र VLAN २ को interface संगै राख्ने
```
Voice VLAN 
sw1(config)#interface f0/1
sw1(config-if)# switchport voice VLAN 7
sw1(config-if)# spanning-tree portfast
sw1(config-if)# spanning-tree bpduguard enable

sw2(config)# interface f0/1
sw2(config-if)# switchport voice VLAN 7
sw2(config-if)# spanning-tree portfast
sw2(config-if)# spanning-tree bpduguard enable
यो interface मा VLAN 2 र VLAN 7 दुबैको traffic लाई pass गर्न सकिन्छ । यसमा portfast र bpduguard enable राख्नु कारण भने voice VLAN को लागि स्विचले ५० सेकेन्ड समय लगाउदैन र अनधिकृत डिभाइसले BPDU पठाएमा VLAN को port स्वत बन्द (err-disable) गर्छ । 
```
```
अब err-disable भएको अवस्थामा के गर्ने?
यस्तो अवस्था सृजना भएमा निम्नानुसारको कार्य गर्ने,
sw1(config)#interface f0/1
sw1(config-if)#shutdown
sw1(config-if)# no shutdown
```
