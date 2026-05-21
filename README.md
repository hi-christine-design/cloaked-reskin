# Cloaked Reskin

A side-by-side comparison tool for the Cloaked app — original Cloaked Design System 4.0 vs the reskin built on the Cloaked Toolkit (v2) design system. Live at [deploy-jade-five-69.vercel.app](https://deploy-jade-five-69.vercel.app).

## Structure

```
deploy/                    Vercel deployment root
  index.html               Single-file app (HTML + CSS + JS)
  tokens/                  Reskin design tokens (CSS)
    colors.css
    numbers.css            (spacing + opacity)
    themes.css             (semantic tokens, Light + Dark)
    typography.css
  fonts/                   Aeonik Mono, Simula
  assets/                  Reskin original screenshots + video logo
assets/                    Source asset library (not deployed)
fonts/                     Source font files
reskin-comparison.html     Earlier standalone comparison artifact
```

## The app has three views

- **Pages** — side-by-side phone mockups (Original PNG vs live HTML reskin) for 24 screens
- **Components** — per-component audit blocks (button, card, list row, tab strip, header)
- **Tokens** — per-category comparison (Colors, Themes, Typography, Spacing) between the original 4.0 system and the reskin v2 system

Light + Dark themes both supported across the chrome and the phone mockups (the reskin uses `[data-theme]` from the design system; the chrome flips via `body[data-site-theme]`).

## Deploy

```bash
cd deploy
npx vercel deploy --prod
```

The Vercel project is aliased to `deploy-jade-five-69.vercel.app`.
