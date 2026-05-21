# Cloaked Reskin Prototype

A side-by-side comparison prototype showing the **original** Cloaked iOS Design System 4.0 against the **reskinned** version using `cloaked-design-md` tokens. Single self-contained HTML file — no build step, vanilla JS.

## Live URL

The Vercel project is **git-connected** (auto-deploys on push to `main`). The production URL gets a fresh hash on every deploy — pull the current one from the GitHub deployments API:

```bash
curl -s 'https://api.github.com/repos/hi-christine-design/cloaked-reskin/deployments?per_page=1' \
  | python3 -c "import json,sys,urllib.request; d=json.load(sys.stdin)[0]; s=json.loads(urllib.request.urlopen(d['statuses_url']).read()); print(s[0]['environment_url'])"
```

Last known URL: https://deploy-kbexvymck-hichristinechung-3891s-projects.vercel.app

(To pin a stable URL, configure a domain alias in Vercel → Settings → Domains.)

Vercel project: `deploy` under `hichristinechung-3891s-projects`. Project config is committed at `deploy/vercel.json` (`framework: null`, no build step — pure static).

## File structure

```
/Users/christinechung/Cloaked-reskin/
├── reskin-comparison.html    # Dev file — open directly in a browser to preview
├── deploy/                   # Vercel deployable copy
│   ├── index.html            # Same as reskin-comparison.html but with relative token paths
│   ├── tokens/               # Cloaked design tokens (colors, typography, spacing, themes)
│   ├── fonts/                # Licensed Simula font (Book + Italic) — not committed
│   ├── assets/reskin/        # Original PNG screenshots (used as fallback)
│   └── .vercel/              # Vercel project link
├── assets/                   # Mirror of deploy/assets/
└── fonts/                    # Mirror of deploy/fonts/
```

## Development workflow

1. Edit `reskin-comparison.html` directly — open in a browser to preview.
2. The right-side panels are HTML/CSS in the `reskins` object; the left-side panels are in `originals`.
3. Each pane renders side-by-side at **393×852** each, scaled to fit the viewport.

## Deploy workflow

```bash
# Edit deploy/index.html directly. (See "Source of truth" note below.)
git add deploy/index.html
git commit -m "..."
git push origin main
# Vercel webhook fires automatically; new deployment URL lands in ~30–60s.
```

**Source of truth — important:** `reskin-comparison.html` at repo root and `deploy/index.html` have diverged. **`deploy/index.html` is what ships to Vercel** (and is much larger — has the components view, playgrounds, tokens view, dark mode, etc.). `reskin-comparison.html` is an older/smaller dev file. When in doubt, edit `deploy/index.html`.

## What's in the prototype

20 screens, each with an HTML original (left) + HTML reskin (right):

`Homepage`, `Exposure status`, `Automations`, `Identity · Phone`, `Identity · Email`, `Identity · Inbox`, `Phone number`, `Call Guard`, `ID monitoring`, `VPN`, `Password manager`, `Spotify identity`, `Wallet`, `One-time passcode`, `Manage eSIM`, `Identity theft`, `Settings`, `Website`, `Messages`, `Menu`.

## Design system mapping

**Original** — Cloaked iOS Design System 4.0 (2026):
- Light cream/white surfaces (`#f7f7f7`), SF Pro typography
- Standard iOS components, liquid-glass bottom nav, semicircle risk meter
- Token names: `--neutral-*`, `--surface-l0-background`, `--brand-300-cloaked`, etc.

**Reskinned** — `cloaked-design-md`:
- Layered surfaces: dark hero (`#0b0b0a`) → light dashboard (`#f3f1ed`) → dark FAQ
- Simula serif headlines + SF Pro body
- Icon-only sticky bottom nav with custom SVG icons
- Token names: `--ct-bkgd-01`, `--ct-bkgd-02`, `--ct-brand`, `--ct-text-h1-serif-family`, etc.

## Mobile behavior (≤768px)

- "Cloaked Reskin" title hidden; just the `Original | Reskinned` toggle at top
- Only one phone visible at a time (toggle swaps which side)
- Prev/next arrows + current screen name at the bottom
- **Swipe left/right** anywhere on the phone area to navigate screens
- **Tap the phone** to enter fullscreen (100vw × 100vh, no border-radius/shadow); tap again to exit. Fullscreen state persists across swipes/toggles.

## Key implementation notes

1. **Phone scaling — desktop vs mobile.**
   - Desktop: `transform: scale()` on `.phone` via the `--phone-scale` CSS variable, computed in JS from viewport size.
   - Mobile: uses `zoom` instead because iOS Safari has a known bug where `position: sticky` misbehaves inside `transform: scale()` parents.

2. **Sticky bottom nav.** The phone has an inner `.phone-scroll` wrapper that's the actual scrolling container (`overflow-y: auto`), separate from the transformed `.phone`. The nav (`.nav-bottom` and `.og-nav`) uses `position: sticky; bottom: 0` against that scroll container.

