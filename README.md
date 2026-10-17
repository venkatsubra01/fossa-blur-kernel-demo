# Blur Kernel Playground: Gaussian vs. Disk blur

An interactive, single-file web demo of the **generalized point spread function** (blur kernel) from
[Zero-Shot Depth from Defocus](https://arxiv.org/abs/2603.26658) (ZEDD benchmark / FOSSA model, ECCV 2026),
made for audiences who have never seen a Gaussian blur.

```
F(u, v, p) = 1/c² · exp(−2 · ((u² + v²) / c²)^(p/2))      # p = 2 → Gaussian, p → ∞ → Disk
```

## Features
- Upload, drag-and-drop or paste any image (the default is an embedded profile photo; other built-in samples: two ZEDD scenes, shapes & text, night lights, points of light).
- Sliders for **kernel size** (3–31), shape **p** (1 → ∞, with Gaussian / Disk presets and "random like training", p ~ 2^U(1,5)), and blur radius **c**.
- Step-by-step animation: a window slides over the image and shows *neighbours × kernel shares = contributions → add up → new pixel*, while the blurred output fills in pixel by pixel, in 8×8 patches, or row by row. Click any pixel to inspect it.
- Kernel heatmap plus a profile plot comparing your p with the Gaussian and the disk (like Fig. A7 in the paper).
- Full-resolution side-by-side: Original / Gaussian / your p / Disk.
- **Depth of field mode**: every pixel gets its own blur radius from its depth via the paper's circle-of-confusion formula
  `c = |D − d| / D · f² / (N (d − f))` (halved and divided by the pixel size, 36 mm sensor on the long edge, as in FOSSA's `ThinLenCamera`).
  Controls for focus distance d (or click a picture to focus there), f-number N and focal length f; depth map, per-pixel blur map,
  a c-vs-depth plot with the worked formula, and an animation that shows each neighbour's own blur radius and kernel.
  The per-pixel renderer follows FOSSA's `power_exp_psf` CUDA kernel: each neighbour contributes with its own c, and pixels with c ≤ 1 px are copied unchanged.

### Depth sources
| Sample | Depth |
| --- | --- |
| ZEDD: armchair (`val_0025`), ZEDD: tool wall (`val_0031`) | Lidar ground truth from the [ZEDD benchmark](https://huggingface.co/datasets/venkatsubra/ZEDD) (CC BY 4.0), small holes filled from neighbours. Includes the real f/1.4 photos at 0.82 / 1.67 / 3.84 / 8.10 m for comparison. |
| Profile photo | Estimated with Depth Anything V2 (metric indoor); the photo has no ground truth. |
| Shapes & text, Night lights, Points of light | Exact (drawn by the demo). |
| Your own image | Made-up slope until you add a depth map: `.npy` in metres (float) or millimetres (uint16), like ZEDD's `gt/depth.npy`, or a grayscale image with a near/far range. |

Depth maps are embedded as 8-bit inverse-depth PNGs (max error about 3 cm). Append `#dof` to the URL to open in depth-of-field mode.

Weights are normalized to sum to 1, and neighbours outside the image are skipped and the rest re-normalized, matching FOSSA's renderer (`util/render.py`, `power_exp_psf`).

## Run
No build step and no dependencies. Open `index.html` in a browser, or serve the folder:

```bash
python3 -m http.server 8000   # then open http://localhost:8000
```

Keyboard: `Space` play/pause, `→` step, `R` reset.
