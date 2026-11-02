# Changelog

## 0.1.0

- First release: `Chat`, the whole conversation in a three-pane layout, with every card the chat sends (next steps, consents, the pre-approval estimate, the application summary and its edit form, the location card, secure inputs, document uploads, vehicle results, details, favourites, comparison and protection products), attachments, dictation, reading aloud, history, bookmarks, export, ratings and the sign-up prompt for visitors.
- `onProgress`, `ChatHandle.measure(part)` (types `ChatProgress`, `ChatPart`, `PartFrame`), and acknowledging replies the customer saw (a reply that ended while the tab was hidden is acknowledged when they come back).
- `chat`: for a tenant with more than one chat, which one this is. Each chat remembers its own current conversation.
- `initialMessage` is asked in a new conversation, so a remembered one under way is not added to. A conversation handed over in `sessionId` is still continued.
