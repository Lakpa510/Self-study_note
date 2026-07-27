२१)	Port security (L2 switch hardening)
```
sw1(config)# int range f0/1-2
sw1(config-if)# switchport port-security
sw1(config-if)# switchport port-security maximum 2
sw1(config-if)# switchport port-security violation restrict
sw1(config-if)# switchport port-security mac-address sticky

sw2(config)# int range f0/1-2
sw2(config-if)# switchport port-security
sw2(config-if)# switchport port-security maximum 2
sw2(config-if)# switchport port-security violation restrict
sw2(config-if)# switchport port-security mac-address sticky
यो L2-security को लागि हो । यस्ले Topology  भित्र अनधिकृत डिभाइसले पहुच स्थापना गर्न खोजेमा यसले violating traffic लाई drop गर्छ, एउटा log/SNMP trap पठाउने बनाउन सकिन्छ, र counter बढाउँछ, तर port भने up नै रहन्छ। अन्य सर्तहरू पनि रहेको छ, यहाँ मैले restrict को lab गरेको छु ।
यसको परिक्षणको लागि मैले PC1 जस्तै IPv4/IPv6 एउटै राखेर PC100 sw1 को interface fa0/2 जडान गरे । यसो गर्दा PC3 संग ping गर्दा fail  भयो ।
```
