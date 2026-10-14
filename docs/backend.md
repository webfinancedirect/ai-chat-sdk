# Backend

Your server holds the API key and does the few things that need it. Every request below goes to your wfd-ai-chats origin with this header:

```
authorization: Bearer <your API key>
content-type: application/json
```

Never ship the API key in an app, and never return it to one.

In all of them, `externalUserId` is **your** id for the customer. It decides who owns a conversation, so take it from the signed-in session on your server, never from a value the app sends.

## Start a conversation (required)

```
POST /v1/sessions
```

```json
{
  "slug": "your-chat-slug",
  "externalUserId": "user-123",
  "context": { "productId": "sku-42" }
}
```

| Field | |
|---|---|
| `slug` | Which chat to open. |
| `externalUserId` | Your id for the customer. |
| `context` | Optional. Strings the chat can use, for example where in your app the customer came from. Pass on what the app sent to `backend.create`. |
| `anonymous` | Optional. `true` for a visitor without an account: see [Visitors and accounts](visitors.md). |

Answer, `201`:

```json
{ "sessionId": "8221347d-…", "token": "…", "expiresAt": "2026-01-01T12:15:00.000Z" }
```

Return this object to the app as it is. It is what `backend.create` resolves with.

| Status | Meaning |
|---|---|
| `400` | The body is not valid. |
| `401` | Missing or wrong API key. |
| `403` | `anonymous` was sent, and this chat does not take visitors. |
| `404` | No such chat, or it is switched off. |
| `409` | The chat has nothing published yet. |

## Refresh a token (required)

Tokens are short-lived. The package asks for a new one when it needs it.

```
POST /v1/sessions/{sessionId}/tokens
```

```json
{ "externalUserId": "user-123" }
```

Answer, `200`: the same shape as above. `404` means the conversation does not exist or is not this customer's: return a `404` to the app, and have `backend.refresh` resolve with `null`. The package then starts a new conversation.

## List a customer's conversations (optional)

Needed for the history button.

```
GET /v1/sessions?slug=your-chat-slug&externalUserId=user-123&limit=30
```

Answer, `200`:

```json
{
  "sessions": [
    { "sessionId": "…", "title": "Do you have this in blue…", "createdAt": "…", "updatedAt": "…", "stage": "browsing" }
  ]
}
```

Newest first. `limit` is 30 by default and at most 50. `backend.list` resolves with the `sessions` array.

## Hand a visitor's conversation to their new account (optional)

```
POST /v1/sessions/{sessionId}/claim
```

```json
{ "from": "visitor-id-the-session-was-started-with", "to": "user-123" }
```

Answer, `200`: a token for the conversation, now owned by `to`. `404`: unknown, not that visitor's, or already claimed. See [Visitors and accounts](visitors.md).

## Export (optional)

`backend.exportUrl(sessionId)` resolves with a link the app opens. What the file is, is yours to decide. To build it, read the conversation with a token for that customer:

```
GET /v1/sessions/{sessionId}/transcript?after=0
authorization: Bearer <session token>
```

## A minimal example (Express)

```ts
const chats = axios.create({
  baseURL: process.env.WFD_AI_CHATS_ORIGIN,
  headers: { authorization: `Bearer ${process.env.WFD_AI_CHATS_API_KEY}` },
  validateStatus: () => true,
});
const SLUG = "your-chat-slug";

app.post("/chat/session", requireUser, async (req, res) => {
  const answer = await chats.post("/v1/sessions", { slug: SLUG, externalUserId: req.user.id, context: req.body.context ?? {} });
  if (answer.status !== 201) return res.status(502).json({ error: "chat_unavailable" });
  res.status(201).json(answer.data);
});

app.post("/chat/session/:id/token", requireUser, async (req, res) => {
  const answer = await chats.post(`/v1/sessions/${encodeURIComponent(req.params.id)}/tokens`, { externalUserId: req.user.id });
  if (answer.status === 404) return res.status(404).json({ error: "not_found" });
  if (answer.status !== 200) return res.status(502).json({ error: "chat_unavailable" });
  res.json(answer.data);
});
```
