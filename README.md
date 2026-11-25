# FaceHider

Anonymize customer photos with emoji, right in your browser — then post them to social media.

Open a photo, click each face, and a random emoji from your whitelist covers it. Download the anonymized PNG at the photo's original resolution. **Everything runs in your browser: photos never leave your device.**

**Live app: https://wahidfarid.github.io/facehider/**

## How to use

1. **Drop a photo** in the page (or click to browse, or paste from the clipboard).
2. **Click each face** — a random emoji from your whitelist covers it.
3. Fine-tune: click a cover to reroll it, drag to move it, double-click to remove it.
4. **Download PNG** and post it.

## Features

- Emoji **whitelist**: choose exactly which emojis the random picker may use (~300 across five categories; 12 classic faces pre-enabled). Your whitelist and cover size are remembered between visits.
- **Shuffle** re-rolls every cover; **Undo** (also Ctrl/Cmd+Z) and Backspace/Delete; two-step **Clear** confirmation.
- Adjustable cover size (4–16% of the image width).
- Keyboard accessible; works with mouse, touch, and pen; light and dark themes.

## Privacy

No server, no upload, no analytics — the single HTML file does everything locally in your browser. Closing the tab erases everything except your emoji whitelist and cover-size preference.

## Tech

One self-contained `index.html` (no build step, no dependencies, no fonts or images fetched at runtime). Overlays are placed as absolutely-positioned elements over the photo and re-drawn onto a full-resolution canvas at export time with the system emoji font. Design decisions are documented in [DESIGN.md](DESIGN.md).

## Run it locally

Open `index.html` in any modern browser (Chrome, Edge, Firefox, Safari) — or serve the folder however you like:

```sh
python3 -m http.server 8000
```

## License

[MIT](LICENSE)
