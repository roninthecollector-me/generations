# CLAUDE.md — project brief for Generations

Read this first. Generations is a standalone **panel map viewer** spun off from *The Sortis Vellum* worldbuilder — just the map half, generalized for anyone building a Jerry's-Map-style panel map (physical or digital).

## Working philosophy

- **One self-contained file.** The whole app is `index.html` — vanilla HTML/CSS/JS, **no build step, no framework, no bundler, no runtime dependencies**. Keep it that way.
- **Local-first, degrade gracefully.** Works fully offline with no account. Optional cloud (Supabase) is reserved in `config.js` but not yet wired — guard any cloud code so a blank config keeps the app 100% local.
- **Playable before pretty.** Iterate from real use.

## Architecture of `index.html`

Single state object `M`, persisted to `localStorage` (key `generations:state:v1`); panel **images** live separately in **IndexedDB** (`generations-img` store), not in `M`.

```
M = {
  meta:    { name, created },
  panels:  [ { coord, label, founded, gen, order, images } ],   // images:{ "<gen>": {w,h,bytes,uploadedAt} }
  currentPanel: "N0 E0",
  settings: {}
}
```

- **Coordinates** are canonical `N{a} E{b}`, zero-normalized (origin `N0 E0`). `parseCoord` / `normCoord` / `coordNoSpace`. Map layout: `x = east(+)`, `y = -north` (north up). `xyOf(coord)` and the inverse `coordFromXY(x,y)` power click-to-add.
- **Images**: keyed in IndexedDB by `imgKey(coord,gen)` = `"<coordNoSpace>/gen<N>"` (full) and `+".thumb"`. `imgURL(key)` returns a cached object URL; `dropURL` revokes it. Upload re-encodes to WebP via `processImage` (full ≤2400px q0.9, thumb ≤600px q0.8).
- **Map** (`renderMap`): grid by coordinate, generation slider rendering "latest image ≤ G", placeholders otherwise, **empty addable cells** (`.map-add`) one ring beyond the panel bounds (click to create a panel), pan/zoom via CSS transform, lazy thumbnails (IntersectionObserver from IndexedDB), full-res lightbox with prev/next-generation nav.
- **Templates**: `drawPanelCanvas` renders an 8×10 @300DPI gridded panel blank (compass + coordinate/gen/label stamp); `downloadTemplate` saves a PNG.
- **Backup/import**: `exportBackup` writes one `.json` with panels + base64 images; `importFromObject` restores a Generations backup (`__generationsBackup`) or imports a **Sortis Vellum** world (`obj.panels` with `terrain`→`label`; art does not transfer — it lives in SV's Supabase).

## Conventions for edits

- New UI stays within the CSS token system (`:root` vars) and the warm "almanac" aesthetic (parchment, red/black ink, verdigris, gold).
- After any change, sanity-check the script:
  `sed -n '/<script>/,/<\/script>/p' index.html | sed '1d;$d' > /tmp/x.js && node --check /tmp/x.js`
- Keep coordinate handling identical across map, panel list, and click-to-add.

## Deploy

Static site. `git push` to the GitHub repo connected to Vercel → auto-deploy. No build command.
