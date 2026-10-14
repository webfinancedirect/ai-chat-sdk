# Native views and optional modules

## Native views in the package

The package contains two native views. Installing it and rebuilding the app links them. There is nothing to configure.

| View | Platforms | What it does |
|---|---|---|
| Message text | iOS | Draws a message as one native text, so the customer can select across paragraphs and lists, as in any other app. On Android, React Native's own text already does this. |
| Paste input | iOS, Android | Pasting an image or a PDF into the text field attaches it. Needs Expo modules in the app, and `expo-file-system` to read the file. |

Both are looked up when the chat starts. An app binary that was built before the package was added does not have them, and the chat still works: replies are drawn with React Native's text, and the field pastes text only. That matters for over-the-air updates: JavaScript that reaches an older binary does not break it.

To see what the running binary has:

```ts
import { nativeViews } from "@webfinancedirect/ai-chat-react-native";

nativeViews(); // { messageText: true, pasteTarget: true }
```

After installing or updating the package, rebuild the native app once:

```bash
npx pod-install
```

## Optional modules

Each is used only when your app has it. Without one, the feature it serves is simply not offered.

| Module | Feature | Your app must also |
|---|---|---|
| `expo-image-picker` | Attach from the camera or the photo library | Declare camera and photo library usage (the module's config plugin does) |
| `expo-document-picker` | Attach a PDF | |
| `expo-file-system` | Read a picked or pasted file | |
| `expo-speech` | Read replies aloud | |
| `expo-speech-recognition` | Dictate a message | Declare microphone and speech recognition usage |
| `expo-location` | "Use my location" | Declare location usage |

The package asks for a permission only at the moment the customer uses the feature.

To use your own picker or location source instead, pass `pickFile` or `getPosition`: see [The Chat component](chat-component.md).
