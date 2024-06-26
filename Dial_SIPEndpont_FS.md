## Dialing a SignalWire SIP Endpoint from FreeSWITCH Dialplan

To dial a SignalWire SIP endpoint from FreeSWITCH dialplan, follow these steps:

1. Create a gateway with SignalWire SIP endpoint details. Refer to this guide for instructions on how to create a gateway in FreeSWITCH: [Gateway Creation Guide](https://github.com/ShashiKumar-SignalWire/SW-example-Docs/blob/main/FS_SIPEndpoint_registration.md).

2. Create the following dialplan in your FreeSWITCH `dialplan/public.xml` to forward your incoming calls to the public dialplan:

```xml
<extension name="Dial">
  <condition field="destination_number" expression="^(.*)$">
    <action application="set" data="sip_custom_header=X-MyCustomHeader: Value-ShashiKumar"/>
    <action application="bridge" data="sofia/gateway/signalwire_prod/sip:SignalwireUsername@example.sip.signalwire.com"/> <!-- Replace example.sip.signalwire.com with your actual SignalWire SIP endpoint URI -->
    <action application="hangup"/>
  </condition>
</extension>
```
If you want to pass custom SIP headers when calling the SIP endpoint, set custom variables as shown below:

```xml
<extension name="Dial">
  <condition field="destination_number" expression="^(.*)$">
    <action application="set" data="sip_h_X-AccountCode=4321"/> <!-- Custom header to pass in INVITE -->
    <action application="set" data="sip_h_X-MyCustomHeader: Value"/> <!-- Custom header to pass in INVITE -->
    <action application="bridge" data="sofia/gateway/signalwire_prod/sip:SignalwireUsername@example.sip.signalwire.com"/> <!-- Replace example.sip.signalwire.com with your actual SignalWire SIP endpoint URI -->
    <action application="hangup"/>
  </condition>
</extension>
```

Replace example.sip.signalwire.com with your actual SignalWire SIP endpoint URI and SignalwireUsername with your SignalWire SIP endpoint username.
