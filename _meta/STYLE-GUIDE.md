# Finovate SOP — Style Guide & Build Spec

Everything needed to build a new SOP that matches the existing set. If you're an AI assistant picking this repo up cold: **read this file first**, then copy `sop-template.html` as your starting point.

> This `_meta/` folder is authoring context only. The leading underscore means GitHub Pages (Jekyll) **excludes it from the published site** — it never appears at a public URL.

---

## What an SOP is, structurally

Each SOP is **one self-contained HTML file** (all CSS + JS inline, no external files except Google Fonts). It renders as a **single-page "card deck"**:

- a fixed **navy sidebar** (brand, subtitle, auto-generated nav, owners footer);
- a **main stage** showing one "chapter" (card) at a time;
- a **top progress bar + counter**, and **Back / Next** buttons;
- **← / →** arrow keys move between cards.

All content lives in one JavaScript array called `chapters`. A small engine below it builds the nav, injects the cards, and handles navigation. **You only edit the `chapters` array** — never the engine.

```js
const chapters = [
  { navTitle:"Welcome", num:"00", html:`... card HTML ...` },
  { navTitle:"Three dials", num:"01", html:`... card HTML ...` },
  // ...one object per card
];
```

- `navTitle` — short label shown in the sidebar nav.
- `num` — the number chip in the nav (e.g. `"00"`, `"01"`, `"04·B"`). Purely cosmetic.
- `html` — the card's inner HTML, written inside backticks (a JS template literal).

The counter (`01 / 11`) is currently **hard-coded** in the `.counter` div — update it to match your number of cards, or the deck still works but the count reads wrong. (The progress *bar* auto-scales; only the text is manual.)

---

## Brand tokens (CSS `:root` variables)

| Token | Value | Use |
| --- | --- | --- |
| `--navy` | `#202658` | primary brand, sidebar, headings |
| `--navy-deep` | `#191d44` | code blocks, gradients |
| `--navy-soft` | `#2c326a` | secondary navy |
| `--green` | `#00d683` | the accent — highlights, active nav, CTAs |
| `--green-deep` | `#00b46e` | green text on light backgrounds |
| `--teal` | `#16846b` | tertiary accent |
| `--bg` / `--bg-2` | `#f4f6fa` / `#eaeef5` | page + subtle fills |
| `--card` | `#ffffff` | tiles, tables |
| `--ink` / `--text` / `--text-soft` | `#202658` / `#343a55` / `#6a7090` | heading / body / muted text |

**Fonts:** `Montserrat` (weights 400–800) for everything, `JetBrains Mono` for code/paths. Loaded from Google Fonts in `<head>`.

**Favicon:** a navy rounded square with a green sunburst mark, inlined as an SVG data-URI in `<link rel="icon">`. Copy it verbatim — it's identical across all pages.

**Brand mark:** `Claude <em>×</em> Finovate` (the `×` is green). **Owners footer:** `Owners · Yanga & Milan`.

---

## Component cheat-sheet

All of these are demonstrated live in `sop-template.html`. Drop them inside a card's `html`.

| Component | Markup | Notes |
| --- | --- | --- |
| **Section kicker** | `<div class="eyebrow">Section 01 · …</div>` | small green uppercase label |
| **Heading** | `<h1>Text <em>highlight</em></h1>` | `<em>` renders green, not italic |
| **Lead paragraph** | `<p class="lead">…</p>` | larger, muted intro line |
| **Tiles** | `<div class="grid three\|two"><div class="tile"><div class="ico">LABEL</div><h3>…</h3><p>…</p></div>…</div>` | responsive card row |
| **Table** | `<table class="tbl"><thead>…<tbody>…</table>` | styled; `<strong>` in a cell becomes a block label |
| **Callouts** | `<div class="call note\|imp\|tip"><span class="k">Label</span><p>…</p></div>` | `note`=neutral, `imp`=important, `tip`=positive |
| **Numbered steps** | `<div class="steps"><div class="step"><div class="badge"></div><div class="body"><h3>…</h3><p>…</p></div></div>…</div>` | badges auto-number (`01, 02…`); leave `.badge` empty |
| **Do / Don't** | `<div class="dd"><div class="col yes">…<div class="col no">…</div>` | ticks vs crosses |
| **Code block** | `<div class="code"><div class="bar"><span>label</span><button class="copy" onclick="cp(this)">Copy</button></div><pre>…</pre></div>` | copy button works out of the box |
| **Path / inline chip** | `<span class="path">Settings → Connectors</span>` | green monospace pill for UI paths |
| **Keyboard key** | `<span class="kbd">Ctrl</span>` | small key cap |
| **Hero cover** | `<div class="cover hero">…</div>` | navy gradient card; use for the opening + closing cards. Inside it, add `<div class="meta"><div>Owner<b>…</b></div>…</div>` and a `<button class="cta" onclick="go(1)">Start →</button>` |
| **Screenshot slot** | `<div class="shot"><div class="cam">📷</div><div class="lbl">Caption</div></div>` | dashed placeholder; swap to `<div class="shot filled"><img src="…"></div>` when you have the image |

---

## House rules for writing SOPs

- **Tone:** plain, direct, a little warm. Short sentences. Speak to "you". Avoid jargon; when a term is unavoidable, define it once.
- **One idea per card.** If a card scrolls a lot, split it.
- **Open with a hero cover** (what this is, who it's for, prerequisites) and **close with a hero cover** (you're done / where to get help).
- **Highlight sparingly** — `<em>` green on one or two words per heading, not whole phrases.
- **British spelling** (`organise`, `colour`) — matches `lang="en-GB"`.

---

## Filenames & publishing

- Lowercase, hyphenated, prefixed `finovate-claude-` (e.g. `finovate-claude-security.html`). No spaces, no version numbers in the name.
- The public URL is `https://finovatesa.github.io/SOP/<filename>`.
- **After adding a new SOP, add a matching card to `index.html`** (copy an existing `<a class="card">` block, bump the badge number, point `href` at the new file) so it appears on the landing page.
- Commit to `main` → GitHub Pages redeploys in ~1–2 min. No build step.
