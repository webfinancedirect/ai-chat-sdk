# @webfinancedirect/ai-chat-react-native

A complete chat screen for React Native apps, served by wfd-ai-chats. You render one component; the conversation, its cards and forms, file attachments, voice, history, ratings and the sign-up prompt for visitors all come with it. What the chat says and does is configured on the server, so most changes need no app release.

```tsx
import { Chat } from "@webfinancedirect/ai-chat-react-native";

<Chat
  baseUrl="https://your-wfd-ai-chats-origin"
  backend={{
    create: (context) => api.post("/chat/session", { context }),
    refresh: (sessionId) => api.post(`/chat/session/${sessionId}/token`),
  }}
  userKey={user.id}
  storage={AsyncStorage}
  header={{ title: "Assistant", onBack: () => navigation.goBack() }}
/>
```

## How it fits together

```
your app  ──►  your backend  ──►  wfd-ai-chats        (start a conversation: needs your API key)
your app  ───────────────────►  wfd-ai-chats        (the conversation itself: needs only the session token)
```

Your API key never goes into the app. Your backend uses it to start a conversation and hands the app a short-lived session token. From then on the package talks to wfd-ai-chats directly with that token.

So there are two parts to set up:

1. **Your backend:** two small endpoints (four with history and visitors). See [Backend](docs/backend.md).
2. **Your app:** install the package and render `Chat`. See [Getting started](docs/getting-started.md).

## Install

```bash
npm install @webfinancedirect/ai-chat-react-native @expo/vector-icons @gorhom/bottom-sheet react-native-gesture-handler react-native-reanimated react-native-worklets react-native-svg
```

Then rebuild the native app (`npx pod-install`, or `npx expo prebuild` and a new build): the package contains native code.

Optional modules, each used only if your app has it:

| Install | For |
|---|---|
| `expo-image-picker`, `expo-document-picker`, `expo-file-system` | Attaching photos and PDFs |
| `expo-speech` | Reading replies aloud |
| `expo-speech-recognition` | Dictating a message |
| `expo-location` | "Use my location" on the location card |

## Requirements

- React Native with the new architecture, React 19. Built and tested on React Native 0.86 with Expo SDK 57.
- iOS 16.4 or later for the native views.
- A wfd-ai-chats origin, a chat slug and an API key. You get these from us.

## Documentation

- [Getting started](docs/getting-started.md)
- [Backend](docs/backend.md): the endpoints your server needs
- [The Chat component](docs/chat-component.md): every prop and method
- [Visitors and accounts](docs/visitors.md): chatting before sign-up
- [Theming](docs/theming.md)
- [Native views and optional modules](docs/native.md)
- [Troubleshooting](docs/troubleshooting.md)

## Support

Open an issue in this repository. Do not include API keys, session tokens or customer data.

## License

Copyright © Web Finance Direct. All rights reserved. See [LICENSE](LICENSE).
