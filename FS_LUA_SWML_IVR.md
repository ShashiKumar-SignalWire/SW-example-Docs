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
