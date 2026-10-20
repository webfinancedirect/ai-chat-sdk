# Visitors and accounts

A chat can be offered to someone who has not signed up yet. The server decides how far a visitor may go: how many messages, and which steps need an account. When a visitor reaches that point, the text field is replaced by a prompt with a "Sign up" button and an "Already have an account? Log in" link.

A chat takes visitors only if it is configured to. If it is not, starting an anonymous conversation answers `403`.

## 1. Give the visitor an id

Make one id per install and keep it (a UUID in `AsyncStorage` is enough). It is what the visitor's conversation is kept under.

## 2. Start conversations as anonymous

On your backend, for a visitor:

```json
POST /v1/sessions
{ "slug": "your-chat-slug", "externalUserId": "guest:6f1c…", "anonymous": true }
```

Make sure a visitor's id can never equal a real user's id: a prefix such as `guest:` does that. Refresh tokens with the same `externalUserId`.

## 3. Tell the chat where sign-up is

```tsx
<Chat
  userKey={`guest:${visitorId}`}
  backend={visitorBackend}
  account={{
    onSignUp: (sessionId) => { rememberForLater(sessionId); navigation.navigate("SignUp"); },
    onLogIn: (sessionId) => { rememberForLater(sessionId); navigation.navigate("SignIn"); },
  }}
  …
/>
```

Each callback gets the conversation's id (or `null` if there is none yet). Keep it: you need it in the next step.

To change the prompt's words:

```tsx
account={{
  …,
  copy: { step: { title: "Ready to go further?", body: "Create a free account to continue." } },
}}
```

The reasons are `step` (this step needs an account), `limit` (the visitor's messages are used up) and `network` (too many visitor chats from their network today).

## 4. After sign-in, make the conversation theirs

Call your backend, which calls `POST /v1/sessions/{sessionId}/claim` with `from` (the visitor id the conversation was started with) and `to` (the new user's id). Then open the chat as the signed-in user and hand it that conversation:

```tsx
<Chat userKey={user.id} backend={userBackend} sessionId={claimedSessionId} … />
```

The customer continues exactly where they stopped. A conversation can be claimed once.
