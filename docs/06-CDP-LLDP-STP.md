१०)	Switch-Switch विच जडान गरिएको तारमा trunk config. नहुदाँ पनि STP र CDP/LLDP ले काम गर्छ गर्दैन हेर्ने
```
Trunk port नहुदा पनि STP र CDP/LLDP ले काम गर्छ । यसको कारण निम्नानुसार रहेको छ ।
CDP (Cisco Discovery Protocol) र LLDP (Link Layer Discovery Protocol) दुवै link-layer discovery protocol हुन्, जसले port trunk छ कि access भन्ने कुराको परवाह नगरी native/untagged frame मा चल्छन्। यी protocol s VLAN tagging प्रक्रियामा निर्भर नभई सिधै physical link मा directly सञ्चार गर्छन्, त्यसैले port को mode (trunk वा access) जे भए पनि यिनीहरू सधैं काम गर्छन्।
STP (Spanning Tree Protocol) भने फरक तरिकाले काम गर्छ Cisco को PVST+ (Per-VLAN Spanning Tree Plus) ले प्रत्येक VLAN को लागि छुट्टाछुट्टै STP instance चलाउँछ, port ले जुनसुकै VLAN(हरू) carry गरिरहेको हुन्छ, त्यसैमा आधारित भएर काम गर्छ।
Access port मा हुँदा त्यो port ले एउटा मात्र VLAN carry गरिरहेको हुन्छ, त्यसैले STP ले त्यही एउटा VLAN को लागि मात्र आफ्नो topology निर्माण गरिरहेको हुन्छ। Trunk नभए पनि STP ले काम गर्न छाड्दैन — किनकि STP त्यो specific VLAN संग बाँधिएको हुन्छ, port को trunk/access mode संग होइन।
```

११)	STP (Spanning Tree Protocol) बारे केहि जानकारी
```
यो माथिको Topology मा STP (Spanning Tree Protocol) स्वत चलेको अवस्था छ जस्ले L2 मा traffic Loop हुन दिदैन । Loop prevention (STP) को आफ्नै कार्य प्रकृति रहेको छ जस्मा Root/non-Root switch हुने गर्छ । यसमा दुबै स्विचको priority value 32768 समान हुना गर्दा स्विच २ को bridge-id सानो भएकोले Root switch बन्न जान्छ ।
```
