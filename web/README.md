# @webfinancedirect/ai-chat-web

A complete chat for React websites, served by wfd-ai-chats. You render one component; the conversation, its cards and forms, file attachments, voice, history, bookmarks, ratings and the sign-up prompt for visitors all come with it, in a three-pane layout: the steps on the left, the conversation in the middle, what the customer collected on the right. What the chat says and does is configured on the server, so most changes need no release of your site.

```tsx
import { Chat } from "@webfinancedirect/ai-chat-web";
import "@webfinancedirect/ai-chat-web/styles.css";

<Chat
  baseUrl="https://your-wfd-ai-chats-origin"
  backend={{
    create: (context) => api.post("/chat/session", { context }),
    refresh: (sessionId) => api.post(`/chat/session/${sessionId}/token`),
  }}
  userKey={user.id}
  header={{ title: "Assistant" }}
/>
```

## How it fits together

```
your site  ──►  your backend  ──►  wfd-ai-chats        (start a conversation: needs your API key)
your site  ───────────────────►  wfd-ai-chats        (the conversation itself: needs only the session token)
```

Your API key never goes into the browser. Your backend uses it to start a conversation and hands the page a short-lived session token. From then on the package talks to wfd-ai-chats directly with that token.

So there are two parts to set up:

1. **Your backend:** two small endpoints (four with history and visitors). They are the same as for the React Native package: see [Backend](../docs/backend.md).
2. **Your site:** install the package, import its stylesheet and render `Chat`. See [Getting started](docs/getting-started.md).

## Install

```bash
npm install @webfinancedirect/ai-chat-web
```

`react` and `react-dom` (19 or later) are peer dependencies.

## Requirements

- React 19, in any bundler that imports CSS (Vite, webpack, Next.js).
- A current Chrome, Edge, Safari or Firefox. Dictation and reading aloud use the browser's own speech features and are offered only where the browser has them.
- A wfd-ai-chats origin, a chat slug and an API key. You get these from us. Tell us the origins your site is served from, so the chat accepts them.

## Documentation

- [Getting started](docs/getting-started.md)
- [Backend](../docs/backend.md): the endpoints your server needs
- [The Chat component](docs/chat-component.md): every prop and method
- [Visitors and accounts](../docs/visitors.md): chatting before sign-up (on the web, keep the visitor id in `localStorage`)
- [Theming](docs/theming.md)
- [Troubleshooting](docs/troubleshooting.md)

## Support

Open an issue in this repository. Do not include API keys, session tokens or customer data.

## License

Copyright © Web Finance Direct. All rights reserved. See [LICENSE](../LICENSE).
