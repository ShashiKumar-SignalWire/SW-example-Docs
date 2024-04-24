## Dialing a SignalWire SIP Endpoint from FreeSWITCH Dialplan

To dial a SignalWire SIP endpoint from FreeSWITCH dialplan, follow these steps:

1. Create the following dialplan in your FreeSWITCH `dialplan/public.xml` and forward your incoming calls to public dialplan :

```xml
<extension name="Dial">
  <condition field="destination_number" expression="^(.*)$">
    <action application="set" data="sip_custom_header=X-MyCustomHeader: Value-ShashiKumar"/>
    <action application="bridge" data="sofia/external/sip:SignalwireUsername@example.sip.signalwire.com"/> <!-- Replace example.sip.signalwire.com with your actual SignalWire SIP endpoint URI. -->
    <action application="hangup"/>
  </condition>
</extension>
```

If you want to pass custom SIP headers when calling the SIP endpoint, set custom variables as shown below:
```xml
<extension name="Dial">
  <condition field="destination_number" expression="^(.*)$">
    <action application="set" data="sip_h_X-AccountCode=4321"/> <!-- Custom header to pass in INVITE -->
    <action application="set" data="sip_custom_header=X-MyCustomHeader: Value"/> <!-- Custom header to pass in INVITE -->
    <action application="bridge" data="sofia/external/sip:SignalwireUsername@example.sip.signalwire.com"/> <!-- Replace example.sip.signalwire.com with your actual SignalWire SIP endpoint URI. -->
    <action application="hangup"/>
  </condition>
</extension>
```
