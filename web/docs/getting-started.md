# Getting started

## 1. Set up your backend

Your server starts conversations with your API key and hands the page a session token. See [Backend](../../docs/backend.md): the endpoints are the same for the web and React Native packages.

## 2. Install

```bash
npm install @webfinancedirect/ai-chat-web
```

## 3. Render the chat

```tsx
import { Chat, type SessionBackend } from "@webfinancedirect/ai-chat-web";
import "@webfinancedirect/ai-chat-web/styles.css";

const backend: SessionBackend = {
  // Your endpoint that calls POST /v1/sessions with your API key.
  create: (context) => fetch("/api/chat/session", { method: "POST", body: JSON.stringify({ context }) }).then((r) => r.json()),
  // Your endpoint that calls POST /v1/sessions/:id/tokens. Answer null for a 404.
  refresh: async (sessionId) => {
    const r = await fetch(`/api/chat/session/${sessionId}/token`, { method: "POST" });
    return r.status === 404 ? null : r.json();
  },
};

export const AssistantPage = () => (
  <div style={{ height: "100vh" }}>
    <Chat baseUrl="https://your-wfd-ai-chats-origin" backend={backend} userKey={currentUser.id} header={{ title: "Assistant" }} />
  </div>
);
```

The chat fills the window. Inside a page with its own header and navigation, pass `embedded` and give the chat's container a height: it then fills the container instead.

## 4. Add what you need

- **History and export:** add `list` and `exportUrl` to `backend`.
- **Visitors:** see [Visitors and accounts](../../docs/visitors.md) and the `account` prop.
- **The address bar:** pass `remember={false}`, `sessionId` from the URL and `onSessionChange` to write it back, and every page load opens the conversation the URL names.
- **Your look:** see [Theming](theming.md).
