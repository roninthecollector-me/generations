# Generations

A panel map viewer for **Jerry's-Map-style** worlds — assemble finished panels by coordinate and scrub through time with a **generation slider** to see the map as it looked at any point. For physical or digital map-makers.

> Build a world one panel at a time. Then watch it grow.

## What it is

A single self-contained web app (no build step, no framework, no account). Everything lives in `index.html` — vanilla HTML/CSS/JS.

- **Map** — every panel laid out by coordinate (`N3 E2`, north up). A generation slider shows the whole map "as of" any generation (each cell shows its newest image at a generation ≤ the slider). Pan, zoom, and click a panel for the full-resolution image.
- **Click to build** — click any empty cell on the grid to drop a new panel there, or add by coordinate from the Panels tab.
- **Generations** — bump a panel's generation and upload new art for it over time. The slider walks the whole history.
- **Print-ready templates** — download an 8×10 @300 DPI gridded panel blank (with a compass and a coordinate stamp) to draw on by hand.
- **Local-first** — panels and images are stored in your browser (localStorage + IndexedDB). No account, works offline. A one-file JSON **backup** moves a map between devices.
- **Import** — bring the panel layout over from a **Sortis Vellum** world export.

## Run it

Just open `index.html` in a browser. No install, no server. Deployed (static) it works the same, plus you can add it to your home screen.

## Storage

- **Map data** (panels, coordinates, generations) → `localStorage`.
- **Panel images** → **IndexedDB** as WebP blobs, re-encoded on upload (full ≤ 2400px, thumbnail ≤ 600px) to stay light.
- **Backup** (About & data → *Download backup*) writes one `.json` with panels + embedded art for safekeeping or moving devices.

## Roadmap

- Optional cloud sync (Supabase) for cross-device, off by default — `config.js` is reserved for it.
- Export the assembled map as one composited image.
- A read-only share link per generation.

## Credit

Inspired by **Jerry Gretzinger's Map**. Built as a standalone spin-off of *The Sortis Vellum* worldbuilder.
