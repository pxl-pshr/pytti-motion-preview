# PYTTI // MOTION PREVIEW

> Real-time 3D path visualizer for PyTTI animation transforms

**[→ Live Demo](https://pxl-pshr.github.io/pytti-motion-preview)**

A browser-based tool for previewing the camera motion that PyTTI will produce from your `scene.yaml` transform expressions — before running a single diffusion step. Paste in your `translate_x`, `translate_y`, `translate_z_3d`, and `rotate_3d` expressions and see the accumulated camera path rendered live in 3D.

---

## What it does

PyTTI drives its virtual camera using per-frame delta transforms expressed as math functions of `t`. These accumulate over the animation — a small rotation each frame compounds into a full arc, translations compose with rotation to produce spirals, orbits, or drifts. This tool simulates that exact accumulation and renders it interactively.

**Two views:**

- **PATH** — Observer camera auto-orbits the full trajectory. The camera frustum shows exact pose at the current frame. Color gradient (cyan → orange) maps start → end of path.
- **CAM** — First-person view through the PyTTI camera itself, moving through a reference scene with depth cues (grid, pillars, fog) so you can feel the motion as it will appear in the output.

---

## Parameters

Expressions are evaluated per-frame with `t` as the frame index. All standard math functions are available: `sin`, `cos`, `tan`, `sqrt`, `pow`, `abs`, `radians`, `degrees`, `atan2`, `log`, `exp`, `PI`, etc.

```yaml
translate_x:    '20 * cos(0.2 * t)'       # horizontal delta each frame
translate_y:    '10 * sin(0.2 * t)'       # vertical delta each frame
translate_z_3d: '0'                        # depth delta each frame
rotate_3d:      '[cos(radians(1)), 0, sin(radians(1)), 0]'  # quaternion [w, x, y, z]
```

The quaternion convention matches PyTTI: `[w, x, y, z]`. Each frame's rotation is multiplied onto the accumulated quaternion, then the translation is applied in that rotated local space — exactly as PyTTI processes it.

---

## Expression examples

| Effect | translate_x | translate_y | rotate_3d |
|---|---|---|---|
| Circular orbit | `20 * cos(0.2 * t)` | `10 * sin(0.2 * t)` | `[1, 0, 0, 0]` |
| Slow Y-axis spin | `0` | `0` | `[cos(radians(1)), 0, sin(radians(1)), 0]` |
| Dolly + tilt | `0` | `0` | `[cos(radians(0.5)), sin(radians(0.5)), 0, 0]` |
| Spiral descent | `5 * cos(0.1 * t)` | `-0.5` | `[cos(radians(0.5)), 0, sin(radians(0.5)), 0]` |
| Figure-8 | `15 * sin(0.15 * t)` | `8 * sin(0.3 * t)` | `[1, 0, 0, 0]` |
| Oscillating roll | `0` | `0` | `[cos(radians(sin(t*0.05)*2)), 0, 0, sin(radians(sin(t*0.05)*2))]` |

---

## Controls

| Control | Function |
|---|---|
| Expression inputs | Live recompute on edit (400ms debounce) |
| Total Frames | Set animation length |
| ↻ RECOMPUTE | Force recompute |
| PATH / CAM toggle | Switch view mode |
| ▶ PLAY / ■ STOP | Play back the animation |
| ⟳ LOOP | Toggle looping |
| Timeline scrubber | Seek to any frame |
| FPS field | Set playback speed |

The panel shows per-frame readout: delta translations, raw quaternion components, accumulated position XYZ, Euler angles (YXZ order), and path analysis (total distance, max radius, loop detection).

---

## Stack

Single-file, zero build step. Pure HTML/CSS/JS + [Three.js r128](https://threejs.org/) via CDN.

```
index.html
```

No dependencies to install. No bundler. Open it in a browser or serve it statically anywhere.

---

## Local use

```bash
# Clone
git clone https://github.com/pxl-pshr/pytti-motion-preview
cd pytti-motion-preview

# Open directly — no server needed for basic use
open index.html

# Or serve locally
python3 -m http.server 8000
```

---

## Related

- [PyTTI](https://github.com/pytti-tools/pytti-core) — the diffusion animation framework this previews
- [PyTTI-Tools](https://github.com/pytti-tools) — extended tooling ecosystem
- [quaternions.online](https://quaternions.online) — quaternion reference and visualizer

---

*by [pxl-pshr](https://github.com/pxl-pshr)*
