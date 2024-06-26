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

**Description:**
This SWML script is designed to handle incoming calls and forward them to a specific number during office hours (Monday to Friday, 9 AM to 6 PM). It also provides a message informing callers about the office hours and requests them to call during that time frame.

```json
{
  "sections": {
    "main": [
      "answer",
      {
        "request": {
          "method": "GET",
          "save_variables":true,
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
                          "from": "+16XYXYXYX",
                          "to": "+198XYXYXYX"
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


### Breakdown and Explanation:
1. **Answer**: The call is answered to initiate the communication process.
2. **Request**: A GET request is made to the specified URL to check if the current time is within working hours.
3. **Switch**: Depending on the success of the request, the script either forwards the call to the specified number or plays a message indicating office hours.
   - **Success Case**:
     - If the request is successful and the current time is within working hours:
       - The call is connected from the specified number to the destination number.
       - If the call is successfully connected, it is transferred to the specified endpoint (`ZYFRTspBKf-34skFsEoO8`).
       - If the call cannot be connected for various reasons (e.g., no answer, line busy, etc.), it's transferred to the specified endpoint (`ZYFRTspBKf-34skFsEoO8`).
   - **Default Case**:
     - If the request fails or the current time is outside working hours:
       - A message is played to inform the caller about the office hours and to call during that time frame.
       - After playing the message, the call is transferred to the specified endpoint (`ZYFRTspBKf-34skFsEoO8`).
4. **Hangup**: Finally, if the call cannot be connected or handled, it is hung up with the reason specified as "hangup".

5. **Configuration Note**: The URL in the request needs to be replaced with the actual Ngrok URL/public URL containing the provided Node Express code to determine working hours.

**Version:**
This SWML script version is "1.0.0".

***NodeJS Expess code***
```javascript
const express = require('express');
const app = express();

// Middleware to check time and day
app.use((req, res, next) => {
  const now = new Date();
  const hour = now.getHours();
  const day = now.getDay(); // Sunday is 0, Monday is 1, and so on...

  // Check if current time is between 9 AM and 6 PM and if it's Monday to Friday
  if (hour >= 9 && hour < 18 && day >= 1 && day <= 5) {
    res.locals.isInWorkingHours = true;
  } else {
    res.locals.isInWorkingHours = false;
  }
  next();
});

// Route to handle requests
app.get('/', (req, res) => {
  res.send('Welcome! The service is available.');
});

// Route to check if it's working hours and return JSON
app.get('/is_working_hours', (req, res) => {
  res.json({ isInWorkingHours: res.locals.isInWorkingHours });
});

// Start server
const port = 3000;
app.listen(port, () => {
  console.log(`Server is running on port ${port}`);
});

```

## SignalWire Call flow builder for Office Hours Call Forwarding

### Introduction
In addition to traditional scripting methods like SignalWire Markup Language (SWML), SignalWire offers a user-friendly drag-and-drop interface called SignalWire Call Flow Builder. This intuitive tool allows users to create complex Interactive Voice Response (IVR) systems visually, without extensive coding knowledge.

SignalWire Call Flow Builder empowers users to design IVRs by arranging various call flow elements such as connect, join_room, play, switch, etc., using a simple drag-and-drop interface. This approach streamlines the process of building telephony applications, enabling rapid development and iteration cycles.

Now, let's create an example for  Office Hours Call Forwarding  step-by-step using SignalWire Call Flow Builder, accompanied by screenshots to guide you through each stage of the process.

Create call flow  with nodes shown in the picture below 
![image](https://github.com/ShashiKumar-SignalWire/SW-example-Docs/assets/45973234/80172702-a13f-469d-b037-32afbf222a9d)


In the request node add URL, Method GET/POST, and enable Set Variables as shown in the screenshot below:

![image](https://github.com/ShashiKumar-SignalWire/SW-example-Docs/assets/45973234/bbc436c8-e190-4367-a652-6b2c723dd647)

Once you've configured the call flow, click on "Deploy" to activate it. and Configure the SWML script to use your SignalWire phone number (you can refer to the Purchase a phone number guide for assistance) and save the changes. 
