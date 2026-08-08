# AR Card Set — Build Guide (100% free, no software/hardware purchases)

This is a browser-based AR project. Nothing to install on your phone — people just
open a link in Chrome and point the camera at your printed card.

## How it works
1. Each card has a printed image (the "target").
2. Your phone's camera recognizes that image.
3. A 3D model appears floating above the card.

You are NOT printing 3D-looking cards — you're printing normal flat images,
and the AR magic happens live in the browser.

---

## Step 0 — What's in this folder
```
ar-card-set/
├── index.html          → the AR viewer (this is what you open on your phone)
├── style.css            → viewer styling
├── card-designer.html   → tool to lay out card art on a printable A4 sheet
├── assets/
│   ├── models/           → put your .glb 3D models here
│   ├── targets/           → put your compiled targets.mind file here
│   └── card-art/           → put your source card images here (before printing)
└── README.md
```

## Step 1 — Design your card art
- Use GIMP (free) or Canva (free tier) to design the flat image for each card.
- **Important for AR tracking:** busy, high-contrast, asymmetric images track far
  better than plain logos or symmetric patterns. Add texture, gradients, or a
  detailed illustration — avoid large flat colored areas.
- Export each card as a PNG/JPG at reasonably high resolution (at least 1000px
  on the longer side) into `assets/card-art/`.

## Step 2 — Get 3D models (free)
- **Poly Pizza** (poly.pizza) — free low-poly models, ideal for phone AR (light = fast).
- **Sketchfab** (sketchfab.com) — filter search by "Downloadable" and check the
  license (CC0 or CC-BY are safest for reuse). Ben 10 fan models show up here
  under community licenses — verify terms before using publicly.
- **Free3D** (free3d.com) — mixed quality, check licenses per model.

## Step 3 — Edit models in Blender (free)
Download Blender (free, open-source) from blender.org. Typical fixes needed:
- **Decimate modifier** to reduce poly count (keep phone AR models under ~50k
  triangles for smooth tracking).
- Apply rotation/scale so the model sits upright and centered at the origin.
- **File → Export → glTF 2.0 (.glb)** — this is the web-friendly format MindAR/
  Three.js needs. Save into `assets/models/` as `card-0.glb`, `card-1.glb`, etc.,
  matching the order of your card images.

## Step 4 — Compile your AR targets (free, browser-based, no install)
1. Go to MindAR's free Image Target Compiler:
   **hiukim.github.io/mind-ar-js-doc/tools/compile/**
2. Upload your card images from `assets/card-art/` — **in the same order**
   you'll reference them in `index.html` (card 0, card 1, ...).
3. Click "Start", wait for it to process, then download the resulting
   `targets.mind` file.
4. Place it in `assets/targets/targets.mind`.

## Step 5 — Wire it up
Open `index.html` — it already has slots for two cards (`targetIndex: 0` and
`targetIndex: 1`), each pointing at a model. To add more cards:
- Add another `<a-asset-item id="model-N" src="assets/models/card-N.glb">`
- Add another `<a-entity mindar-image-target="targetIndex: N">` block with its model
- Re-run Step 4 with N+1 images total, in matching order

Adjust each model's `position`, `rotation`, and `scale` attributes to sit nicely
on the card — you'll tweak these by testing and reloading.

## Step 6 — Test it locally on your phone
Camera access needs either `localhost` or `https`. Easiest local option:
```bash
# from inside ar-card-set/
python3 -m http.server 8000
```
Then, on your PC, find your local IP (e.g. `192.168.1.5`) and note that plain
HTTP over your home Wi-Fi will NOT get camera permission on most phones
(browsers require HTTPS for camera, except literally `localhost`). So for real
phone testing, use free hosting instead (Step 7) — it's faster than fighting
with local HTTPS certificates.

## Step 7 — Host it for free
**GitHub Pages** (free, no card required):
1. Create a free GitHub account and a new repository.
2. Upload this whole `ar-card-set` folder to it.
3. In the repo Settings → Pages, enable GitHub Pages on the `main` branch.
4. You'll get a free URL like `https://yourname.github.io/ar-card-set/`.
5. Open that link in Chrome on your Android phone, allow camera access, and
   point it at a printed card.

## Step 8 — Print your cards
1. Open `card-designer.html` in your browser (double-click the file, or host it
   alongside the rest).
2. Choose a card size (poker size 63×88mm is the standard TCG size).
3. Upload your card images from `assets/card-art/`, **in the exact order**
   you compiled them into `targets.mind`.
4. Click "Generate print sheet" then "Download PNG (300 DPI)" — this gives you
   a print-ready A4 sheet with crop marks.
5. Print at home, or take the PNG to any local print shop (many offer cheap
   photo-paper prints per page — this is the only real-world cost, ink/paper,
   not software).
6. Cut along the crop marks. Optional: laminate for durability — a home laminator
   isn't required, glossy photo paper alone tracks fine.

---

## Quick troubleshooting
- **Model doesn't appear:** check the browser console (remote debug via
  `chrome://inspect` from your PC with the phone plugged in) — usually a wrong
  file path or targetIndex mismatch.
- **Tracking is jittery/fails:** your card image is too plain — add more visual
  detail/contrast, or increase the printed size.
- **Model floats wrong / wrong scale:** tweak `position` and `scale` in
  `index.html` for that specific `a-entity`, then reload.
