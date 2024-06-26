# SignalWire Voicemail Examples

## Introduction

In this guide, we will explore various examples of creating voicemail systems using different methods: SignalWire Markup Language (SWML), FreeSWITCH, and SignalWire Call Flow Builder. Each method offers unique capabilities and approaches to implementing voicemail functionality within telecommunication systems.

## FreeSWITCH Voicemail Example

## Introduction
In this setup, we'll configure FreeSWITCH to bridge incoming calls from SignalWire and a destination number. We'll start by registering a gateway with SignalWire SIP endpoint details, and establishing the connection between FreeSWITCH and SignalWire. Then, we'll configure the FreeSWITCH dialplan to handle incoming calls from SignalWire and forward them to a specified destination number. If the call is unanswered, we'll handle voicemail to ensure callers can leave a message. This setup enables seamless call forwarding and voicemail functionality using FreeSWITCH and SignalWire.

### Steps:

1. **Register a Gateway with SignalWire Endpoint**: Follow the guide provided by SignalWire to register a gateway with the SignalWire SIP endpoint details. This step establishes the connection between your FreeSWITCH instance and SignalWire.
   
   [Freeswitch Signalwre SIP endpoint registraion](https://github.com/ShashiKumar-SignalWire/SW-example-Docs/blob/main/FS_SIPEndpoint_registration.md)

3. **Configure Dialplan for Incoming Calls**: Once the gateway is registered, you need to configure your FreeSWITCH dialplan to handle incoming calls from SignalWire and route them to the appropriate destination.

    ```xml
    <extension name="incoming_calls">
      <condition field="destination_number" expression="^(\d{10})$">
        <action application="bridge" data="sofia/gateway/your_signalwire_gateway/${destination_number}"/>
      </condition>
    </extension>
    ```

4. **Configure Voicemail Handling**: To handle cases where the FreeSWITCH user does not answer the call, you can set up voicemail handling in the dialplan.

    ```xml
    <extension name="voicemail">
      <condition field="destination_number" expression="^(\d{10})$">
        <action application="bridge" data="sofia/gateway/your_signalwire_gateway/${destination_number}"/>
        <action application="voicemail" data="default ${destination_number}"/>
      </condition>
    </extension>
    ```

5. **Adjust Configuration**: Replace `your_signalwire_gateway` with the name of your registered SignalWire gateway.

6. **Testing**: After configuring the dialplan, test the setup by placing a call to the SignalWire number configured to forward calls to the SIP endpoint. If the call is not answered, it should be forwarded to voicemail as per the configured dialplan.


#### Resources
- [Gateways Configuration](https://developer.signalwire.com/freeswitch/FreeSWITCH-Explained/Configuration/Sofia-SIP-Stack/Gateways-Configuration_7144069/)
- [SIP endpoint configuration](https://developer.signalwire.com/guides/set-up-a-signalwire-phone-number-with-a-sip-endpoint/)
- [Freeswitch Voicemail](https://developer.signalwire.com/freeswitch/FreeSWITCH-Explained/Modules/mod-voicemail/Voicemail_13174041/)

## SignalWire SWML Voicemail Example

### Introduction

In this guide, we will explore an example of using SignalWire Markup Language (SWML) to create a voicemail system. SignalWire Markup Language (SWML) is a scripting language used to control the behavior of voice calls, such as initiating calls, playing audio, recording messages, and more.

In this example, we will walk through the steps involved in setting up a voicemail system using SWML. This system allows callers to leave a message when the called party is unavailable to answer the call. We will break down each component of the SWML script, explaining its purpose and functionality, to provide a comprehensive understanding of how to create and customize a voicemail system using SignalWire.


#### Example SWML JSON script:
```json
{
  "sections": {
    "main": [
      {
        "connect": {
          "from": "+1XYXYXYXYX",
          "timeout": 10,
          "to": "+1XYXYXYXYX"
        }
      },
      {
        "switch": {
          "case": {
            "connected": ["hangup"]
          },
          "default": [
            {
              "switch": {
                "case": {
                  "noAnswer": [{"transfer": "zbPB5t-FP3ptWUbHmYHCm"}],
                  "busy": [{"transfer": "zbPB5t-FP3ptWUbHmYHCm"}],
                  "decline": [{"transfer": "zbPB5t-FP3ptWUbHmYHCm"}]
                },
                "default": ["hangup"],
                "variable": "connect_failed_reason"
              }
            }
          ],
          "variable": "connect_result"
        }
      }
    ],
    "zbPB5t-FP3ptWUbHmYHCm": [
      {
        "play": {
          "url": "say:Our Agent is not answering your call at the moment, Please leave your message after the beeb sound",
          "say_language": "en-US",
          "say_gender": "female",
          "say_voice": "gcloud.en-US-Neural2-C"
        }
      },
      {
        "record": {
          "beep": true,
          "format": "mp3",
          "stereo": true,
          "terminators": "#",
          "end_silence_timeout": 10,
          "initial_timeout": 10,
          "max_length": 60
        }
      },
      "hangup"
    ]
  },
  "version": "1.0.0"
}
```
### Example Breakdown

#### Connect Method
Initiates a call to the specified number with a timeout.

```json
{
  "connect": {
    "from": "+16XYXYXYXYX",
    "timeout": 10,
    "to": "+91XYXYXYX"
  }
}

```

#### Switch Method
Handles different scenarios after attempting to connect the call, such as no answer, busy, or decline.
```
{
  "switch": {
    "case": {
      "connected": ["hangup"]
    },
    "default": [
      {
        "switch": {
          "case": {
            "noAnswer": [{"transfer": "zbPB5t-FP3ptWUbHmYHCm"}],
            "busy": [{"transfer": "zbPB5t-FP3ptWUbHmYHCm"}],
            "decline": [{"transfer": "zbPB5t-FP3ptWUbHmYHCm"}]
          },
          "default": ["hangup"],
          "variable": "connect_failed_reason"
        }
      }
    ],
    "variable": "connect_result"
  }
}
```
#### Play Method
Plays a message informing the caller that the agent is unavailable and prompts them to leave a message after the beep sound.
```json
{
  "play": {
    "url": "say:Our Agent is not asnswring your call at the mpment, Please leave your mesage after the beeb sound",
    "say_language": "en-US",
    "say_gender": "female",
    "say_voice": "gcloud.en-US-Neural2-C"
  }
}

```

#### Record Method
Records the caller's message after playing the prompt with specified settings.
```json
{
  "record": {
    "beep": true,
    "format": "mp3",
    "stereo": true,
    "terminators": "#",
    "end_silence_timeout": 10,
    "initial_timeout": 10,
    "max_length": 60
  }
}

```

#### Hangup Method
Ends the call after the recording is completed or if the call cannot be connected.
```json
"hangup"
```

#### Getting Started
1. Sign up for a SignalWire account and create a project.
2. Navigate to your project and click on the "Relay / SWML" tab.
3. Click on the "SWML Scripts" tab and then click on the "+ New" button to create a new SWML Script.
4. Copy and paste the provided SWML script into the editor. Ensure to customize the `from` and `to` numbers in the `Connect` action according to your requirements, then save the script.
5. Configure the SWML script to use your SignalWire phone number (you can refer to the [Purchase a phone number guide](https://developer.signalwire.com/rest/purchase-a-phone-number/) for assistance) and save the changes.
6. Test the functionality of your configured SWML script by initiating calls and verifying the voicemail system.

#### Resources
- [SignalWire Documentation](https://developer.signalwire.com/)
- [SignalWire SWML Guide](https://developer.signalwire.com/sdks/reference/swml/methods/)


## SignalWire Call Flow Builder Voicemail Example

## Introduction

In addition to traditional scripting methods like SignalWire Markup Language (SWML), SignalWire offers a user-friendly drag-and-drop interface called SignalWire Call Flow Builder. This intuitive tool allows users to create complex Interactive Voice Response (IVR) systems visually, without extensive coding knowledge.

SignalWire Call Flow Builder empowers users to design IVRs by arranging various call flow elements such as connect, join_room, play, switch, etc., using a simple drag-and-drop interface. This approach streamlines the process of building telephony applications, enabling rapid development and iteration cycles.

Now, let's create an example of a voicemail system step-by-step using SignalWire Call Flow Builder, accompanied by screenshots to guide you through each stage of the process.


1. **Login and Access Call Flow Builder**: Upon logging into your SignalWire space, navigate to the Call Flow Builder section.
   ![image](https://github.com/ShashiKumar-SignalWire/SW-example-Docs/assets/45973234/18c7285c-6750-4afb-9321-2db0fd3721d3)


2. **Create a New Call Flow**: Click on the "+ Add New" button located in the top-right corner. This action will redirect you to a new page containing a text box and a "Save" button.
   ![image](https://github.com/ShashiKumar-SignalWire/SW-example-Docs/assets/45973234/13926569-11e7-432e-a385-369b041dae0f)


3. **Name Your Call Flow**: In the text box, provide a meaningful name for your call flow, then click "Save." You will then be redirected to the Builder page.
 

4. **Explore the Call Flow Builder Interface**: On the Builder page, you'll notice all available nodes listed on the left panel and the "Handle call" section in the middle.
![image](https://github.com/ShashiKumar-SignalWire/SW-example-Docs/assets/45973234/9e8f52f5-2153-42e4-9929-cff875afaf73)

5. **Add Forward to Phone Number Node**: Click on the "Forward to Phone Number" button. A form will appear on the right side, allowing you to specify the forward-to-phone options.
 ![image](https://github.com/ShashiKumar-SignalWire/SW-example-Docs/assets/45973234/1d92e785-4166-405c-879e-7b6e69e06b21)


6. **Fill Out Forwarding Options**: In the form, set the "Default From" field to your SignalWire number. Then, under "Phone Number 1," specify the "From" and "To" numbers. You have the option to add more phone numbers if needed.

7. **Handle Call Flow**: After adding the forward to the phone number node, you'll see options for handling different call outcomes like Success, No Answer, Busy, Decline, and Error.
 ![image](https://github.com/ShashiKumar-SignalWire/SW-example-Docs/assets/45973234/15133637-5874-41b1-b65e-a9c43ef01545)


8. **Connect Nodes**: Create a link between the "Handle call" node and the "Forward to Phone" node.
  ![image](https://github.com/ShashiKumar-SignalWire/SW-example-Docs/assets/45973234/2f7ab1b4-7246-4f79-a9db-5e976978d87e)

9. **Handle Different Call Outcomes**: For each outcome (Success, No Answer, Busy, Decline, Error), add appropriate actions. For example, use the "Play Audio or TTS" node to provide a message for each scenario.
10. 

11. **Set Up Voicemail Recording**: Select the "Voicemail Recording" node and specify the desired audio format (e.g., WAV or MP3).
     ![image](https://github.com/ShashiKumar-SignalWire/SW-example-Docs/assets/45973234/9ecec43f-3272-45a6-9661-112c4556a034)

12. **Link Nodes Together**: Connect the "Voicemail Recording" node to the "Play Audio or TTS" node for the remaining options and then link the "Hangup" node to the "Voicemail Recording" node.
    ![image](https://github.com/ShashiKumar-SignalWire/SW-example-Docs/assets/45973234/c5a0c36b-f661-4e9f-bff0-37c3b1de5d56)

   
14. **Deploy Call Flow**: Once you've configured the call flow, click on "Deploy" to activate it.

15. Configure the SWML script to use your SignalWire phone number (you can refer to the Purchase a phone number guide for assistance) and save the changes.
16. Test the functionality of your configured SWML script by initiating calls and verifying the voicemail system.

 #### Resources
- [SignalWire Documentation](https://developer.signalwire.com/)
- [SignalWire CallFlow Builder Voicemail Guide](https://developer.signalwire.com/call-flow-builder/voicemail-recording)


  
