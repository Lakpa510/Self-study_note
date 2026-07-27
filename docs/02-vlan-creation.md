
१)	स्विच १ र २ को hostname क्रमशः sw1 र sw2 राख्ने
```
स्विच १
switch(config)# hostname sw1
sw1(config)#

स्विच २
switch(config)# hostname sw2
sw2(config)#
```

२)	स्विच १ र २ मा VLAN 2 देखि 10 सम्म बनाउने 
```
स्विच १/२ (दुबैमा एउटैले काम गर्छ )
sw1(config)#VLAN 2-10
sw1(config-VLAN)#exit
sw1(config) #	sw2(config)#VLAN 2-10
sw2(config-VLAN)#exit
sw2(config) #
```
३)	स्वित १ र २ मा बनाएको VLAN हरूमा VLAN २ र VLAN ५ मा PC को लागि १/१ वटा इन्टरफेस (Interface) दिने
```
स्विच १
VLAN 2 को लागि 
sw1(config)#interface f0/1
sw1(config-if)#switchport mode access
sw1(config-if)#switchport access vlan 2
sw1(config-if)#no shutdown
sw1(config-if)#exit

VLAN 5 को लागि 
sw1(config)#interface f0/2
sw1(config-if)#switchport mode access
sw1(config-if)#switchport access vlan 5
sw1(config-if)#no shutdown
sw1(config-if)#exit

स्विच २
VLAN 2 को लागि 
sw2(config)#interface f0/1
sw2(config-if)#switchport mode access
sw2(config-if)#switchport access vlan 2
sw2(config-if)#no shutdown
sw2(config-if)#exit

VLAN 5 को लागि 
sw2(config)#interface f0/2
sw2(config-if)#switchport mode access
sw2(config-if)#switchport access vlan 5
sw2(config-if)#no shutdown
sw2(config-if)#exit
```
