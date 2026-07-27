४)	प्रत्येक PC मा VLAN अनुसार IPv4, IPv6 Address दिने
```
PC1: IPv4 - 192.168.1.10/24 | IPv6 - 2006:A:AAA:1::2/64
PC2: IPv4 - 192.168.2.10/24 | IPv6 - 2006:A:AAA:2::2/64
PC3: IPv4 - 192.168.1.11/24 | IPv6 - 2006:A:AAA:1::3/64
PC4: IPv4 - 192.168.2.11/24 | IPv6 - 2006:A:AAA:2::3/64

IPv6: 2006:A:AAA:1::2/64 (compressed form )
2006:000A:0AAA:0001:0000:0000:0000:0002/64
वास्तवमा IPv6 को format यो हो तर सजिलो, छिटो बुझ्नको लागि compressed form प्रयोग गरिन्छ ।
```
५)	IP Address दिएपश्चात sw1 को VLAN 2 मा राखेको PC1 ले sw2 को VLAN 2 मा राखेको PC3 विच सम्पर्क हुन सक्छ सक्दैन भनेर ping 192.168.1.11/ ping  2006:A:AAA:1::3 गर्ने र VLAN ५ को पनि यसरी नै हेर्ने
```
-	यसरी स्विच १ को VLAN २ 192.168.1.10 देखि  स्विच २ को VLAN २ 192.168.1.11  मा रहेको PC लाई ping गरेर हेर्दा निम्नानुसारको नतिजा दिन्छ ।
C:\>ping 192.168.1.11
Pinging 192.168.1.11 with 32 bytes of data:
Request timed out.
Request timed out.
Request timed out.
Request timed out.
Ping statistics for 192.168.1.11:
Packets: Sent = 4, Received = 0, Lost = 4 (100% loss)
```
```
ping 2006:A:AAA:1::3 (सबै fail हुन्छ )
Pinging 2006:A:AAA:1::3 with 32 bytes of data:
Request timed out.
Request timed out.
Request timed out.
Request timed out.
```
```
स्विचमा VLAN बनाई सकेपछि छुट्टै Broadcast domain मा विभाजन हुन्छ । विभाजन भएपछि एक स्विचले अर्को स्विचमा VLAN traffic पठाउदा प्रत्येक VLAN को ID tag गरेर पठाउन trunk port हुनु पर्छ । Multiple VLAN को traffic बोक्ने काम trunk ले गर्छ । यसमा VLAN tag गरेर पठाउनको लागि trunk port नभएको कारणले गर्दा स्विच १ मा रहेको PC1 ले स्विच २ मा रहेको PC3 को MAC Address थाहा पाउन ARP request गर्दा स्विच १ ले PC3 सम्म पठाउन सक्दैन । यसरी नसकेपछि Frame पुनः ARP response लिएर PC1 मा फर्किदैन र सम्पर्क टुट्न पुग्छ ।
IPv6 ले ARP को साटो NDP (Neighbor Discovery Protocol) प्रयोग गर्छ, जुन multicast-based हो (broadcast होइन), तर switch ले यसलाई पनि VLAN-bounded तरिकाले नै forward गर्छ — त्यसैले IPv4 जस्तै trunk नभएसम्म VLAN 2 का दुई PC बीच NDP/ping दुवै fail हुन्छन्।
```
