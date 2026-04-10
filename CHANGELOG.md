# Changelog

All notable changes to VideoValet UI are documented here.

## [Unreleased]

## [2026-04-10]

### Changed
- Improved text contrast across `index.html` (nav links, drop zone subtext, format label)
- Drop zone border updated to a more neutral tone
- Accepted file types expanded: added GIF support in `index.html` and image formats in `app.html`
- Tooltip and header text contrast improvements in `app.html`
- Chat input focus ring refined
- Back link and editor label colors updated for better legibility

## [2026-04-08] — Initial public release

### Added
- `app.html` — main editor interface with two-panel layout (chat + file manager)
- `index.html` — landing/marketing page
- Natural language → FFmpeg command translation via Claude API
- File upload and job submission via KITTY backend (Cloudflare Workers)
- Progress polling with 2s interval
- Client-side side-by-side video rendering using Canvas + MediaRecorder
- Demo mode with fallback when no files are selected
- IndexedDB persistence for pending files
- Mobile tab fallback for split-panel layout
