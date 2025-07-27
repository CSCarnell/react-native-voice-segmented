# React Native Voice Segmented

A speech-to-text library for React Native with **Android segmented session support**.

Forked from [@wdragon/react-native-voice](https://github.com/wdragon/react-native-voice) with additional features for advanced Android speech recognition capabilities.

##  New Features

### Segmented Session Support (Android 13+)

This fork adds support for Android's segmented session feature, which allows continuous speech recognition without timing out during pauses. This is particularly useful for:
- Long-form dictation
- Voice notes and memos  
- Continuous speech recognition applications
- Multi-sentence speech input

## Installation

```sh
yarn add @cscarnell/react-native-voice-segmented

# or

npm i @cscarnell/react-native-voice-segmented --save
```

Link the iOS package

```sh
npx pod-install
```

## Table of contents

- [Configuration in Expo](#expo-config)
- [Usage](#usage)
  - [Example](#example)
- [API](#api)

<h2 align="center">Configuration in Expo</h2>

After installing this npm package, add the [config plugin](https://docs.expo.io/guides/config-plugins/) to the [`plugins`](https://docs.expo.io/versions/latest/config/app/#plugins) array of your `app.json` or `app.config.js`:

```json
{
  "expo": {
    "plugins": ["@cscarnell/react-native-voice-segmented"]
  }
}
```

Then, add permissions inside the app.json configuration:

```json
"ios": {
      "supportsTablet": true,
      "bundleIdentifier": "com.anonymous.SpeectToTextApp",
 "infoPlist": {
        "NSSpeechRecognitionUsageDescription": "This app uses speech recognition to convert your speech to text.",
        "NSCameraUsageDescription": "This app uses the camera to let user put a photo in his profile page."
      }
    },
    "android": {
      "adaptiveIcon": {
        "foregroundImage": "./assets/adaptive-icon.png",
        "backgroundColor": "#FFFFFF"
      },
      "permissions": ["android.permission.RECORD_AUDIO"],
      "package": "com.anonymous.SpeectToTextApp"
    },
```

<h2 align="center">Usage</h2>

<p align="center"><a href="https://github.com/wdragon/react-native-voice/blob/master/example/src/VoiceTest.tsx">Full example for Android and iOS.</a></p>

### Example

```javascript
import Voice from '@wdragon/react-native-voice';
import React, {Component} from 'react';

class VoiceTest extends Component {
  constructor(props) {
    Voice.onSpeechStart = this.onSpeechStartHandler.bind(this);
    Voice.onSpeechEnd = this.onSpeechEndHandler.bind(this);
    Voice.onSpeechResults = this.onSpeechResultsHandler.bind(this);
  }
  onStartButtonPress(e){
    Voice.start('en-US');
  }
  ...
}
```

<h2 align="center">API</h2>

<p align="center">Static access to the Voice API.</p>

**All methods _now_ return a `new Promise` for `async/await` compatibility.**

| Method Name                          | Description                                                                                                                                                             | Platform     |
| ------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------ |
| Voice.isAvailable()                  | Checks whether a speech recognition service is available on the system.                                                                                                 | Android, iOS |
| Voice.start(locale)                  | Starts listening for speech for a specific locale. Returns null if no error occurs.                                                                                     | Android, iOS |
| Voice.stop()                         | Stops listening for speech. Returns null if no error occurs.                                                                                                            | Android, iOS |
| Voice.cancel()                       | Cancels the speech recognition. Returns null if no error occurs.                                                                                                        | Android, iOS |
| Voice.destroy()                      | Destroys the current SpeechRecognizer instance. Returns null if no error occurs.                                                                                        | Android, iOS |
| Voice.removeAllListeners()           | Cleans/nullifies overridden `Voice` static methods.                                                                                                                     | Android, iOS |
| Voice.isRecognizing()                | Return if the SpeechRecognizer is recognizing.                                                                                                                          | Android, iOS |
| Voice.getSpeechRecognitionServices() | Returns a list of the speech recognition engines available on the device. (Example: `['com.google.android.googlequicksearchbox']` if Google is the only one available.) | Android      |

<h2 align="center">Events</h2>

<p align="center">Callbacks that are invoked when a native event emitted.</p>

| Event Name                          | Description                                            | Event                                           | Platform     |
| ----------------------------------- | ------------------------------------------------------ | ----------------------------------------------- | ------------ |
| Voice.onSpeechStart(event)          | Invoked when `.start()` is called without error.       | `{ error: false }`                              | Android, iOS |
| Voice.onSpeechRecognized(event)     | Invoked when speech is recognized.                     | `{ error: false }`                              | Android, iOS |
| Voice.onSpeechEnd(event)            | Invoked when SpeechRecognizer stops recognition.       | `{ error: false }`                              | Android, iOS |
| Voice.onSpeechError(event)          | Invoked when an error occurs.                          | `{ error: Description of error as string }`     | Android, iOS |
| Voice.onSpeechResults(event)        | Invoked when SpeechRecognizer is finished recognizing. | `{ value: [..., 'Speech recognized'] }`         | Android, iOS |
| Voice.onSpeechPartialResults(event) | Invoked when any results are computed.                 | `{ value: [..., 'Partial speech recognized'] }` | Android, iOS |
| Voice.onSpeechVolumeChanged(event)  | Invoked when pitch that is recognized changed.         | `{ value: pitch in dB }`                        | Android      |


**Notes on Android**

Even after all the permissions are correct in Android, there is one last thing to make sure this libray is working fine on Android. Please make sure the device has Google Speech Recognizing Engine such as `com.google.android.googlequicksearchbox` by calling `Voice.getSpeechRecognitionServices()`. Since Android phones can be configured with so many options, even if a device has googlequicksearchbox engine, it could be configured to use other services. You can check which serivce is used for Voice Assistive App in following steps for most Android phones:

`Settings > App Management > Default App > Assistive App and Voice Input > Assistive App`

Above flow can vary depending on the Android models and manufactures. For Huawei phones, there might be a chance that the device cannot install Google Services.

**How can I get `com.google.android.googlequicksearchbox` in the device?**

Please ask users to install [Google Search App](https://play.google.com/store/apps/details?id=com.google.android.googlequicksearchbox&hl=en).

### iOS

Need to include permissions for `NSMicrophoneUsageDescription` and `NSSpeechRecognitionUsageDescription` inside Info.plist for iOS. See the included `VoiceTest` for how to handle these cases.

```xml
<dict>
  ...
  <key>NSMicrophoneUsageDescription</key>
  <string>Description of why you require the use of the microphone</string>
  <key>NSSpeechRecognitionUsageDescription</key>
  <string>Description of why you require the use of the speech recognition</string>
  ...
</dict>
```

## Advanced Features (Segmented Session)

### New Methods

| Method | Description | Platform |
|--------|-------------|----------|
| `Voice.startWithExtras(locale, extras, options)` | Start speech recognition with custom Android intent extras | Android, iOS (no-op) |

### New Events

| Event | Description | Platform |
|-------|-------------|----------|
| `Voice.onSpeechSegmentResults` | Called when segment results are available during segmented session | Android |

### Usage Examples

#### Basic Segmented Session

```javascript
import Voice from '@cscarnell/react-native-voice-segmented';

// Set up event handlers
Voice.onSpeechSegmentResults = (e) => {
  console.log('Segment results:', e.value);
};

// Start with segmented session (Android only)
Voice.startWithExtras('en-US', {
  'android.speech.extra.SEGMENTED_SESSION': true,
  'android.speech.extra.PARTIAL_RESULTS': true,
});
```

#### Advanced Configuration

```javascript
Voice.startWithExtras('en-US', {
  // Enable segmented session (Android 13+)
  'android.speech.extra.SEGMENTED_SESSION': true,
  
  // Enable partial results
  'android.speech.extra.PARTIAL_RESULTS': true,
  
  // Custom timeout values
  'android.speech.extra.SPEECH_INPUT_MINIMUM_LENGTH_MILLIS': 2000,
  'android.speech.extra.SPEECH_INPUT_COMPLETE_SILENCE_LENGTH_MILLIS': 5000,
  
  // Any other Android-specific extras
});
```

#### Continuous Dictation Example

```javascript
import React, { useEffect, useState } from 'react';
import { View, Text, Button, ScrollView } from 'react-native';
import Voice from '@cscarnell/react-native-voice-segmented';

const ContinuousDictation = () => {
  const [segments, setSegments] = useState([]);
  const [isListening, setIsListening] = useState(false);

  useEffect(() => {
    Voice.onSpeechStart = () => setIsListening(true);
    Voice.onSpeechEnd = () => setIsListening(false);
    
    Voice.onSpeechSegmentResults = (e) => {
      // Each segment is a complete phrase/sentence
      if (e.value && e.value.length > 0) {
        setSegments(prev => [...prev, e.value[0]]);
      }
    };

    Voice.onSpeechError = (e) => {
      console.log('Speech error:', e.error);
      setIsListening(false);
    };

    return () => Voice.destroy();
  }, []);

  const startContinuousListening = () => {
    Voice.startWithExtras('en-US', {
      'android.speech.extra.SEGMENTED_SESSION': true,
      'android.speech.extra.PARTIAL_RESULTS': true,
    });
  };

  return (
    <View style={{ padding: 20 }}>
      <Button
        title={isListening ? 'Stop' : 'Start Continuous Dictation'}
        onPress={isListening ? () => Voice.stop() : startContinuousListening}
      />
      
      <ScrollView style={{ marginTop: 20, maxHeight: 300 }}>
        {segments.map((segment, index) => (
          <Text key={index} style={{ marginBottom: 10 }}>
            {index + 1}. {segment}
          </Text>
        ))}
      </ScrollView>
    </View>
  );
};
```

## Compatibility & Migration

- **Android**: Segmented session requires Android 13+ (API 33+). On older versions, it gracefully falls back to standard behavior.
- **iOS**: The `startWithExtras` method works as a no-op that calls the regular `start` method.
- **Migration**: This library is fully backward compatible with `@wdragon/react-native-voice`. Simply replace the import and all existing functionality works exactly the same.

