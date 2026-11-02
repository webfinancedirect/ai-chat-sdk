# Theming

The chat is styled by `@webfinancedirect/ai-chat-web/styles.css`. Every rule is scoped to the chat's own class names (`ai-…`, `chat-…`), so it does not style the rest of your page.

Colours are CSS custom properties. Pass them in `theme` and each key is set as `--ai-<key>` on the chat's root:

```tsx
<Chat theme={{ accent: "#1a3063", "accent-2": "#2c4f9e" }} … />
```

The keys the stylesheet reads: `accent`, `accent-2`, `accent-light`, `primary`, `ink`, `ink-muted`, `line`, `panel`, `panel-strong`, `bg-1`, `bg-2`, `ok`, `warn`, `danger` and `font-display`.

Buttons and fields take their font size from the page. To change anything else, override the class in your own stylesheet, loaded after the chat's.
