# Signalwire  IVR examples in FS LUA and SWML
## Introduction
In this guide, we will explore various examples of creating  IVR examples using methods such as SignalWire Markup Language (SWML), FreeSWITCH LUA scripting, and Signlwire Callflow builder Each technique offers unique capabilities and approaches to implementing IVR functionality within telecommunication systems.

## FreeSWITCH LUA Script IVR example 
This Lua script for FreeSWITCH creates a simple Interactive Voice Response (IVR) system. When a call is received, the script answers the call and provides a series of spoken prompts using Text-to-Speech (TTS). The caller is given several options, such as joining a conference, accessing an IVR, listening to voicemail, enjoying music on hold, or contacting a specific person. Based on the digit pressed by the caller, the script transfers the call to the appropriate destination.

```lua
session:answer();
while (session:ready() == true) do
    session:setAutoHangup(false);
    session:set_tts_params("flite", "kal");
    session:speak("Welcome. Welcome to the Signalwire Freeswitch IVR examples.");
    session:sleep(100);
    session:speak("Please select an action.");
    session:sleep(100);
    session:speak("To join the conference, press 1.");
    session:sleep(100);
    session:speak("To call FreeSWITCH IVR, press 2.");
    session:sleep(100);
    session:speak("To call Voice Mail, press 3.");
    session:sleep(100);
    session:speak("To listen to hold music, press 4.");
    session:sleep(100);
    session:speak("To call me, press 0.");
    session:sleep(3000);
    digits = session:getDigits(1, "", 3000);
    if (digits == "1") then
        session:execute("conference", "my_conference@default");
    end
    if (digits == "2") then
        session:execute("transfer", "5000 XML default");
    end
    if (digits == "3") then
        session:execute("voicemail", "default 1000");
    end
    if (digits == "4") then
        session:execute("playback", "local_stream://moh");
    end
    if (digits == "0") then
        session:execute("bridge", "sofia/gateway/sip_provider/voipaware@sip.voipuser.org");  -- Replace 'sofia/gateway/sip_provider/voipaware@sip.voipuser.org' with your actual gateway
    end
end
```

### Configuration Steps
To set up the IVR examples in  Freeswitch, follow these steps:

1. Add the LUA Script: Place the above LUA script in the scripts directory of FreeSWITCH. For example, /usr/local/freeswitch/scripts and name it welcome_ivr.lua.
2. Update Dialplan: Add the following XML in the public.xml dialplan to include the IVR extension:
```xml
<include>
  <extension name="welcome_ivr">
    <condition field="destination_number" expression="^(.*)$">
      <action application="lua" data="welcome_ivr.lua"/>
    </condition>
  </extension>
</include>
```
3. Reload Configuration: Execute the following command from the command line to reload the FreeSWITCH configuration:
```bash
fs_cli -x "reloadxml"
```
These steps provide a clear guide on how to configure and deploy the FreeSWITCH Lua script IVR example.

#### Resources
- [Freeswitch LUA exmples ](https://developer.signalwire.com/freeswitch/FreeSWITCH-Explained/Client-and-Developer-Interfaces/Lua-API-Reference/Lua-examples/)


# SignalWire Call Flow Builder Voicemail Example

## Introduction

In addition to traditional scripting methods like SignalWire Markup Language (SWML), SignalWire offers a user-friendly drag-and-drop interface called SignalWire Call Flow Builder. This intuitive tool allows users to create complex Interactive Voice Response (IVR) systems visually, without extensive coding knowledge.

SignalWire Call Flow Builder empowers users to design IVRs by arranging various call flow elements such as connect, join_room, play, switch, etc., using a simple drag-and-drop interface. This approach streamlines the process of building telephony applications, enabling rapid development and iteration cycles.

Now, let's create an example of all IVR functions example step-by-step using SignalWire Call Flow Builder, accompanied by screenshots to guide you through each process stage.

1. **Login and Access Call Flow Builder**: Upon logging into your SignalWire space, navigate to the Call Flow Builder section.
   ![image](https://github.com/ShashiKumar-SignalWire/SW-example-Docs/assets/45973234/18c7285c-6750-4afb-9321-2db0fd3721d3)

2. **Create a New Call Flow**: Click on the "+ Add New" button located in the top-right corner. This action will redirect you to a new page containing a text box and a "Save" button.
   ![image](https://github.com/ShashiKumar-SignalWire/SW-example-Docs/assets/45973234/13926569-11e7-432e-a385-369b041dae0f)
3. **Name Your Call Flow**: In the text box, provide a meaningful name for your call flow, then click "Save." You will then be redirected to the Builder page.
 

4. **Explore the Call Flow Builder Interface**: On the Builder page, you'll notice all available nodes listed on the left panel and the "Handle call" section in the middle.
![image](https://github.com/ShashiKumar-SignalWire/SW-example-Docs/assets/45973234/68d1b89a-a218-4f3d-bc56-3d823c0f9658)


5. **Add Answer Call Node**: To answer the incoming call first add an Answer call node and link it to the Handle call node as shown in the screenshot below
![image](https://github.com/ShashiKumar-SignalWire/SW-example-Docs/assets/45973234/40a02f14-f037-4bee-aacb-71bb91a15e8f)

6. **Add Gather Node**: To gather caller input digits after playing a prompt, add a Gather Node and configure the options as shown in the screenshots below.
    ![image](https://github.com/ShashiKumar-SignalWire/SW-example-Docs/assets/45973234/0dc92b3c-bd96-4872-86cd-1c519221a3b6)
    ![image](https://github.com/ShashiKumar-SignalWire/SW-example-Docs/assets/45973234/7ef3f330-dc0c-4b2c-be25-668e1ce4447c)

7. **Add Nodes to Gather Options**: After creating the Gather Node, configure each digit option by adding the corresponding child nodes. For example, you can add a Join Room node for Option 1, a Record Message node for Option 2, a Playback node for Option 3, a Forward to PSTN node for Option 4, and a Hangup node for all other options. Ensure to use Hangup nodes for the child nodes to complete the IVR flow.
    ![image](https://github.com/ShashiKumar-SignalWire/SW-example-Docs/assets/45973234/6f206ed2-10ce-4f8c-8a26-58a4000760e5)

8. **Complete IVR Flow**

    ![image](https://github.com/ShashiKumar-SignalWire/SW-example-Docs/assets/45973234/14e32d8f-f59d-4536-8473-6bfc98666b56)

    **Note:**
    There is no direct Join Room node in the Call Flow Builder. To create this functionality, follow these steps:

    1. **Create an SWML Script**: Go to the Relay/SWML tab in your SignalWire dashboard and create a new SWML script with the following JSON content:
    
        ```json
        {
          "version": "1.0.0",
          "sections": {
            "main": [
              {
                "join_room": "my_room"
              }
            ]
          }
        }
        ```
    
    2. **Use the Execute SWML Node**: In the Call Flow Builder, use the *Execute SWML* node for the desired Gather digit option and link it to the script you created above.

By completing these steps, you can effectively implement the IVR flow in SignalWire, allowing you to handle various call routing options, including joining a room using a custom SWML script.

9. **Test Your IVR**: Congifure IVR call flow builder to your SIgnalwire number and make a test call to check the call flow as expected 



