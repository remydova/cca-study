# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A single-page PWA flashcard app called **"the quiet study"** for CCA (Claude Certified Associate) exam prep. Remy Dova aesthetic — navy/amber/teal/lavender palette, lofi tone.

## Running it

No build step. Open `index.html` directly in a browser, or serve locally:
```
npx serve .
```
PWA install requires HTTPS or localhost with a service worker.

## Architecture

**`index.html`** — the entire app. Sections in order:
- Google Fonts CDN link (Playfair Display, Caveat, Lora)
- CSS with CSS custom properties for the palette
- HTML: petal canvas → `.app` shell → header → mode tabs → progress → card-area → overlay screens
- JS: card data → localStorage history → deck/session state → render functions → Web Audio rain → canvas petals

**Key state variables:**
- `deck[]` — shuffled card array for the current session
- `reviewQueue[]` — cards rated "review again" that replay at session end
- `phase` — `'main'` or `'review'` (review pass runs after deck is exhausted)
- `sessionRatings{}` — `{cardId: 'got'|'almost'|'review'}` for this session only
- `history{}` — persisted to `localStorage('quietStudyHistory')` as `{cardId: {lastRating, lastDate, reviewCount}}`

**"Tonight's cards" logic** (`getDueCards()`): returns cards where `history[id]` is absent, `lastDate !== today`, or `lastRating !== 'got'`.

**Rain**: white noise buffer → lowpass (4kHz) → bandpass (800Hz) → gain node. Single `AudioContext`, lazy-initialized on first toggle.

**Petals**: `<canvas id="petals">` fixed behind the app, `requestAnimationFrame` loop. 24 petals, ellipse shapes in muted palette colors.

## Palette

```
--bg:#0c0c18  --surf:#13131f  --surf2:#1a1a2e
--amber:#d4956a  --teal:#7fb5a0  --lav:#9b8bb4
--text:#e8ddd0  --dim:#6a5d50
```

## Adding cards

Add objects to the `CARDS` array in `index.html`:
```js
{ id: 11, front: "concept name", back: "explanation.", example: "remy dova real-world example." }
```
IDs must be unique — history is keyed by `id`. Update `sw.js` cache version string when making changes that should invalidate offline cache.

## Remy Dova voice

Card `back` and `example` fields should stay lowercase (no capitals except proper nouns), intimate and direct, never clinical. Examples should reference real Remy Dova project moments (Miso Floats, remy-studio, newsletter/video tools).
