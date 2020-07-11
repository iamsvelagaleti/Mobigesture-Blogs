# What is IVR?

IVR (Interactive Voice Flow) is a modern sophisticated system to generate an automated voice call flow that allows system to interact with its caller and answer them based on their input selection provided to the system in the form of Voice or DTMF(Dual-tone Multi-frequency) through keypad. 

To make the IVR call interactive and personalized, it will be deigned with the helps of different menus, sub-menus and options to choose from. This Customer eXperience(CX) can be designed in multiple ways. Out of all available below are the most popular and adapted methodologies in the IT industry

1. With CCXML and VXML based digital documents which are processed in a Voice Gateway Engine to generate an IVR call.
2. [Freeswitch](https://freeswitch.com/) or [Asterisk](https://www.asterisk.org/)

In either of the possibility, the basic flow for the IVR call will be

![IVR Call Flow](https://github.com/iamsvelagaleti/Mobigesture-Blogs/blob/master/IVR%20Call%20Flow%20Architecture/IVR%20Call.png)

When a call received at the call center from telephone network, it will be converted to SIP call by Voice Platform Switches and flow through the call flow by responding to audio prompts through DTMF/voice inputs. If Caller is desired and opted to speak with the call center agent, then call will be routed to the Agent.

In this blog, let us have an high level overview on the IVR system infrastructure designed based on VXML and CCXML running on on-premise Voice Gateway.


## IVR System Infrastructure

Now, let us understand the basic infrastructure required to setup an IVR call flow with the help of VXML & CCXML. When a caller called an IVR number, it hits PSTN endpoint, which will get converted to a SIP call by Voice Platform Switches (Current Market leaders are [Avaya](https://support.avaya.com/products/P0979/voice-portal), [Genesys](https://docs.genesys.com/Documentation/GVP) & [Cisco](https://www.cisco.com/c/en/us/support/customer-collaboration/unified-customer-voice-portal-11-6/model.html)). This SIP call initiates a SIP session on CCXML Engine and follows the call flow as designed in CCXML.

![IVR Call Flow Architecture.png](https://github.com/iamsvelagaleti/Mobigesture-Blogs/blob/master/IVR%20Call%20Flow%20Architecture/IVR%20Call%20Flow%20Architecture.png?raw=true)

In reference with the above picture, SIP call is generated at Voice Platform Switch and transferred to  Voice Gateway. Internally a SIP session is initiated for the SIP Call and it starts traversing through the CCXML using CCXML engine. CCXML Engine is the core that controls the complete call flow based on the instructions provided in CCXML document. In the call flow, CCXML engine initiates Voice Browser to parses through VXML documents and communicate with the caller. When the caller interested to connect with the Agent then CCXML engine connects to an Agent from Agent Pool

To understand more in detail, it is important to go through the below terminology and their functionality. So, let start with each components in the above diagram:

### SIP

**S**ession **I**nitiation **P**rotocol is a signaling text-based protocol used to make IVR calls using **Internet Telepony** on voice-gateway sessions in real-time to control multimedia communication sessions. It also allows to maintain, modify and terminate call sessions that involve video, voice, messaging and other communications applications and services between two or more endpoints on IP networks.

> Ref: https://en.wikipedia.org/wiki/Session_Initiation_Protocol#:~:text=The%20Session%20Initiation%20Protocol%20(SIP,voice%2C%20video%20and%20messaging%20applications.

There are 2 types of SIP Messages: request and response. 

**SIP request** will be sent by the call-agent with a method to define the nature of the request and the Request-URI. There are 14 ways of SIP request, that can be sent in an IP call. Out out all, below are the most used requests in a SIP Call

```
REGISTER	-	Used to register the URI as a *Contact* in the network 

INVITE		-	Used to establish a call, it will sent from the client to the server

ACK			-	Used for acknowledgement purposes for confirming the request received at the other end point

BYE			-	Used for signal termination and to end call

CANCEL		-	Used to cancel any pending request sent prior to this request

REFER		- 	Used to request an issue-request to call-transfer
```

**SIP response** will be sent by the agent server to indicate the result of received SIP request. These are generally determined with the help of numerical range of result code. Below is the high-level

```
1XX		-	used to indicate received response is valid and being processed

2XX		-	To confirm request is processed successfully (200 is most used response code to indicate call 				established)

3XX		-	To indicate call redirect to complete request

4XX		-	To indicate error occurered at the server side (400 is used for bad syntax on the request)

5XX		-	To indicate server internal failure on a valid request

6XX		-	To indicate failure which cannot be handled from server side (ex: call rejection)

```



### Voice Gateway

Voice gateway can be referred as Router PSTN with a standard purpose of converting telephone signals from PSTN to digital IP packets for transporting over internet based on VoIP protocols. A Voice gateway will typically support a single protocol, out of which SIP is higher accepted protocol.

Voice Gateway contains two primary components.

1. **Voice Browser** -  Is a type of browser works on [VoiceXML(VXML)](https://voicexml.org/) to get interactive call flow forms and accept input as DTMF and/or Voice inputs

   Below code is the example for a sample VXML file to welcome the caller on receiving a call and ask his/her input as DTMF between 0 to 9.

   ```
   <?xml version="1.0" encoding="UTF-8"?>
   <vxml version = "2.1">
       <meta name="maintainer" content="1234567890" />
       <form id="guessNumber">
           <field name="guess">
               <prompt>Hello Caller! Thank you for being a Mobigesture blogger. To Test, please pick a number between 0 and 9</prompt>
               <grammar xml:lang="en-US" root = "MYRULE" mode="dtmf">
                   <rule id="MYRULE" scope = "public">
                       <one-of>
                           <item> 1 </item>
                           <item> 2 </item>
                           <item> 3 </item>
                           <item> 4 </item>
                           <item> 5 </item>
                           <item> 6 </item>
                           <item> 7 </item>
                           <item> 8 </item>
                           <item> 9 </item>
                           <item> 0 </item>
                       </one-of>
                   </rule>
               </grammar>
               <grammar xml:lang="en-US" root = "MYRULE" mode="voice">
                   <rule id="MYRULE" scope = "public">
                       <one-of>
                           <item> one </item>
                           <item> two </item>
                           <item> three </item>
                           <item> four </item>
                           <item> five </item>
                           <item> six </item>
                           <item> seven </item>
                           <item> eight </item>
                           <item> nine </item>
                           <item> zero </item>
                       </one-of>
                   </rule>
               </grammar>
               <noinput>
                   <prompt>I did not hear you. Please try again.</prompt>
                   <reprompt/>
               </noinput>
               <nomatch>
                   <prompt>Is that a number? Please try again.</prompt>
                   <reprompt/>
               </nomatch>
           </field>
           <filled namelist="guess" mode="all">
               <prompt> Your input is <value expr="guess" /></prompt>
           </filled>
       </form>
   </vxml>
   ```

   

2. **CCXML Engine** - To handle calls based on instructions passed through CCXML

   - CCXML (**C**all **C**ontrol e**X**tensible **M**arkup **L**anguage) is W3C standard markup language designed to control how a phone call to be answered, transferred, conferenced and more. It adds a robust call support to VoiceXML. One critical thing to understand about CCXML is, it is not a media language like VoiceXML, it just provides support to move calls around and integrate different systems.

     > Ref: https://www.w3.org/TR/ccxml/

### Voice Platform

Voice Platform is a hardware unit can be defined as switch to execute commands and logic provided on VXML and CCXML with a capability to process speech and DTMF inputs along with enabling voice application creation on it. They are also designed to interface with back-end systems. The major share holders in the market are

1. [Avaya Voice Platform](https://support.avaya.com/products/P0979/voice-portal) (AVP)
2. [Genesys Voice Platform](https://docs.genesys.com/Documentation/GVP) (GVP)
3. [Cisco Voice Platform](https://www.cisco.com/c/en/us/support/customer-collaboration/unified-customer-voice-portal-11-6/model.html) (CVP)



This mode of working with VXML & CCXML is becoming legacy due to various factors. Some of them are

1. Upfront Infrastructure setup cost is high

2. Maintenance based on Voice Platform switches are complex

3. CCXML session maintenance is a bit complicated in design

   

## Conclusion

With huge ROI and desire to provide more personalized assistance to the customers, companies are interested in making investments towards IVR systems to assist their customers 24/7. On the flip side, on-premise setup & maintenance requires huge investments, to reduce the upfront costs, VoIP domain engineers are making steps to implement solutions on Cloud. To make it possible there are certain open source alternatives (namely [Freeswitch](https://freeswitch.com/) or [Asterisk](https://www.asterisk.org/)) available in the market. 

 

