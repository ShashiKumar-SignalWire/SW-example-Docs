# Signalwire  Office Hours Call Forwarding examples
## Introduction
In this guide, we will explore various examples of creating office hours call forwarding to agents using different methods: SignalWire Markup Language (SWML), FreeSWITCH, and SignalWire Call Flow Builder. Each method offers unique capabilities and approaches to implementing office hours call forwarding functionality within telecommunication systems.

## FreeSWITCH Dialplan for Office Hours Call Forwarding
## Introduction
This FreeSWITCH dialplan configuration forwards incoming calls to an external number during office hours (Monday to Friday, 9 AM to 6 PM). Outside of these hours, the calls are sent to voicemail.

### Dialplan Configuration
The following XML configuration should be added to your FreeSWITCH dialplan (e.g., `dialplan/default.xml`).

```xml
<extension name="office_hours">
   <!-- Condition for Monday to Friday, 9 AM to 6 PM -->
   <condition wday="1-5" hour="9-17">
      <action application="set" data="hangup_after_bridge=true"/>
      <action application="set" data="continue_on_fail=true"/>
      <action application="bridge" data="sofia/gateway/signalwire/+198XYXYXY"/>
   </condition>
   
   <!-- Default condition to send the call to voicemail -->
   <condition>
      <action application="answer"/>
      <action application="sleep" data="1000"/>
      <action application="voicemail" data="default ${domain_name} ${dialed_extension}"/>
   </condition>
</extension>
```

