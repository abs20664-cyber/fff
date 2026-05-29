# Claude Chat

A polished, single-file web app that mimics the **claude.ai** chat interface and talks directly to Anthropic's [Messages API](https://docs.anthropic.com/en/api/messages) — the same API powering the assistant you're chatting with.

Open `index.html` in a browser. No build step, no server, no dependencies to install.

![single-file](https://img.shields.io/badge/single--file-html-orange) ![api](https://img.shields.io/badge/api-anthropic-c96442) ![streaming](https://img.shields.io/badge/streaming-SSE-success)

## Features

### The chat experience
- **claude.ai look**: cream/dark theme, sidebar with chats grouped Today / Yesterday / Earlier, rust-orange accent, Copernicus serif headings, the signature 8-pointed star mark.
- **Streaming responses** via SSE (token-by-token, smoothed with `requestAnimationFrame`).
- **Markdown** with syntax-highlighted code blocks (one-click copy), tables, blockquotes, and **KaTeX** math (`$x^2$` and `$$\int...$$`).
- **Personalized greeting** with your name and time of day.
- **Theme toggle** — light, dark, or follow system.

### AI features (uses the latest API capabilities)
- **Vision / images** — drag, drop, paste, or click to attach images. Sent as `image` content blocks; Claude can see and describe them.
- **Extended thinking** — toggle on for Sonnet 4.5 / Opus 4 / 4.1. The reasoning trace streams into a collapsible block above the answer.
- **Model picker**: Sonnet 4.5, Sonnet 4, Opus 4.1, Opus 4, Haiku 3.5.
- **Custom system prompt**.
- **Token usage** displayed under each response (`123 in · 456 out`).

### Conversation management
- **Edit any user message** and resend (truncates the branch and regenerates).
- **Regenerate** any assistant response.
- **Inline rename** — click the chat title in the topbar.
- **Search across conversations** (`⌘K` / `Ctrl+K`) with arrow-key navigation.
- **Export** the current chat as Markdown.
- All state in `localStorage` — no backend, no telemetry.

### Keyboard shortcuts
| Shortcut | Action |
|---|---|
| `⌘K` / `Ctrl+K` | Command palette + chat search |
| `⌘⇧O` / `Ctrl+Shift+O` | New chat |
| `⌘/` / `Ctrl+/` | Open settings |
| `Esc` | Close modal / stop streaming |
| `Enter` / `Shift+Enter` | Send / newline |

## Quick start

1. Open `index.html` in any modern browser. That's it.
2. On first launch, paste your [Anthropic API key](https://console.anthropic.com/settings/keys). It's stored in `localStorage` and only sent to `api.anthropic.com`.
3. Start chatting. Drag an image into the composer to test vision. Click the brain pill in the topbar to enable extended thinking.

> Prefer to serve it: `python3 -m http.server 8000` then open `http://localhost:8000`.

## How the AI is integrated

```
browser  ──HTTPS──▶  api.anthropic.com/v1/messages  (stream:true)
   ▲                                │
   └─── SSE: text/thinking deltas ◀┘
```

- Sends conversation history each turn (user/assistant pairs).
- Image messages become `[{type:"image", source:{type:"base64", ...}}, {type:"text", text:"..."}]` content blocks.
- With extended thinking enabled, sends `thinking: {type:"enabled", budget_tokens:4000}` and parses `thinking_delta` events into the reasoning trace.
- Uses the `anthropic-dangerous-direct-browser-access: true` header for browser CORS.

## Files

- `index.html` — the entire app (HTML + CSS + JS)
- `README.md` — this file

## Notes

- Storing API keys in `localStorage` is convenient for personal use but not appropriate for shared machines. For multi-user setups, put a thin proxy server in front and keep the key server-side.
- Extended thinking is shown only for models that support it (Sonnet 4.5, Opus 4 / 4.1).
- Update the `<select>` in `index.html` as new models ship.
