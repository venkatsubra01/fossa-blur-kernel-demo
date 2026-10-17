# Blur Kernel Playground: Gaussian vs. Disk blur

An interactive, single-file web demo of the **generalized point spread function** (blur kernel) from
[Zero-Shot Depth from Defocus](https://arxiv.org/abs/2603.26658) (ZEDD benchmark / FOSSA model, ECCV 2026),
made for audiences who have never seen a Gaussian blur.

```
F(u, v, p) = 1/c² · exp(−2 · ((u² + v²) / c²)^(p/2))      # p = 2 → Gaussian, p → ∞ → Disk
```

## Features
- Upload, drag-and-drop or paste any image (or use the built-in samples: shapes & text, night lights, points of light).
- Sliders for **kernel size** (3–31), shape **p** (1 → ∞, with Gaussian / Disk presets and "random like training", p ~ 2^U(1,5)), and blur radius **c**.
- Step-by-step animation: a window slides over the image and shows *neighbours × kernel shares = contributions → add up → new pixel*, while the blurred output fills in pixel by pixel, in 8×8 patches, or row by row. Click any pixel to inspect it.
- Kernel heatmap plus a profile plot comparing your p with the Gaussian and the disk (like Fig. A7 in the paper).
- Full-resolution side-by-side: Original / Gaussian / your p / Disk.

Weights are normalized to sum to 1, and neighbours outside the image are skipped and the rest re-normalized, matching FOSSA's renderer (`util/render.py`, `power_exp_psf`).

## Run
No build step and no dependencies. Open `index.html` in a browser, or serve the folder:

```bash
python3 -m http.server 8000   # then open http://localhost:8000
```

Keyboard: `Space` play/pause, `→` step, `R` reset.
