# VideoValet — UI Reference

This repo is the front-end reference for VideoValet. It contains the full working UI prototype — use it to match the design, component patterns, and interaction logic when building the production app.

---

## Quick Start

No build step needed. Open either file directly in a browser:

```
index.html   → landing page / marketing site
app.html     → the editor (main UI to integrate)
```

---

## What's in the UI

### Layout (`app.html`)

Two-panel split layout:

| Panel | Width | Contents |
|---|---|---|
| Chat (left) | 55% | Conversation history + text input |
| Files (right) | 45% | File grid, output card, processing card |

On mobile it collapses to a tab switcher (Chat / Files).

### Components

- **File card** — thumbnail, selection ring, number badge, file meta pill, large-file warning badge
- **Chat bubble** — user messages (right, orange-tinted) and AI responses (left, dark)
- **Processing card** — spinner, progress bar with glow animation, step label + percentage
- **Output card** — thumbnail preview, filename, meta pills, download + share buttons
- **Upload button** — triggers file picker, accepts MP4 / MOV / AVI / MKV / WEBM
- **Header** — macOS-style dots, back link, branding, file count

### Design Tokens

| Token | Value | Usage |
|---|---|---|
| Background | `#0a0a0a` | Page, chat panel |
| Surface | `#0f0f0f` | Header, cards |
| Border | `#1a1a1a` | All dividers |
| Accent | `#f97316` | Buttons, selection, progress bar |
| Accent hover | `#fb923c` | Button hover state |
| Text primary | `#e5e5e5` | Body text |
| Text muted | `#a3a3a3` | Labels, meta |
| Text dim | `#525252` | Placeholders, timestamps |
| Font | `Inter` + `monospace` | UI labels use mono |
| Border radius | `0.75rem` (cards), `0.5rem` (badges) | |

---

## API Integration

The UI makes two types of API calls:

### 1. Claude (NL → FFmpeg command)

```js
// app.html — askClaude()
POST https://api.anthropic.com/v1/messages
Headers:
  x-api-key: YOUR_ANTHROPIC_API_KEY_HERE  // ⚠️ see setup below
  anthropic-version: 2023-06-01
  anthropic-dangerous-direct-browser-access: true
Body:
  model: claude-haiku-4-5-20251001
  system: "translate natural language video commands into FFmpeg parameters..."
  messages: [{ role: "user", content: "<command> — files: [<filename>, ...]" }]
```

**Response shape the UI expects:**
```json
{
  "content": [{ "text": "{ \"action\": \"trim\", \"params\": { ... } }" }]
}
```

### 2. KITTY API (session + file upload + processing)

```js
const KITTY_API = 'https://ai-kitty.web-cardinalblue.workers.dev';

// Create session
POST /api/sessions → { sessionId }

// Upload file
POST /api/sessions/:sessionId/upload  (multipart/form-data, field: "file")

// Process — the UI sends the parsed FFmpeg params here after Claude responds
// (see processCommand() in app.html for full flow)
```

---

## Setup

### API Key

```js
// app.html, line 331 — replace with your key
const ANTHROPIC_API_KEY = 'YOUR_ANTHROPIC_API_KEY_HERE';
```

Get a key at [console.anthropic.com](https://console.anthropic.com). In production this call should move server-side so the key is never exposed in the browser.

### Demo Files

The editor expects `example_video_1.mp4` and `example_video_2.mp4` in the same folder for demo mode. Real uploads work without them.

---

## Client-Side Video Processing

Some operations run entirely in the browser without hitting the backend:

- **Side-by-side** — two videos rendered to a `<canvas>` and captured via `MediaRecorder`

See `clientSideSideBySide()` in `app.html`. Output is a Blob URL handed back to the output card.

---

## Stack

| | |
|---|---|
| Styling | Tailwind CSS (CDN), custom CSS for animations |
| Font | Inter (Google Fonts) |
| Framework | Vanilla JS — no build step |
| Video processing | HTML5 Canvas API + MediaRecorder API |
| Storage | IndexedDB (`videovalet_db`) for pending files across page loads |

---

## Integrating into the Production App

The UI is intentionally self-contained in two files so you can reference any component in isolation. Recommended approach:

1. Use `app.html` as a **live visual spec** — open it side-by-side while building
2. Copy the CSS custom properties and component styles into your global stylesheet
3. Match the HTML structure of each component in your Vue templates
4. Wire your existing backend session/upload/processing logic to the same UI states (`processing-card`, `output-card`, etc.)

The key interaction states to implement:

| State | UI element |
|---|---|
| Idle | File grid with upload button |
| Files selected | Orange selection ring + selected badge |
| Processing | `#processing-card` visible, progress bar animating |
| Output ready | `#output-section` visible with download/share |
| Error | Chat bubble with error text |
