# Troubleshooting

**The chat says "Could not load this conversation."**
Your backend's `create` or `refresh` failed, or the browser could not reach wfd-ai-chats. Check the network tab: the session request to your backend, then the requests to `baseUrl`.

**Requests to wfd-ai-chats fail with a CORS error, or a 403 "not embeddable on this site".**
The chat accepts only the origins it is configured for. Send us every origin your site is served from (production, staging, `http://localhost:5173` for development).

**"Invalid hook call", or two copies of React.**
The package uses your site's React. If your bundler resolves a second copy (a linked package, a monorepo), dedupe `react` and `react-dom` (Vite: `resolve.dedupe`).

**The chat has no height.**
Give its container a height, or render it without `embedded` to fill the window.

**No microphone or speaker button.**
The chat turns voice on, and the browser must offer the Web Speech API. Firefox has no dictation; reading aloud needs a voice installed on the device.
