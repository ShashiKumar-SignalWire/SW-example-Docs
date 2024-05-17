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
   <condition field="destination_number" expression="^(10[01][0-9])$">
        <action application="export" data="dialed_extension=$1"/>
   </condition>
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
### Explanation

1. **Working Hours Condition (9 AM to 6 PM, Monday to Friday):**
   - The condition checks if the current time is within the specified working hours.
   - Sets `hangup_after_bridge` to `true` to ensure the call is terminated after the bridge completes.
   - Sets `continue_on_fail` to `true` to continue executing the dialplan if the bridge action fails.
   - Bridges the call to the external number via the specified SIP gateway during working hours.

2. **Default Condition (Outside Working Hours):**
   - Acts as the fallback condition when the working hours condition is not met.
   - Answers the incoming call.
   - Pauses for 1 second (1000 milliseconds).
   - Sends the call to voicemail for the specified domain and dialed extension.

### Adjustments

- Replace `${domain_name}` with your actual domain name.
- Replace `+198XYXYXY` with the actual phone number you want to forward calls to during working hours.

### How to Use

1. **Update the Dialplan**: Copy the XML configuration into your FreeSWITCH dialplan file (e.g., `dialplan/default.xml`).
2. **Reload the Dialplan**: After updating the dialplan, reload it using the FreeSWITCH CLI:
   ```bash
fs_cli -x "reloadxml"
```

## SignalWire SWML  for Office Hours Call Forwarding
### Introduction
In this guide, we will explore an example of using SignalWire Markup Language (SWML) to create office hours call forwarding. SignalWire Markup Language (SWML) is a scripting language used to control the behavior of voice calls, such as initiating calls, playing audio, recording messages, and more.

```json
{
  "sections": {
    "main": [
      "answer",
      {
        "request": {
          "method": "GET",
          "url": "https://f47d-103-52-38-2.ngrok-free.app/is_working_hours"
        }
      },
      {
        "switch": {
          "case": {
            "success": [
              {
                "cond": [
                  {
                    "then": [
                      {
                        "connect": {
                          "from": "+16172728591",
                          "to": "+918143209997"
                        }
                      },
                      {
                        "switch": {
                          "case": {
                            "connected": [
                              {
                                "transfer": "ZYFRTspBKf-34skFsEoO8"
                              }
                            ]
                          },
                          "default": [
                            {
                              "switch": {
                                "case": {
                                  "noAnswer": [
                                    {
                                      "transfer": "ZYFRTspBKf-34skFsEoO8"
                                    }
                                  ],
                                  "busy": [
                                    {
                                      "transfer": "ZYFRTspBKf-34skFsEoO8"
                                    }
                                  ],
                                  "decline": [
                                    {
                                      "transfer": "ZYFRTspBKf-34skFsEoO8"
                                    }
                                  ],
                                  "error": [
                                    {
                                      "transfer": "ZYFRTspBKf-34skFsEoO8"
                                    }
                                  ]
                                },
                                "default": [
                                  "hangup"
                                ],
                                "variable": "connect_failed_reason"
                              }
                            }
                          ],
                          "variable": "connect_result"
                        }
                      }
                    ],
                    "when": "%{request_response.isInWorkingHours} == true"
                  },
                  {
                    "else": [
                      {
                        "play": {
                          "url": "say:Our office hours are from Monday to Friday, spanning 9 AM to 6 PM. To connect with our agents, kindly place your call during this time frame. We look forward to assisting you!",
                          "say_language": "en-US",
                          "say_gender": "female",
                          "say_voice": "gcloud.en-US-Standard-C"
                        }
                      },
                      {
                        "transfer": "ZYFRTspBKf-34skFsEoO8"
                      }
                    ]
                  }
                ]
              }
            ]
          },
          "default": [
            {
              "play": {
                "url": "say:Oops! Something's not quite right on our end. Please try calling back later. Thank you!",
                "say_language": "en-US",
                "say_gender": "female",
                "say_voice": "gcloud.en-US-Standard-C"
              }
            },
            {
              "transfer": "ZYFRTspBKf-34skFsEoO8"
            }
          ],
          "variable": "request_result"
        }
      }
    ],
    "ZYFRTspBKf-34skFsEoO8": [
      {
        "hangup": {
          "reason": "hangup"
        }
      }
    ]
  },
  "version": "1.0.0"
}

```
