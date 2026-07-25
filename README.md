# 🖨️ 3D Print Tools

Two browser-based tools for 3D printing. 100% client-side — nothing is uploaded to a server; every file you open stays in your browser.

**▶ Live site: https://borenw.github.io/3d-print-tools/**

| Tool | Live link | What it does |
|------|-----------|--------------|
| 🩹 **Easy Support** | [support.html](https://borenw.github.io/3d-print-tools/support.html) | Generates supports whose tips contact the model with **4× less area** so they snap off clean. In/out `.3mf`, also exports `.stl`. |
| 🧊 **3D Browser** | [viewer.html](https://borenw.github.io/3d-print-tools/viewer.html) | Drag-and-drop viewer for **STL / 3MF / OBJ / PLY / glTF** with intuitive slicer-style mouse controls. |

---

## 1. Easy Support — 4× smaller contact, clean removal

Supports are annoying because *removing* them scars the print. Bond strength scales with contact **area**, so the tool shrinks each support tip's footprint while keeping the base wide and stable.

**Algorithm (Generate mode):**
1. **Find overhangs** — every down-facing triangle steeper than the overhang threshold (45° by default) is flagged; faces already resting on the plate are ignored.
2. **Place pillars** — flagged points are snapped to a spacing grid and ray-cast straight down to land on the model below (or the build plate).
3. **Slim the tips** — each pillar is built as a truncated cone: a wide base for stability tapering up to a slim contact tip. Halving the tip diameter quarters the contact area → the default **4×** reduction. A short straight neck under the tip gives a clean break line.
4. **Export** — the model and the generated supports are written back to a valid `.3mf` (two objects) or a merged `.stl`.

The achieved reduction is **measured live** (π·r_tip² vs. a standard flat tip), not assumed — tune the overhang angle, spacing, reduction factor, reference tip Ø and base Ø with sliders and watch the readout.

**Modify mode** — for `.3mf` files that already contain supports as *separate objects*: tick which objects are supports and the tool pinches their top contact band inward, reducing that footprint by the chosen factor.

**Try it instantly:** load the bundled `2x2_ver1.3mf`, or pick **▲ Test overhang (built-in)** — a slab on two legs whose underside always needs support.

## 2. 3D Browser

- **Formats:** `.stl`, `.3mf`, `.obj`, `.ply`, `.gltf`/`.glb` — detected by extension.
- **Mouse (slicer/CAD-intuitive):** left-drag = orbit · **middle-drag = pan** · right-drag = pan · wheel = zoom. Touch: one-finger orbit, two-finger pan/zoom.
- Z-up print-bed orientation, auto-fit, live bounding-box **dimensions in mm**.
- Wireframe, model/background color, grid & axes toggles, drag-and-drop anywhere.

---

## Tech

Single self-contained HTML files. [Three.js](https://threejs.org) (r160, via jsDelivr) for rendering and format loaders; [fflate](https://github.com/101arrowz/fflate) to read/write the `.3mf` ZIP container. No build step, no backend.

```
index.html      landing page
viewer.html     3D Browser
support.html    Easy Support tool
samples/        bundled 2x2_ver1.3mf
```

## Run locally

Because the tools `fetch()` the bundled sample, serve over HTTP rather than opening `file://`:

```bash
cd 3d-print-tools
python3 -m http.server 8000
# open http://localhost:8000/
```

(You can still drag-and-drop your own files with no server.)

## License

MIT
