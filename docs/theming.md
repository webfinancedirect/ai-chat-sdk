# Theming

Pass any part of the theme; the rest keeps its default.

```tsx
<Chat
  theme={{
    colors: { primary: "#1A3063", userBubble: "#1A3063", assistantBubble: "#F2F4F8" },
    radius: 12,
  }}
  …
/>
```

| Key | Default | Used for |
|---|---|---|
| `colors.primary` | `#1F6FEB` | Buttons, icons, active states |
| `colors.onPrimary` | `#FFFFFF` | Text on `primary` |
| `colors.background` | `#FFFFFF` | The screen, the header, the composer |
| `colors.surface` | `#F4F6F8` | Cards and sheets |
| `colors.text` | `#14171A` | Text |
| `colors.textMuted` | `#5B6670` | Secondary text |
| `colors.border` | `#DDE2E7` | Lines and outlines |
| `colors.userBubble` | `#1F6FEB` | The customer's messages |
| `colors.onUserBubble` | `#FFFFFF` | Text in the customer's messages |
| `colors.assistantBubble` | `#F4F6F8` | Replies |
| `colors.danger` | `#C62828` | Errors |
| `colors.warn` | `#B26A00` | Warnings |
| `colors.ok` | `#2E7D32` | Success |
| `colors.info` | `#1565C0` | Notices |
| `radius` | `14` | Corner radius |
| `spacing` | `(n) => n * 4` | The spacing scale |
| `fontFamily` | system | Text drawn by React Native |

On iOS, message text is drawn by the package's native text view in the system font; `fontFamily` does not apply to it.
