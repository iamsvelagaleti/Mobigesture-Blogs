# What is IVR?

IVR (Interactive Voice Flow) is a modern sophisticated system to generate an automated voice call flow that allows system to interact with its caller and answer them based on their input selection provided to the system in the form of Voice or DTMF(Dual-tone Multi-frequency) through keypad. 

To make the IVR call interactive and personalized, it will be deigned with the helps of different menus, sub-menus and options to choose from. This Customer eXperience(CX) can be designed in multiple ways. Out of all available below are the most popular and adapted methodologies in the IT industry

1. With CCXML and VXML based digital documents which are processed in a Voice Gateway Engine to generate an IVR call.
2. [Freeswitch](https://freeswitch.com/) or [Asterisk](https://www.asterisk.org/)

In either of the possibility, the basic flow for the IVR call will be



When a call received to the call center, it will be converted to SIP call and flow through the call flow by requesting DTMF or voice-based inputs from the caller. If Caller desired and opted for connected to the call center agent, then call will be redirected to the Agent.




## IVR System Infrastructure

Now, let us understand the basic infrastructure required to setup an IVR call flow with the help of VXML & CCXML. When a caller called an IVR number, it hits PSTN endpoint, which will get converted to a SIP call by Voice Platform Switches (Current Market leaders are [Avaya](https://support.avaya.com/products/P0979/voice-portal), [Genesys](https://docs.genesys.com/Documentation/GVP) & [Cisco](https://www.cisco.com/c/en/us/support/customer-collaboration/unified-customer-voice-portal-11-6/model.html)). This SIP call initiates a SIP session on CCXML Engine and follows the call flow as designed in CCXML.

![IVR Call Flow Architecture.png](https://github.com/iamsvelagaleti/Mobigesture-Blogs/blob/master/IVR%20Call%20Flow%20Architecture/IVR%20Call%20Flow%20Architecture.png?raw=true)

In reference with the above picture, SIP call is generated at Voice Platform Switch and transferred to  Voice Gateway. Internally a SIP session is initiated for the SIP Call and it starts traversing through the CCXML using CCXML engine. CCXML Engine is the core that controls the complete call flow based on the instructions provided in CCXML document. In the call flow, CCXML engine initiates Voice Browser to parses through VXML documents and communicate with the caller. When the caller interested to connect with the Agent then CCXML engine connects to an Agent from Agent Pool

To understand more in detail, it is important to go through the below terminology and their functionality. So, let start with each components in the above diagram:

### SIP

**S**ession **I**nitiation **P**rotocol is a signaling text-based protocol used to make IVR calls using **Internet Telepony** on voice-gateway sessions in real-time to control multimedia communication sessions. It also allows to maintain, modify and terminate call sessions that involve video, voice, messaging and other communications applications and services between two or more endpoints on IP networks.

> Ref: https://en.wikipedia.org/wiki/Session_Initiation_Protocol#:~:text=The%20Session%20Initiation%20Protocol%20(SIP,voice%2C%20video%20and%20messaging%20applications.

There are 2 types of SIP Messages: request and response. 

**SIP request** is sent by the used with a method to define the nature of the request and the Request-URI. There are 14 ways of SIP request, that can be sent in an IP call.



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

With huge ROI and desire to provide more personalized assistance to the customers, companies are interested in investments towards IVR systems to assist their customers 24/7. 

Considering the customer satisfaction, it is very important to meet required expectations. And, to be successful in IVR call flow implementation, huge efforts are required in testing. When it comes to manual testing, it becomes a stress to listen similar prompts repeatedly and same repetitive inputs to be given on a each development change, which can be called as failure in this automated software world. Automation testing is made possible with emulation of call centre environment using Automation Tools for this domain.

> For the details on Automation of IVR call flow testing follow with this link:

 

