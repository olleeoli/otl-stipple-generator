# OTL Stipple Generator

**Turn any photo into stipple art and CNC-ready drill files, right in the browser.**

Built by OTL Woodwork LLC (Atlanta, GA) for the stipple portraits we cut on a Shapeoko Pro 5: MDF painted matte black, then a V-bit plunges thousands of dots to reveal the wood underneath. This is the tool that turns a photo into those dots.

**▶ Live app: [otl-stipple-generator.vercel.app](https://otl-stipple-generator.vercel.app)**

<!-- Add a screenshot here once you have one:
![OTL Stipple Generator screenshot](docs/screenshot.png)
-->

---

## What it does

1. **Drop in a photo.** Drag and drop, use the file picker, or paste straight from the clipboard (Ctrl/⌘ + V). PNG, JPG, and WebP all work.
2. **Tune it live.** Every slider redraws the preview immediately, so you can dial in the look before committing to a cut.
3. **Export for the machine.** Download SVGs built for CNC toolpaths, plus a full-color version for display and mockups.

No accounts, no uploads, no server. The image never leaves your computer.

## Features

### Dot controls
| Setting | Range | What it does |
|---|---|---|
| Grid Spacing | 3–20 px | Distance between dot centers. Smaller = more dots, more detail, longer cut. |
| Min / Max Dot Size | 0.1–3 px / 1–8 px | Radius range dots are mapped into based on brightness. |
| Jitter | 0–1 | Randomly offsets each dot off the grid so it reads as hand-stippled rather than mechanical. |

### Image controls
| Setting | What it does |
|---|---|
| Contrast | Gamma curve applied to brightness. Push it up to separate the lights and darks. |
| Brightness | Shifts the whole tonal range up or down. |
| Threshold | Drops dots below a set intensity, so empty areas stay clean (fewer wasted plunges). |
| Invert | On by default: bright areas get bigger dots, which is what you want when the dots reveal light wood through black paint. |

### Canvas and colors
- Width and height (200–1200 px); height follows the photo's aspect ratio on upload.
- Pick the dot and background colors to preview the finished piece. Gold on dark is the default and roughly matches natural wood on black paint.

## Exports

| File | Best for | Details |
|---|---|---|
| **Drill SVG** ⚡ *(recommended)* | Fast production cuts | Dots are grouped into SVG layers by size (`drill_r1.5`, `drill_r2.0`, …). Each layer becomes its own drill toolpath at its own depth. Roughly 80% faster than carving every circle. |
| **VCarve SVG** | Maximum detail | Every dot as a true-size `<circle>` for a V-carve toolpath. Sharpest result, longest cut. |
| **Full SVG** | Display, mockups, and social posts | Full-color render with background, exactly as the preview shows it. |

The export panel also shows dot count, canvas size, and grid settings, so you can estimate cut time before you commit.

## Shop workflow

This is how we use it for a stipple portrait:

1. **Prep the stock.** 3/4" MDF, top face painted matte black.
2. **Generate.** High-contrast portraits, animals, and bold shapes read best. Crank the contrast and set a threshold to keep backgrounds clean.
3. **Export the Drill SVG** and import it into your CAM software.
4. **One drill toolpath per layer.** Small-radius layers get shallow plunges and large-radius layers go deeper. With a 60° V-bit, depth controls dot diameter directly.
5. **Cut.** Plunge rates around 10–15 ipm work well in MDF.
6. **Clean up** with a light pass of fine sandpaper or a brush to knock down fuzz.

## How it works

The generator is a variable-size stipple on a jittered grid:

```
for each grid cell:
    offset the center randomly (jitter)
    sample the photo's luminance at that point   (0.299R + 0.587G + 0.114B)
    apply contrast (gamma) and brightness
    if below threshold → skip
    radius = minDot + value × (maxDot − minDot)
```

The result is a list of `{x, y, r}` dots rendered as SVG `<circle>` elements, so the output stays vector from preview to export.

## Tech

Built to be as simple as possible to run and maintain:

- **One file.** All of the HTML, CSS, and vanilla JavaScript lives in `index.html`.
- **No build step.** No framework, bundler, or dependencies.
- **Fully client-side.** Canvas for image sampling, inline SVG for output, no backend.
- **Static deploy on Vercel.**

### Run it locally

```bash
git clone https://github.com/olleeoli/otl-stipple-generator.git
cd otl-stipple-generator
open index.html        # macOS, or double-click the file
```

That's it. There's nothing to install.

## Roadmap

- Direct G-code export (GRBL / Carbide Motion) with optimized cut order
- Physical units (inches/mm) and stock-size presets
- Saved settings between sessions

## Related

- **OTL Halftone Generator** is the sister tool, using a rotatable regular grid for classic halftone portraits with G-code output.

---

Made in Atlanta by **OTL Woodwork LLC**.
