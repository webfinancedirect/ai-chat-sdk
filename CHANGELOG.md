# Changelog

## 0.4.0

- Comparison for uploads

## 0.3.0

- New peer dependencies: `@gorhom/bottom-sheet` (5 or later), `react-native-gesture-handler` (2 or later), `react-native-reanimated` (3 or later; version 4 also needs `react-native-worklets`) and `react-native-svg` (13 or later). `expo-haptics` is optional: with it, copying a reply gives a light tap. Install the ones your app lacks and rebuild it. `Chat` and `ChatView` wrap themselves in `GestureHandlerRootView` and `BottomSheetModalProvider`, so your app adds no provider for them.
- A new default look throughout, with new theme defaults: `primary` and `userBubble` `#1A3063`, `surface` and `assistantBubble` `#F4F4F4`, `border` `#C9C9CA`, `text` `#373739`, `textMuted` `#8B9298`, `danger` `#E34843`. `Theme` gains `fonts` (`main`, `bold`, `semiBold` families; a weight left out uses `main`) and `fontSizes` (`heading` 18, `subheading` 14, `paragraph` 13.5, `small` 12, `extraSmall` 10; each can be overridden), and `Theme.colors` gains `icon`, `ink`, `warning`, `success`, `primaryShadeHigh`, `primaryShadeLow` and `primaryDark`. A theme built as a whole `Theme` object (not passed through `mergeTheme`) must add them. Sizes scale with the screen width.
- The conversation: the newest message is at the bottom and a sent message is brought to the top while its reply arrives. "↑ N new messages" leads to replies that came in further down. A reply first seen while it streams appears sentence by sentence, then its cards. Under each message: its time, copy, read aloud, bookmark (or a double tap on the reply), stop while a reply runs, and "regenerate" on the newest reply to a typed message. A message that could not be sent says so and is sent again with a tap.
- While the customer waits: a bubble with rotating status lines and a stop button, the step being worked on above it, and afterwards a "Worked for Ns" chip that opens the steps the reply took. Research on vehicles shows its own bubble. An inventory search shows where it looks ("Searching within 25 mi of Butler, WI 53007…") under the reply until its results arrive. The ad carousel's logo may be an SVG; a logo that cannot be loaded is left out.
- The vehicle cards, the vehicle details, the comparison, the favourites and the selected vehicle have a new look. A picked vehicle is shown under the reply that confirms it, after any search results and before the other cards, and the protection products (GAP, service contracts) are a card of their own (`wfd.inventory/products`), where rows are chosen, then confirmed or declined.
- The customer can send while a reply is running: the message shows at once and goes out when the reply ends. `ComposerApi.disabled` (and the `disabled` prop of `Composer`) now means only that sending is closed because the customer is over their allowance, the same as `locked`. A running reply no longer sets it.
- The header, composer, starter questions, usage line, rating chips and form, and the sign-up prompt have a new default look. The sign-up prompt's buttons now read "Sign up" and "Already have an account? Log in"; the default `step` text is "Create a free account to continue where you left off."
- Header buttons: `HeaderAction` takes `disabled` (drawn grey, takes no taps). "New conversation" and "Download" stay greyed until the customer has sent a message, and "New conversation" also while a reply is running.
- `ChatViewProps.onProgress` also reports `responding` (a reply is being worked on).
- The usage line reads "Daily AI limit reached — resets at HH:MM" and "Sending too fast — wait a moment". The composer's default placeholder is "Message the AI agent...".
- `Theme.colors` gains `placeholderGrey`, `red` and `mediumGrey`. A theme built as a whole `Theme` object (not passed through `mergeTheme`) must add them.
- A returning customer opening the chat (the backend has `list`, no `sessionId` handed over, no `initialMessage`, no `account`) is offered their three most recent conversations, "See all conversations" and "Start a new chat" instead of resuming the remembered one. Nothing is loaded or created meanwhile: "Start a new chat" starts a new conversation with its greeting, a typed message starts one without it, and a conversation picked is continued. `Chat` takes `firstName` for its "Welcome back" line; `ChatView` takes the `launcher` it draws and `deferOpen`.
- The strip under the composer shows the journey step and, when the server sends them, the vehicle being discussed, favourites, searches and compare. Its chips open a new journey sheet (`InsightsSheet`) at their section, where the vehicle being discussed and the favourites can be selected, compared or unstarred, and each search shows its first three results; compare opens the comparison. `Chat` and `ChatView` take `assistantName` for the sheet's copy (default "Alex"). `JourneyStrip` takes optional `insights`, `onOpen` and `onCompare`, its `journey` may be null, and `disabled` and `onSay` are now optional. A running reply no longer disables the strip.
- Bookmarks open in a bottom sheet: `BookmarksSheet` is opened and closed through its ref (`SheetHandle`), and no longer takes `visible`. A row's label is at most 40 characters.
- The conversation history has a new look: each conversation is named after its vehicle (`SessionSummary.vehicleName`, "General conversation" without one) and shows its deal status (`SessionSummary.status`, which the app may supply through `backend.list`; "Pending" without one). `HistorySheet` no longer takes `currentSessionId`. `historyTimestamp` now reads "Just now", "25m ago", "Yesterday", "4d ago", then the date.
- The workflow cards have a new look. A choice with `layout: "cards"` draws numbered next-step cards (the `primary` option filled; once one is chosen the others grey out); an answered choice collapses to its answer, or to the choice's `answer` text when the server sends one. A consent is a "Review & respond to consent" button that opens its text in a bottom sheet, where it is accepted or declined; after an accept the chat is covered by a short "Notifying the dealer…" / "Running your soft check…" / "Submitting your application…" card (chosen by the consent's title) until the reply starts. A summary's Edit opens a bottom sheet whose Done sends what changed. The SSN field's button stays closed until the number is complete and says what is wrong as it is typed. After "I'll type the details" the licence card says the details go in the chat, and "Skip for now" stays open until the details are in.
- Sheets (the item detail, the plugins' sheets and the conversation history) open as bottom sheets, which the system back button closes. While a reply runs, a card or sheet action that the assistant answers (Select, Done in a summary's edit sheet) waits, drawn disabled; the quiet ones (open details, star, compare) stay open. `SheetModal` and `InsightsSheet` take `busy`, and a sheet renderer receives it.
- An action sent while a reply runs (through `ChatClient.act`) waits for that reply and goes out after it, in tap order with held messages, leaving the running reply's status and steps as they are.
- The processing card after a consent goes away by itself after 45 seconds, and on the system back gesture.
- `Theme.colors` gains `primaryShadeMedium`.

## 0.2.0

- The location card takes a ZIP code, a city ("Madison, WI") or a state, and asks which one when a city name exists in several states. A state searches the whole state; a ZIP, a city or the device's position offers the chat's radius choices and "Any distance".
- While a reply is on its way, the chat shows a status line and, when the chat's configuration sets them, an ad carousel with your logo. It disappears as soon as the reply starts.

## 0.1.0

First public release.

- `Chat`: the whole chat screen in one component.
- Native message text (iOS) and paste input (iOS, Android).
- Attachments, reading aloud, dictation and location through optional Expo modules.
