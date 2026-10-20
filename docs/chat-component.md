# The Chat component

```tsx
import { Chat, type ChatHandle } from "@webfinancedirect/ai-chat-react-native";
```

`Chat` fills the space it is given. Put it in a view with `flex: 1`, inside your safe area.

## Peer dependencies

The chat draws its sheets with `@gorhom/bottom-sheet`, its gestures with `react-native-gesture-handler` and `react-native-reanimated`, and SVG logos with `react-native-svg`. Your app installs them:

| Package | Version |
|---|---|
| `@gorhom/bottom-sheet` | 5 or later |
| `react-native-gesture-handler` | 2 or later |
| `react-native-reanimated` | 3 or later. Version 4 also needs `react-native-worklets`. Set up its Babel plugin as its documentation says. |
| `react-native-svg` | 13 or later |
| `expo-haptics` | Optional. With it, copying a reply gives a light tap. |

`Chat` (and `ChatView`) wrap themselves in `GestureHandlerRootView` and `BottomSheetModalProvider`: you add no provider for the chat. An app that already has them at its root keeps them; the chat's own sheets open in the chat's provider.

## Props

### Required

| Prop | Type | |
|---|---|---|
| `baseUrl` | `string` | Your wfd-ai-chats origin. |
| `backend` | `SessionBackend` | How your backend starts and lists conversations. See below. |
| `userKey` | `string` | Who is using the app on this device: the signed-in user's id, or a visitor's. Each one's current conversation is remembered separately. |

### `backend`

```ts
interface SessionBackend {
  create: (context: Record<string, string>) => Promise<{ sessionId: string; token: string; expiresAt: string }>;
  refresh: (sessionId: string) => Promise<{ sessionId: string; token: string; expiresAt: string } | null>;
  list?: () => Promise<Array<{ sessionId: string; title: string; createdAt: string; updatedAt: string; stage: string | null }>>;
  exportUrl?: (sessionId: string) => Promise<string>;
}
```

| Function | |
|---|---|
| `create` | Starts a conversation. Reject (throw) if your backend fails. |
| `refresh` | A new token for a conversation. Resolve with `null` when it is no longer this customer's; the chat then starts a new one. |
| `list` | Earlier conversations, newest first. Without it there is no history button. |
| `exportUrl` | A link to the conversation as a file. Without it there is no download button. |

Each maps to an endpoint in [Backend](backend.md).

### Optional

| Prop | Type | |
|---|---|---|
| `storage` | `KeyValueStorage` | Where the chat remembers things on the device. `AsyncStorage` fits as it is. Without it nothing outlives the screen: the customer gets a new conversation each time. |
| `header` | `{ title, onBack?, actions? }` | The bar over the conversation. Leave it out to draw your own and use the methods below. |
| `context` | `Record<string, string>` | Passed to `backend.create` for a new conversation. |
| `initialMessage` | `string` | Sent as the first message of a conversation that has none yet. |
| `sessionId` | `string` | Continue this conversation instead of the remembered one. |
| `account` | see [Visitors and accounts](visitors.md) | What "Sign up" and "Already have an account? Log in" do. |
| `onLeaveHold` | `(held: boolean) => void` | See "Leaving the chat". |
| `theme` | partial `Theme` | See [Theming](theming.md). |
| `placeholder` | `string` | The text field's placeholder. |
| `firstName` | `string` | The signed-in customer's first name, for "Welcome back" over their recent conversations. |
| `assistantName` | `string` | The assistant's name in the chat's own copy ("Everything Alex is tracking for you"). Without it the assistant is Alex. |
| `onEvent` | `(name, payload) => void` | Things that happened in the conversation, for your analytics. Names and payloads are set by the chat's configuration. |
| `pickFile` | `(source) => Promise<PickedFile \| null>` | Your own file picker, used in place of Expo's. |
| `getPosition` | `() => Promise<{ lat, lng } \| null>` | Your own location provider, used in place of `expo-location`. |
| `renderText` | `(text, streaming) => ReactNode` | Draw a reply's text yourself. |
| `pasteTarget` | component | Your own paste view, used in place of the package's. |

### `header`

```tsx
header={{
  title: "Assistant",
  onBack: () => navigation.goBack(),
  actions: [{ id: "help", icon: "help-circle-outline", label: "Help", onPress: openHelp }],
}}
```

The chat's own buttons come first: read replies aloud, conversations, bookmarks, download, new conversation. Each shows only when it can work. Your `actions` follow. `icon` is an Ionicons name.

## While a reply is on its way

From the moment a message is sent until the first words arrive, the chat shows a spinner with a rotating status line. When the chat's configuration sets an ad carousel, it is shown under the line: your logo (PNG, JPEG, WebP or SVG; one that cannot be loaded is left out), a big line and a small line, turning every 6 seconds, with arrows and dots. Nothing to pass: the lines, the slides and the logo come from the chat's configuration. Colours follow your [theme](theming.md) (`primary`, `onPrimary`, `textMuted`).

## The location card

The card takes a ZIP code, a city ("Madison, WI") or a state ("MN" or "Minnesota"). A state searches the whole state. A ZIP, a city or the device's position (`getPosition`, or `expo-location`) is followed by the chat's radius choices and "Any distance". A city that exists in several states is asked about: the card offers each one.

## Methods

```tsx
const chat = useRef<ChatHandle>(null);
<Chat ref={chat} … />
```

| Method | |
|---|---|
| `send(text)` | Sends a message as if the customer had typed it. |
| `startNew()` | Starts a new conversation. |
| `openBookmarks()` | Opens the customer's bookmarks. |
| `leave(proceed)` | Runs `proceed` now, or after the rating prompt when one is due. |

## Leaving the chat

When a conversation is long enough, the chat asks for a rating once, on the way out. Its own back button handles that. The system's back gesture is yours, so hold it while the chat says so:

```tsx
const [held, setHeld] = useState(false);
usePreventRemove(held, ({ data }) => {
  chat.current?.leave(() => navigation.dispatch(data.action));
});

<Chat ref={chat} onLeaveHold={setHeld} … />
```

Without this, a customer who swipes back simply is not asked.

## What the customer gets

- Replies with formatting, tables and links; text can be selected and copied.
- Cards the chat sends: lists, details, choices, forms, consent, secure input, uploads, summaries.
- Attachments from the camera, the photo library or files, and pasted images and PDFs.
- Reading replies aloud, and dictation.
- Bookmarks (the button on a reply, or a double tap).
- Earlier conversations, a new conversation, and download.
- A rating, asked once per conversation.

Which of these a chat offers is set on the server. A feature that needs an optional module shows only when your app has it.
