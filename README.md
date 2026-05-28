# Claude Chat

A self-contained web app that mimics the **claude.ai** chat interface and talks directly to the Anthropic API. Single HTML file — no build step, no server, no dependencies to install.

![preview](https://img.shields.io/badge/single--file-html-orange) ![api](https://img.shields.io/badge/api-anthropic-c96442)

## Features

- claude.ai-style UI: cream background, sidebar with conversation history, message bubbles, the signature rust-orange accent
- Streaming responses (token-by-token, like the real thing)
- Multiple conversations, grouped by Today / Yesterday / Earlier
- Markdown rendering with syntax-highlighted code blocks (one-click copy)
- Model picker: Sonnet 4.5, Sonnet 4, Opus 4.1 / 4, Haiku 3.5
- Optional custom system prompt
- Auto dark mode (follows OS theme)
- All data stays on your machine — API key and conversations live in `localStorage`
- Stop-mid-stream button, keyboard shortcuts (Enter to send, Shift+Enter for newline)

## Quick start

1. Open `index.html` in any modern browser. That's it.
2. On first launch you'll be asked for an [Anthropic API key](https://console.anthropic.com/settings/keys) — paste it in. It's stored locally in your browser and only sent to `api.anthropic.com`.
3. Start chatting.

> If you'd rather serve it locally:
> ```bash
> python3 -m http.server 8000
> # then open http://localhost:8000
> ```

## How the AI is integrated

The app calls the Anthropic [Messages API](https://docs.anthropic.com/en/api/messages) directly from the browser using the `anthropic-dangerous-direct-browser-access: true` header, with `stream: true` so responses arrive as Server-Sent Events. Conversation history is sent on every turn so the model has full context.

```
browser  ──HTTPS──▶  api.anthropic.com/v1/messages  (stream)
   ▲                                │
   └────── SSE: text deltas ◀──────┘
```

No backend, no proxy, no telemetry.

## Files

- `index.html` — the entire app (HTML + CSS + JS, ~700 lines)
- `README.md` — this file

## Notes

- Storing API keys in `localStorage` is convenient for personal use but not appropriate for shared machines or production deployments. For a multi-user setup, put a thin proxy server in front and keep the key server-side.
- The model list reflects current Anthropic offerings; update the `<select>` in `index.html` as new models ship.
