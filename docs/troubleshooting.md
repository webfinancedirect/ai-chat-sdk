# Troubleshooting

**The chat shows an error straight away.**
`backend.create` failed or returned the wrong shape. It must resolve with `{ sessionId, token, expiresAt }` exactly as wfd-ai-chats answered your backend. Check your backend's logs: `401` from wfd-ai-chats means the API key is wrong, `404` that the chat slug is.

**A new conversation starts every time the screen opens.**
Pass `storage` (for example `AsyncStorage`), and keep `userKey` the same for the same customer.

**Text in replies cannot be selected on iOS, or pasting an image does nothing.**
The running app binary does not contain the package's native views. Rebuild the native app after installing the package. `nativeViews()` tells you what the binary has.

**There is no attach button.**
The chat is not configured to take files, or the app has no picker. Install `expo-image-picker` (and `expo-document-picker` for PDFs) with `expo-file-system`, or pass `pickFile`.

**There is no microphone button, or no "read aloud".**
The chat is not configured for voice, or `expo-speech-recognition` / `expo-speech` is not installed.

**The history or download button is missing.**
Add `backend.list` or `backend.exportUrl`. Visitors without an account get neither.

**The build fails on iOS after installing.**
Run `npx pod-install` again. The native views need the new architecture and iOS 16.4 or later.

**The package does not load in Expo Go.**
It contains native code. Use a development build.

**Still stuck?**
Open an issue with the package version, your React Native and Expo versions, the platform, and what you see. Leave out API keys, tokens and customer data.
