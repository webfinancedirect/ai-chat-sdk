# The Chat component

```tsx
import { Chat, type ChatHandle } from "@webfinancedirect/ai-chat-web";
import "@webfinancedirect/ai-chat-web/styles.css";
```

The stylesheet is required: the chat's layout and every card are drawn by it.

## Props

### Required

| Prop | Type | What it is |
|---|---|---|
| `baseUrl` | `string` | The wfd-ai-chats origin. |
| `backend` | `SessionBackend` | How your backend starts and lists this customer's conversations. |
| `userKey` | `string` | Who is using the site: the signed-in user's id, or a visitor's (`guest:<id>`). Each one's conversation is remembered apart. |
| `header` | `{ title: string; subtitle?: string; logo?: ReactNode }` | The bar over the conversation. |

### `backend`

| Function | Required | What it does |
|---|---|---|
| `create(context)` | yes | Starts a conversation. Resolves `{ sessionId, token, expiresAt }`. |
| `refresh(sessionId)` | yes | A fresh token for a conversation; `null` when it is no longer this customer's. |
| `list()` | no | The customer's conversations, newest first. Without it there is no History button. |
| `exportUrl(sessionId)` | no | A link to the conversation as a PDF. Without it there is no Export button. |

### Optional

| Prop | Type | What it does |
|---|---|---|
| `chat` | `string` | For a tenant with more than one chat: which one this is: its workflow slug. Your `backend` must open and list that same chat. Each chat remembers its own current conversation. Leave it out for the default chat. |
| `storage` | `KeyValueStorage` | Where the conversation and the customer's choices are remembered. Default: `localStorage` (memory when the browser blocks it). |
| `remember` | `boolean` | `false`: each visit starts a new conversation unless `sessionId` names one. Default `true`. |
| `sessionId` | `string` | Continue this conversation (one named in the URL, or a visitor's just made the account's). |
| `onSessionChange` | `(sessionId) => void` | The conversation shown changed (a new one, one from the history). |
| `context` | `Record<string, string>` | Launch context for a new conversation (a VIN the customer came from). |
| `initialMessage` | `string` | Sent as the first message of a conversation that has none yet. |
| `account` | `{ onSignUp, onLogIn, copy? }` | For a visitor: where "Sign up" and "Log in" lead. Each gets the conversation's id. |
| `embedded` | `boolean` | Fill the container instead of the window. |
| `theme` | `Record<string, string>` | CSS custom properties on the chat's root. See [Theming](theming.md). |
| `ads` | `{ logo?: ReactNode; brandLabel?: string }` | The mark and name over the ad carousel shown while a reply is on its way. |
| `placeholder` | `string` | The text field's placeholder. |
| `assistantName` | `string` | The assistant's name in copy that talks about it. Default "Alex". |
| `onEvent` | `(name, payload) => void` | Events the chat's workflow emits (a consent answered), for your analytics. |
| `onProgress` | `(progress) => void` | Where the conversation stands, whenever it changes: `sessionId`, `stage`, `userMessages` (messages the customer has sent), `limited`, `feedback`, `anonymous`, `loaded`, `responding` (a reply is being worked on). |
| `transport` | `Transport` | Replaces `fetch` (tests). |

## Methods

```tsx
const chat = useRef<ChatHandle>(null);
<Chat ref={chat} … />
```

| Method | What it does |
|---|---|
| `send(text)` | Sends a message as if the customer had typed it. |
| `startNew()` | Starts a new conversation, after the rating prompt when one is due. |
| `leave(proceed)` | Runs `proceed` now, or once the customer has answered or skipped the rating prompt. Call it before navigating away. |
| `measure(part)` | Where a part of the chat is on screen, in viewport coordinates (`{ x, y, width, height }`), or `null` when it is not drawn right now. `part` is `"header"`, `"conversation"`, `"starters"`, `"composer"` or `"journey"`. For pointing at the chat in a tour of your site. |

## Replies the customer did not see

A reply that finishes while the tab is hidden is not acknowledged until the customer comes back. When the chat's configuration tells customers about replies they missed (a push to your mobile app), only those unacknowledged replies are sent.

## What the customer gets

- The top bar: New Chat, Export PDF, Voice, History, the bookmark pin (drag it onto a message, or double-click a message), the vehicle being discussed and the current step.
- The left pane: the chat's steps, with the current one marked.
- The conversation: messages with their time, read aloud, stop and regenerate; the steps a reply took; every card the chat sends; "↑ N new messages" for replies that arrived out of sight.
- The composer: text, files (picked, pasted or dropped), dictation, and the daily allowance.
- The right pane: the vehicle being discussed, favourites, recent results and bookmarks.
