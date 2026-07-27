# LibriDomi

A completely private, single-file EPUB reader. No accounts, no server, no analytics, no tracking.

**🔗 [Try it out here](https://gordoncopestake.github.io/LibriDomi/LibriDomi.html)**

## What it is

LibriDomi is one HTML file (`LibriDomi.html`) that runs entirely in your browser. It stores EPUBs, covers, reading positions, and preferences in IndexedDB — nothing leaves your device.

## Features

- **Private by design** — CSP headers injected into every EPUB at import time block remote images, fonts, media, and scripts
- **Single file** — everything in one `.html`, no build step, no dependencies to install
- **Offline-capable** — works once the CDN dependencies (JSZip, EPUB.js) are cached
- **Reading positions** — CFI-based progress tracking saved per book, with "furthest read" prompt
- **Chapter ticks** — progress bar shows tick marks at actual TOC chapter positions
- **Themes** — Paper, Sepia, Dark (inline `!important` styles override EPUB stylesheets)
- **Font controls** — publisher/serif/sans font stacks, 80%–250% size scaling
- **Touch gestures** — swipe to turn pages, tap zones for prev/next, disabled on touch for ticks
- **Keyboard navigation** — arrow keys, PageUp/PageDown, space
- **Responsive** — works on desktop, tablet, and mobile with safe-area insets
- **Duplicate detection** — SHA-256 content hashing prevents the same EPUB being stored twice

## Quick start

```bash
cd /path/to/LibriDomi
python3 -m http.server 9999 --bind 0.0.0.0
```

Open `http://localhost:9999/LibriDomi.html` (or your machine's hostname/IP).

## Dependencies

Pinned CDN scripts with SRI integrity hashes (configured at the top of the file):

| Library | Version | Purpose |
|---------|---------|---------|
| [JSZip](https://stuk.github.io/jszip/) | 3.10.1 | EPUB unzip + CSP injection |
| [EPUB.js](https://github.com/futurepress/epub.js) | 0.3.93 | Rendering engine |

Upgrade by changing the version numbers and integrity hashes in the `<script>` tags near the top of the file.

## Configuration

All configuration is in one place near the top of the `<script>` block:

| Constant | Default | Purpose |
|----------|---------|---------|
| `APP_NAME` | `'LibriDomi'` | App name shown in UI |
| `APP_FULL_NAME` | `'LibriDomi Local'` | Full name for page titles |
| `DB_NAME` | `'libridomi'` | IndexedDB database name |
| `LOCATION_CHAR_BREAK` | `300` | Chars between location markers (smaller = more granular ticks) |
| `FONT_RANGE` | `{ min: 80, max: 250, step: 10 }` | Font size range in percent |

## Storage

IndexedDB with four object stores:

| Store | Key | Contents |
|-------|-----|----------|
| `books` | `id` (content hash) | Metadata, cover blob, filename, timestamps |
| `files` | `bookId` | Raw EPUB blob |
| `progress` | `bookId` | CFI, percentage, furthest position, completed flag |
| `settings` | `key` | Preferences (theme, font, font size) |

## Privacy

- No accounts, no application server, no analytics, no tracking
- EPUB content is sandboxed with CSP headers injected at import time
- `allowScriptedContent: false` — no JavaScript inside EPUBs is executed
- URL imports require HTTPS, use `credentials: 'omit'` and `referrerPolicy: 'no-referrer'`
- External links inside books require explicit confirmation
- SHA-256 content hashing for duplicate detection (FNV-1a fallback if Web Crypto unavailable)

## License

MIT
