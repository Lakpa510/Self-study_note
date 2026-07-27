मैले गरेका गल्तिहरू र सिकेका पाठहरू (Mistakes I Made & What I Learned)
१)	Trunk allowed VLAN command बारे गलत बुझाइ
```
मैले सुरुमा VLAN pruning गर्दा प्रत्येक VLAN लाई छुट्टाछुट्टै लाइनमा switchport trunk allowed vlan X गरी थप्ने प्रयास गरें, यो सोचेर कि यसले list मा थप्दै जान्छ। तर वास्तविकतामा यो command ले हरेक पटक अघिल्लो list लाई replace गर्छ, append गर्दैन। यसको नतिजा स्वरूप अन्तिम लाइनमा राखेको VLAN मात्र trunk मा बाँकी रहन्थ्यो, बाँकी सबै हराउँथे। यसबाट मैले सिकें — list थप्न चाहेमा add/remove keyword प्रयोग गर्नुपर्छ, अथवा एकैचोटि comma-separated list (vlan 2,3,5,6,7,10) दिनुपर्छ।
```
२)	 Port security violation mode बारे गलत command
```
मैले अनधिकृत device जोडिएमा port स्वतः बन्द हुनुपर्छ भनेर सोचेको थिएँ, तर command मा violation restrict राखें — जुन traffic लाई drop मात्र गर्छ, port लाई बन्द गर्दैन। पछि थाहा भयो कि port लाई स्वतः err-disable state मा लैजान चाहेमा violation shutdown नै सही command हो। यसबाट मैले violation mode का तीन प्रकार (protect, restrict, shutdown) बीचको फरक राम्रोसँग बुझें।
```
३)	VLAN 1 लाई सुरुमा बेवास्ता गरेको
```
सुरुको trunk pruning मा मैले VLAN 2,3,4,5,6,7,10 लाई मात्र फोकस गरें र default VLAN1 लाई हटाउनु पर्छ भन्ने ख्याल गरिनँ। पछि थाहा भयो कि VLAN 1 trunk मा explicitly prune नगरेसम्म सधैं रहन्छ, र यो native VLAN mismatch जस्ता attack को लागि एउटा सजिलो entry point बन्न सक्छ। यसबाट मैले default VLAN कहिल्यै management/production traffic को लागि प्रयोग नगर्नु भन्ने security best practice सिकें।
```
४)	DTP (Dynamic Trunking Protocol) negotiation बारे थाहा नभएको
```
मैले trunk हटाएर हेर्दा पनि link trunk नै रहिरह्यो सुरुमा यो किन भयो भन्ने थाहा भएन। show interface gig0/1 switchport हेरेपछि थाहा भयो कि Administrative Mode dynamic auto मा थियो, जसले गर्दा दुबै स्विचले आपसमा negotiate गरेर आफै trunk बनाइरहेका थिए। यसबाट मैले DTP negotiation कसरी काम गर्छ र किन production environment मा यसलाई switchport nonegotiate ले बन्द गर्नुपर्छ भन्ने बुझें।
```
५)	Native VLAN mismatch ले के असर पार्छ भन्ने व्यावहारिक रूपमा नबुझेको
सैद्धान्तिक रूपमा native VLAN mismatch खराब हो भन्ने थाहा भए पनि, यसले वास्तवमा के गर्छ भन्ने मलाई थाहा थिएन। दुई स्विचमा फरक native VLAN राखेर परीक्षण गर्दा मात्र मैले %CDP-4-NATIVE_VLAN_MISMATCH जस्ता error message देखें र थाहा पाएँ कि STP ले port लाई block गर्न सक्छ अनि ping समेत असफल हुन्छ। Theory भन्दा hands-on test ले बढी स्पष्ट बुझाइ दियो ।

६)	Voice VLAN मा PortFast/BPDU Guard को आवश्यकता नबुझेको
```
सुरुमा मैले voice VLAN configure गर्दा PortFast र BPDU Guard राख्नु आवश्यक छ भन्ने ख्याल गरिनँ। पछि बुझें कि यी नभए स्विचले ती port लाई normal STP listening/learning state बाट गुज्रन बाध्य पार्छ (करिब ३०-५० सेकेन्ड ढिलो), जुन IP फोन जस्ता end-device को लागि अनावश्यक हो। साथै BPDU Guard ले अनधिकृत switch जडान हुनबाट जोगाउँछ।
```
७)	VLAN ले network लाई segment गर्छ एउटा VLAN को PC ले अर्को VLAN को PC सँग Layer 3 device बिना कहिल्यै कुराकानी गर्न सक्दैन, trunk भए पनि नभए पनि।