3. **Mobile body height.** JS sets `--app-h` from `window.innerHeight` on load/resize/orientationchange. CSS uses `height: var(--app-h, 100dvh)` with `100dvh` / `100vh` fallbacks. iOS Safari's `dvh` can be unreliable with `overflow: hidden` — JS is the bulletproof source of truth.

4. **Simula font.** Licensed, gitignored (`prototypes/fonts/` and `prototypes/deploy/fonts/`). Loaded via `@font-face` pointing at `fonts/Simula-Book.otf` and `fonts/Simula-Italic.otf`. Deployed to Vercel locally; not in any git repo.

5. **Both phones are 393×852.** iPhone 16/17 standard dimensions for both sides. (Originally the left was 375×812 for iPhone 13 mini parity, but unified for cleaner comparison.)

6. **iOS Safari button quirk.** `.mobile-tab` and other buttons include `-webkit-text-fill-color` and `-webkit-appearance: none` defensively — iOS Safari sometimes overrides button text color with system defaults.

## Known issues / open work at session end

- On iOS Safari, the mobile bottom bar (← screen name →) was intermittently not visible. Last attempted fix: `--app-h` JS variable. Hard refresh may be needed to clear cache.
- Some original templates (`Messages`, `Website`) are looser approximations from the PNG references rather than precise Figma extracts.
- The `homepage` original was the only one built directly from a Figma `get_design_context` extract; the other 19 originals were rebuilt from the PNG screenshots in the iOS 4.0 visual language.

## Components view & interactive playgrounds

`deploy/index.html` has a sidebar **Components** view alongside Pages and Tokens. Each component sub-page mounts an interactive playground that mirrors the Figma component panel (canvas on the left, property controls on the right). Existing playgrounds:

- **List row** — Figma `19941:868`
- **Button** — Figma `18759:7017` (Style × Size × leading/trailing icon)
- **Icon button** — Figma `16960:10476` (Style × Size + icon swap)
- **Divider** — Figma `18685:6445` (BKGD_01/02 + divider/secondary toggles)
- **Header** — synthesized from Figma `20238:20024` (6 landing instances) + `20238:20025` (detail layout); no single Figma component exposes these as variants, so the playground decomposes them into atomic props

**Adding a playground:** follow the existing `mountXxxPlayground()` pattern near the bottom of the script block. Each gets its own root div (`#xxxPlaygroundRoot`), visibility CSS gated by `body[data-components-sub="xxx"]`, and a sidebar tab in `#componentsNav`.

**Theme propagation:** the site's `applyTheme()` function flips `data-theme` on `.reskin, .pg-themed` elements (the html element keeps a static `data-theme="light"` as a fallback). Mark new playground canvases with `.pg-themed` so semantic `--ct-cta-*` / `--ct-bkgd-*` / `--ct-text-*` tokens flip on the toggle. On mount, also read `document.body.getAttribute('data-site-theme')` and set it as `data-theme` directly — `applyTheme` already ran by the time late-mounted DOM exists.

## Dark mode for hand-coded originals

The original-side mockups (`.original` markup, `.og-*` classes) use hardcoded hex values that don't flip with theme. Two-pronged fix lives in `deploy/index.html`:

1. **JS hex swap** (`applyOriginalColorTheme`) — walks every element with an inline `style` attribute under `.original` and swaps hex values via the Cloaked Color JSON's light↔dark lookup table.
2. **CSS class overrides** — `body[data-site-theme="dark"] .og-*` rules added for every `.og-*` class that declares colors at the CSS level (the JS only touches inline styles, so class-level colors need the explicit overrides).

If you add new `.og-*` styles, add the matching `body[data-site-theme="dark"] .og-*` rule alongside.

## Verify-component skill

`.claude/skills/verify-component/SKILL.md` (gitignored) — checks any new component playground against its Figma source on three axes: token binding (no hex/rgba/off-scale px where a `--ct-*` token exists), spacing/layout accuracy, and completeness (themes/states/variants). Run it after any Figma-to-HTML translation. The skill's **Discovered gotchas** section grows as new project-specific quirks surface.

## Git history note

This prototype was originally developed on the `christine` branch of the [cloaked-design-md repo](https://github.com/PS-Cloaked/cloaked-design-md). The 8 commits from that session were force-reverted (back to commit `c42d5f1`) so the cloaked-design-md repo stays clean. **The prototype now lives only in this folder.**

## Why the reskin (pitch summary for engineering)

- **Trust is the product.** Privacy/identity protection requires the UI to feel trustworthy. Serif typography and editorial layouts signal craft and seriousness in a way standard iOS chrome can't.
- **Brand differentiation.** Distinctive visual language vs. dozens of look-alike iOS privacy apps (Aura, DeleteMe, etc.).
- **Premium positioning.** Supports a higher subscription price point and reduces price sensitivity.
- **Marketing alignment.** Matches Cloaked's marketing site / brand materials — eliminates the current visual seam between marketing and product.
- **Compounding payoff.** Token-driven system means future features inherit elevated quality for free; themes (dark mode, accessibility variants, white-label) become trivial.
- **Tradeoffs to be transparent about**: 20-screen migration cost, Simula font licensing for production, iOS-specific custom components (glass nav, gradients) may need Android/web fallbacks.
