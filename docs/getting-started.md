# Getting started

## 1. Set up your backend

The app cannot start a conversation by itself: that takes your API key, which must stay on a server. Add the endpoints described in [Backend](backend.md) first. You need at least "start a conversation" and "refresh a token".

## 2. Install

```bash
npm install @webfinancedirect/ai-chat-react-native @expo/vector-icons
```

Add the optional modules for the features you want (see [Native views and optional modules](native.md)), then rebuild the native app:

```bash
npx pod-install
```

With Expo's managed workflow, make a new development or production build instead. The package does not work in Expo Go, because it contains native code.

## 3. Render the chat

```tsx
import AsyncStorage from "@react-native-async-storage/async-storage";
import { Chat, type SessionBackend } from "@webfinancedirect/ai-chat-react-native";
import { SafeAreaView } from "react-native-safe-area-context";

const backend: SessionBackend = {
  // Both return { sessionId, token, expiresAt } from YOUR backend.
  create: async (context) => (await api.post("/chat/session", { context })).data,
  refresh: async (sessionId) => {
    const response = await api.post(`/chat/session/${sessionId}/token`, {}, { validateStatus: (s) => s === 200 || s === 404 });
    return response.status === 200 ? response.data : null;
  },
};

export const ChatScreen = () => (
  <SafeAreaView style={{ flex: 1 }} edges={["top", "bottom"]}>
    <Chat
      baseUrl="https://your-wfd-ai-chats-origin"
      backend={backend}
      userKey={currentUser.id}
      storage={AsyncStorage}
      header={{ title: "Assistant", onBack: () => navigation.goBack() }}
    />
  </SafeAreaView>
);
```

That is a working chat. The customer's conversation is remembered per `userKey`, so they come back to where they left off.

Keep `backend` stable between renders (define it outside the component, or with `useMemo`).

## 4. Add what you need

| To get | Do |
|---|---|
| A list of earlier conversations | Add `backend.list` |
| "Download" in the header | Add `backend.exportUrl` |
| Chat for visitors without an account | See [Visitors and accounts](visitors.md) |
| Your colours | Pass `theme`: see [Theming](theming.md) |
| Analytics | Pass `onEvent` |

All props are in [The Chat component](chat-component.md).
