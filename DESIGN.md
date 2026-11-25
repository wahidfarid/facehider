# FaceHider Design System

A single-page, zero-dependency browser tool: upload a customer photo, click each face,
get a random whitelisted emoji over it, download the anonymized image. Everything runs
client-side; photos never leave the device.

## 0. Research Log

- Embedded refs: shortlisted `vercel.md`, `notion.md`, `ollama.md` (utility/neutral moods) → picked **taste-skill (Layer A)** + **vercel.md (Layer B)** because the brief is an operational, no-brand utility where quiet monochrome precision keeps the photo as the only loud element on screen.
- Lazyweb: skipped — the user asked for a quick, single-purpose tool with a fixed feature set (upload, click, random whitelisted emoji, export); no design direction needed beyond the loaded references. Named per the greenfield-lane rule.
- Imagen drafts: skipped — no image-generation tool is available in this session; named per the lane rule.
- StyleGallery: fetched `patterns/viewport-shell/panel-layout.md` via the sgfetch recipe → the stage/controls shell is a panel layout: `panel_layout` grid (`repeat(auto-fit, minmax(min(22rem,100%),1fr))`), main = image stage, side = emoji controls, with `min-inline-size: 0` on both. Scroll ownership decided separately below (layout-skill contracts).

Design read: operational utility for a shop owner, trust-first (their customers' photos), single task flow. Dials: DESIGN_VARIANCE 3, MOTION_INTENSITY 2, VISUAL_DENSITY 4.

## 1. Atmosphere & Identity

A quiet photo counter. The chrome is near-invisible monochrome; the photo is the only
thing with color, so the eye goes straight to it. The signature is the shadow-as-border
line (Vercel's `0 0 0 1px rgba(0,0,0,.08)`): panels and controls are separated by hairline
rings, never by boxes or colored bars. Emojis are the product's content, not decoration —
they appear only in the picker and on the photo.

## 2. Color

### Palette

| Role | Token | Light | Dark | Usage |
|------|-------|-------|------|-------|
| Surface/page | --surface-page | #FFFFFF | #0A0A0A | Page background |
| Surface/panel | --surface-panel | #FAFAFA | #141414 | Control rail, toolbar |
| Surface/stage | --surface-stage | #EDEDED | #1B1B1B | Area behind the photo |
| Surface/hover | --surface-hover | #F0F0F0 | #1F1F1F | Hover wash on controls |
| Surface/active | --surface-active | #E6E6E6 | #262626 | Pressed wash |
| Text/primary | --text-primary | #171717 | #FAFAFA | Titles, labels, primary button bg |
| Text/secondary | --text-secondary | #4D4D4D | #A0A0A0 | Body, hints |
| Text/tertiary | --text-tertiary | #6A6A6A | #8A8A8A | Placeholders, disabled (≥4.5:1 on panel) |
| Border/ring | --ring-border | rgba(0,0,0,0.08) 0 0 0 1px | rgba(255,255,255,0.14) 0 0 0 1px | Shadow-as-border on panels/buttons |
| Border/hairline | --hairline | #EBEBEB | #262626 | Dividers inside panels |
| Accent/primary | --ink-invert | #171717 bg / #FFFFFF text | #FFFFFF bg / #0A0A0A text | Download (primary action) |
| Focus/focus | --focus-ring | #0072F5 | #3B82F6 | focus-visible outline, never decorative |
| Status/destructive | --danger | #C4241D | #FF6369 | Clear-confirm text/press |

### Rules
- One accent: blue exists only as the focus ring. The primary action (Download) is the dark ink-invert button.
- Nothing decorative carries color. Emoji glyphs in the picker are content.
- Photo stage stays tonal in both themes (no checkerboard, no brand color).

## 3. Typography

### Scale

| Level | Size | Weight | Line Height | Tracking | Usage |
|-------|------|--------|-------------|----------|-------|
| H1 (app name) | 15px | 600 | 1.4 | -0.01em | Toolbar title |
| Body | 14px | 400 | 1.5 | 0 | Buttons, labels, body |
| Body/sm | 13px | 400 | 1.5 | 0 | Panel section labels, hints |
| Caption | 12px | 500 | 1.4 | 0.02em | Count chip, footer note |

(One-screen tool: no display type. H1-to-body contrast carried by weight and tracking.)

### Font Stack
- Primary: `system-ui, -apple-system, "Segoe UI", sans-serif` — self-contained file, no network font load.
- Emoji: `"Apple Color Emoji", "Segoe UI Emoji", "Noto Color Emoji", "Twemoji Mozilla", sans-serif` — used for emoji content only.

### Rules
- No serif, no mono. Two families total (system UI + emoji).
- Body text never below 14px on desktop; below 48rem primary copy bumps to 17px and the 13px secondary roles to 15px (phone floors).

## 4. Spacing & Layout

### Base Unit
4px. Toolbar height 52px; panel width ~20rem desktop.

| Token | Value | Usage |
|-------|-------|-------|
| --space-1 | 4px | icon-to-label |
| --space-2 | 8px | inside buttons, emoji grid gaps |
| --space-3 | 12px | control rows |
| --space-4 | 16px | panel padding, section gaps |
| --space-6 | 24px | stage padding around photo |

### Layout line

Layout: FaceHider workspace — fixed toolbar + image stage + emoji control rail -> panel-layout (https://github.com/changeroa/StyleGallery, patterns/viewport-shell/panel-layout.md) composed with scroll-body-shell mechanics; scroll owners: on ≥48rem the shell is `100dvb` grid rows [toolbar auto / workspace minmax(0,1fr)], workspace is a panel grid (stage main + 20rem side), the side rail owns internal scroll (`min-height:0; overflow:auto`), the stage owns none (photo fits its slot via max-width/max-height + aspect preserved); on <48rem the same shell stacks rows [stage minmax(0,1fr) / rail 42dvb] so the rail still owns the only internal scroll — document scroll never owns the workspace; anti-patterns: two panels fighting over the wheel, `100vh` on iOS, grid children missing `min-size:0`, media slot that pushes the toolbar off-screen.

### Rules
- Breakpoint: single intrinsic flip at 48rem via `auto-fit` minmax (panel-layout contract), plus the rail's 42dvb height swap below it.
- Empty / loading / error for the stage: empty = dropzone with privacy note; loading = skeleton shimmer of the slot; error = inline cause next to the slot (bad file type, decode failure).

## 5. Components

### Toolbar (header)
- **Structure**: `header` > cluster(title) + cluster(status chip, icon buttons, primary button)
- **Variants**: none
- **Spacing**: height 52px, padding-inline --space-4, gap --space-3
- **States**: buttons idle/hover/active/focus-visible/disabled (disabled = 40% opacity, no pointer); Clear has a two-step confirm state (idle → "Confirm clear" destructive for 3s → back); below 48rem the armed state is a transient mode that replaces the other toolbar actions so the confirm label always fits (320px included)
- **Accessibility**: all buttons ≥40×40 CSS px hit area, visible focus ring, aria-labels on icon-only buttons, count chip is a live region ("3 faces covered")
- **Motion**: none (state washes only)
- **Layout**: cluster; toolbar is the shell's fixed row

### Photo stage (main panel)
- **Structure**: `main.stage` > empty dropzone (label+input[type=file]) OR `.photo-slot` (relative, aspect-ratio from image) > `img` + absolutely-positioned `.emoji-cover` buttons + `.click-layer`
- **Variants**: empty (dropzone), loaded
- **Spacing**: stage padding --space-6; slot centered, max-inline-size 100%, max-block-size 100%
- **States**: empty (browse hint, drag-over highlight), loaded, error (inline, next to slot)
- **Accessibility**: click layer is a real button element ("Place emoji"); each placed emoji is a `<button aria-label="Emoji: 😀">` (click = reroll, double-click = remove, pointer-drag = move); Delete/Backspace removes last; Escape does nothing destructive
- **Motion**: placed emoji scales in 120ms ease-out (reduced-motion: cross-fade)
- **Layout**: stage owns no scroll; slot is a media-fit frame centered in the panel

### Emoji rail (side panel)
- **Structure**: `aside` > search input > size row (label + range) > emoji grid (grouped by category, sticky headers) > footer (enabled count + Reset)
- **Variants**: none
- **Spacing**: padding --space-4; grid `repeat(auto-fill, minmax(40px,1fr))` gap --space-2
- **States**: emoji buttons idle/hover/active/focus-visible/checked (checked = check glyph + tonal wash, never a colored outline); search empty state ("No emoji matches 'x'"); zero-enabled hint at top of grid
- **Accessibility**: emoji buttons ≥40×40, aria-pressed for checked, aria-label = emoji char + common name for the starter set; rail owns the workspace's only scroll
- **Motion**: none
- **Layout**: scroll-body-shell body; grid is intrinsic (`min(40px,100%)` floor)

### Primary button (Download)
- **Structure**: dark ink-invert, radius 6px, label + download glyph
- **States**: disabled until an image is loaded; hover wash; active press (translate-y 1px); focus ring
- **Layout**: toolbar right cluster

## 6. Motion & Interaction

| Type | Duration | Easing | Usage |
|------|----------|--------|-------|
| Micro | 120ms | ease-out | emoji place-in scale, button washes |
| Standard | 200ms | ease-in-out | Clear confirm swap, drag-over highlight |

- Only `transform`, `opacity`, `filter` animate. Dragging uses pointer events on the emoji buttons with a `dragging` class (no transition while dragging).
- `prefers-reduced-motion`: place-in becomes opacity-only; everything else is state washes.
- No entrance animations, no marquees, no hover motion on non-interactive elements.

## 7. Depth & Surface

### Strategy: shadows (Vercel shadow-as-border, whisper-level)

| Level | Value | Usage |
|-------|-------|-------|
| Ring | `0 0 0 1px rgba(0,0,0,0.08)` (dark: rgba(255,255,255,0.14)) | Panels, buttons, inputs, chip |
| Raised | ring + `0 2px 4px rgba(0,0,0,0.04)` | Toolbar over stage |

### Radius

| Token | Value | Usage |
|-------|-------|-------|
| --radius-sm | 6px | Buttons, inputs |
| --radius-md | 10px | Panels, photo slot corners |
| --radius-full | 9999px | Count chip only |

### Rules
- No traditional borders on cards/panels — ring shadows only. Hairline `#EBEBEB` dividers inside the rail are the exception (neutral 1px rules are allowed).
- Nested corners concentric.

## 8. Accessibility Constraints & Accepted Debt

### Constraints
- WCAG 2.2 AA: contrast per table (placeholders use #6A6A6A, 4.5:1+ on panel surfaces); every interactive element ≥40×40 CSS px; visible `--focus-ring` outline on all controls, never fully covered; full keyboard path: Tab through toolbar → stage place button → placed emojis → rail; Escape closes nothing (no overlays exist); Delete/Backspace undo-last when focus is not in a text field; `prefers-reduced-motion` respected; reflow at 320px: toolbar buttons keep hit sizes, rail stacks below stage, no horizontal scroll of primary content.

### Accepted Debt
| Item | Location | Why accepted | Owner / Exit |
|------|----------|--------------|--------------|
| Emoji glyph appearance varies by OS font | Picker, canvas export | Self-contained file uses system emoji fonts; consistent per-platform look is acceptable for a quick internal tool | If brand-consistent emoji needed: bundle Twemoji assets |
| Single uniform emoji size (one slider scales all covers) | Stage | Uniform covers read cleaner on social posts; per-emoji resize rejected for scope | Add per-emoji handles on request |
| Cover center clamps to the image bounds; export crops covers at the edge | Stage, export | Center-clamp is the placement spec; a cover dragged to the border hangs half off on screen but crops cleanly in the PNG | Per-emoji bounds handling on request |
| Workspace flip is a hard 48rem media query, not an intrinsic auto-fit flip | index.html | The 42dvb rail row needs a media query anyway; rendered behavior is identical at the breakpoint | Adopt auto-fit only if the rail row moves |
| Count chip is screen-reader-only on phones | index.html mobile block | 390px cannot fit brand + chip + four 44px actions without double ellipsis; aria-live announcement is kept | Revisit if the chip text shortens ("3 faces") |
