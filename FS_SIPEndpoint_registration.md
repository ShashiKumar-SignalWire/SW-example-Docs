## Setting up a SignalWire SIP Endpoint in FreeSWITCH

To register a SignalWire SIP endpoint in FreeSWITCH, follow these steps:

1. Set up a SIP endpoint in the SignalWire space.
   - You can create a SIP endpoint manually within your SignalWire Space or programmatically through the [Create A SIP Endpoint API](https://developer.signalwire.com/apis/docs/rest/2020-12-11/account/sip-endpoints/create-a-sip-endpoint).
   - If you want to create a SIP endpoint in your SignalWire Space, visit the SIP tab and click "Create a Sip Endpoint."
     ![image](https://github.com/ShashiKumar-SignalWire/SW-example-Docs/assets/45973234/f385b59c-023e-413d-b7e6-07765e2b5b43)

   - You will need to set a few things: the username you will register your SIP endpoint as, a password to authenticate your endpoint when registering, which SignalWire or verified phone number you would like to show when dialing a PSTN number (if left blank, a random number from your purchased numbers will be chosen), encryption status, an outbound call policy (whether to allow calls to PSTN), and the default codecs/ciphers you would like to use. You can create a SIP endpoint programmatically by making a POST request to the SIP Endpoint resource.

2. Configure the SIP endpoint in FreeSWITCH.
   - First, follow the official FreeSWITCH documentation [link](https://developer.signalwire.com/freeswitch/FreeSWITCH-Explained/Configuration/Sofia-SIP-Stack/Gateways-Configuration_7144069/) to create a gateway.
   - Create a new XML file in your FreeSWITCH `conf` directory under `sip_profiles/external/` and name it `signalwire_gateway.xml`.
   
```xml
<gateway name="signalwire_gateway">
  <param name="proxy" value="example.sip.signalwire.com"/> <!-- SignalWire SIP endpoint URI -->
  <param name="extension-in-contact" value="true"/> 
  <param name="username" value="username"/> <!-- SignalWire SIP endpoint username -->
  <param name="password" value="password"/> <!-- SignalWire SIP endpoint password -->
  <param name="transport" value="udp"/>
</gateway>
```

 - Now, go to fs_cli and then execute reloadxml. After a successful XML reload, execute reload mod_sofia, and to see your gateway in fs_cli, execute sofia status.
