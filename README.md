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

## 2. 3D Browser + lightweight editor

- **Formats:** `.stl`, `.3mf`, `.obj`, `.ply`, `.gltf`/`.glb` — detected by extension.
- **Mouse (slicer/CAD-intuitive):** left-drag = orbit · **middle-drag = pan** · right-drag = pan · wheel = zoom. Touch: one-finger orbit, two-finger pan/zoom.
- Z-up print-bed orientation, auto-fit, live bounding-box **dimensions in mm**.
- Wireframe, model/background color, grid & axes toggles, drag-and-drop anywhere.

### Editor tools (left toolbox)

- **Move face — `M`.** Click a flat surface to select its coplanar face; the camera **rotates to a head-on (planar) view** and a 3-axis gizmo aligned to the surface appears. Drag an axis to **push/pull**, in one of two modes (the ⬆ button toggles):
  - **EXTRUDE** (default) — the face moves and **new side walls are generated** from its old outline to the new position; the rest of the mesh is left exactly as it was (a true extrude/emboss).
  - **STRETCH** — the face's vertices (and every triangle welded to them) move, so the **attached faces deform** to follow.

  Bounding-box dimensions update live. **Refine the selection** head-on with **Ctrl+drag** (add a rectangle) / **Shift+drag** (remove a rectangle); rectangle corners **snap to strong edges** (sharp/boundary), and if you keep moving on and off a snap it disengages for that drag.
- **Select object — `O`.** Click a part. If it's a standalone connected component it's selected whole; if it's only *weakly* attached (a thin neck), the tool sweeps for the **smallest cross-section plane** and virtually disconnects there, so a leg/blob pops off as its own object. Drag it away to detach, `Del`/🗑 to remove, or copy/cut/paste/duplicate it.
- **Draw — `R`.** Choose **CUT** (default) or **ADD** with the ✂️ button (or right-click), then drag a rectangle on a surface — it's aligned to that surface's own axes and **applies on release**: cut subtracts, add unions. Corners **snap to nearby model vertices**; if you keep moving on and off a snap point it disables snapping for that rectangle (so it never fights you). `Esc` cancels. Booleans use [three-bvh-csg](https://github.com/gkjohnson/three-bvh-csg).
- **Ruler — `K`.** Click a start point on a surface, then an end point. The measuring X/Y/Z axes are built from that surface's normal + tangents; while you move, the end **snaps to whichever axis is nearest the cursor**, and deviating far from all three switches to a **free** 3-D measurement. Each ruler shows its length in mm, and can be clicked to select and deleted.
- **Undo / redo** — `⌘/Ctrl+Z`, `⇧Z` (or `Ctrl+Y`). **Clipboard** — `⌘/Ctrl+C` copy, `X` cut, `V` paste, `D` duplicate (operate on the selected object).
- **Esc** returns to orbit/select mode.

### Other conveniences

- **Sliced / G-code-only `.3mf`**: some slicer projects (e.g. Bambu/Flash Studio) save only a toolpath and no mesh. Instead of failing, the viewer parses the embedded **G-code and renders the toolpath** (colored by layer height). Raw `.gcode` files open the same way.
- **Remembers your last file**: the most recent model you open is cached in the browser (localStorage) and reopened automatically next time. `viewer.html?model=<url>` opens a specific model by link.

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
