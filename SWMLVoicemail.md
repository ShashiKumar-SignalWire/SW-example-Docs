# SignalWire SWML Voicemail Example

## Introduction

In this guide, we will explore an example of using SignalWire Markup Language (SWML) to create a voicemail system. SignalWire Markup Language (SWML) is a scripting language used to control the behavior of voice calls, such as initiating calls, playing audio, recording messages, and more.

In this example, we will walk through the steps involved in setting up a voicemail system using SWML. This system allows callers to leave a message when the called party is unavailable to answer the call. We will break down each component of the SWML script, explaining its purpose and functionality, to provide a comprehensive understanding of how to create and customize a voicemail system using SignalWire.

## Example Breakdown

### Connect Action
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

### Switch Action
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
### Play Action
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

### Record Action
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

### Hangup Action
Ends the call after the recording is completed or if the call cannot be connected.
```json
"hangup"
```
## Usage
To use this SWML voicemail example, you need to integrate the provided SWML script with your SignalWire application. Customize the script as needed to fit your specific requirements.

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
