# SignalWire Voicemail Examples

## Introduction

In this guide, we will explore various examples of creating voicemail systems using different methods: SignalWire Markup Language (SWML), FreeSWITCH, SignalWire LaML, and SignalWire Call Flow Builder. Each method offers unique capabilities and approaches to implementing voicemail functionality within telecommunication systems.

## FreeSWITCH Voicemail Example

[Include FreeSWITCH voicemail example here]

## SignalWire LaML Voicemail Example

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
          "url": "say:Our Agent is not asnswring your call at the mpment, Please leave your mesage after the beeb sound",
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
[Include SignalWire Call Flow Builder voicemail example here]



