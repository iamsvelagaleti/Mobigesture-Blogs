# IVR

IVR (Interactive Voice Flow) is an automated voice call flow that allows system to interact with its caller and support them based on their inputs provided to the system in the form of Voice or DTMF(Dual-tone Multi-frequency) through keypad. 

To make the IVR call more interactive and personalized, it will be deigned with different menus, sub-menus and options with the help of CCXML and VXML based digital documents which are processed with the help of Voice Gateway Engine.



## IVR System Infrastructure

As soon as call received at PSTN endpoint, it will get converted to SIP call to initiate a SIP session and start the IVR call.



<Planning to add an image here>



As shown on the picture above, SIP call initiates Voice Gateway to proceed with the call. Internally a SIP session is initiated to traverse through the CCXML using CCXML interpreter. CCXML is the core that controls the complete call flow using Voice Browser to parse through VXML documents. 

To understand more in detail, it is important to go through the below terminology and their functionality. So, let start with each components in the above diagram:

### PSTN

**P**ublic **S**witched **T**elephone **N**etwork simply called as 'telephone line' to allow telecommunication between two devices. It interconnects telephone lines, cellular networks, fiber-optic cables, and undersea telephone cables, which allow devices to connect with each other.

> Ref: https://en.wikipedia.org/wiki/Public_switched_telephone_network

### SIP

**S**ession **I**nitiation **P**rotocol is the most commonly used protocol to initiate IVR calls on voice-gateway sessions in real-time. It also allows to maintain, modify and terminate call sessions that involve video, voice, messaging and other communications applications and services between two or more endpoints on IP networks.

> Ref: https://en.wikipedia.org/wiki/Session_Initiation_Protocol#:~:text=The%20Session%20Initiation%20Protocol%20(SIP,voice%2C%20video%20and%20messaging%20applications.

### Voice Gateway

Voice gateway can be referred as Router PSTN with a standard purpose of converting telephone signals from PSTN to digital IP packets for transporting over internet based on VoIP protocols. A Voice gateway will typically support a single protocol, out of which SIP is higher accepted protocol.

Voice Gateway contains two primary components.

1. **Voice Browser** -  Is a type of browser to accept input in the form of DTMF & Voice inputs

   - As similar to web browser using HTML, Voice Browser uses VoiceXML (VXML). VXML is a dialog markup language helps to design an interactive call flow forms with leverage on usage of DTMF and Voice as the form inputs. 

     > Ref: https://voicexml.org/

2. **CCXML Engine** - To handle calls based on instructions passed through CCXML

   - CCXML (**C**all **C**ontrol e**X**tensible **M**arkup **L**anguage) is W3C standard markup language designed to control how a phone call to be answered, transferred, conferenced and more. It adds a robust call support to VoiceXML. One critical thing to understand about CCXML is, it is not a media language like VoiceXML, it just provides support to move calls around and integrate different systems.

     > Ref: https://www.w3.org/TR/ccxml/

### Voice Platform

Voice Platform is a hardware unit can be defined as switch to execute commands and logic provided on VXML and CCXML with a capability to process speech and DTMF inputs along with enabling voice application creation on it. They are also designed to interface with back-end systems. The major share holders in the market are

1. Avaya Voice Platform (AVP)
2. Genesys Voice Platform (GVP)
3. Cisco Voice Platform (CVP)

## Conclusion

With huge ROI and desire to provide more personalised assistance to the customers, companies are interested in investments towards IVR systems to assist their customers 24/7. 

Considering the customer satisfaction, it is very important to meet required expectations. And, to be successful in IVR call flow implementation, huge efforts are required in testing. When it comes to manual testing, it becomes a stress to listen similar prompts repeatedly and same repetitive inputs to be given on a each development change, which can be called as failure in this automated software world. Automation testing is made possible with emulation of call centre environment using Automation Tools for this domain.

> For the details on Automation of IVR call flow testing follow with this link:

 

