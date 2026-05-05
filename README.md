# Solid of Revolution

An interactive [solid of revolution](https://en.wikipedia.org/wiki/Solid_of_revolution) generator built with [Processing](https://processing.org/). Draw a profile curve with mouse clicks and the sketch revolves it around the central axis to produce a 3D wireframe solid, rendered using [triangle strips](https://en.wikipedia.org/wiki/Triangle_strip).

![](animation.gif)

---

## Controls

| Action | Input |
|---|---|
| Add a vertex to the profile | Left click |
| Finish profile and generate solid | Right click |
| Reset canvas (when a solid exists) | Right click |

---

## How It Works

### 1. Profile Input

Left clicks add vertices to a 2D profile polyline. The first vertex is always snapped to the central axis (the vertical centre line of the canvas) so the solid is closed at the top. When the profile is finished with a right click, a final vertex is added at the same height as the last point, also on the axis, closing the bottom.

### 2. Revolution — Generating Meridians

The profile is revolved 360° around the central axis in steps of **4°**, producing `360 / 4 = 90` meridian slices. Each slice is computed by applying a [2D rotation matrix](https://en.wikipedia.org/wiki/Rotation_matrix) to the previous one:

```
x' = x·cos(θ) − z·sin(θ)
z' = x·sin(θ) + z·cos(θ)
y' = y  (unchanged)
```

This fills a 2D array `m[meridian][profilePoint]` of 3D points.

### 3. Mesh Construction

The surface is assembled as a [`TRIANGLE_STRIP`](https://processing.org/reference/createShape_.html) in Processing's P3D renderer. For each pair of adjacent meridians, alternating vertices from the two slices are emitted in order, connecting them into triangles that form the surface. The mesh is drawn without fill so the full wireframe — including any interior geometry — is visible.

### 4. Drawing on Both Sides

The profile can be drawn to the left or right of the axis, which allows creating concentric or nested solids by defining profiles on opposite sides.

---

## Implementation Notes

- **Coordinate system:** The canvas centre (`width/2`) acts as the revolution axis. Profile x-coordinates are offset by `width/2` before rotation so that the axis maps to the origin in 3D space.
- **Angle step:** 4° per meridian gives 90 slices, which balances visual smoothness with performance. Reducing it increases detail; increasing it produces a more faceted solid.
- **Rendering:** The sketch uses Processing's `P3D` renderer and `translate(width/2, 0, -100)` to centre the solid on screen after generation.

---

## Project Structure

```
solid_of_revolution/
└── CIU_solido_revolucion.pde   # Single-file Processing sketch
```

---

## Requirements

- [Processing 3+](https://processing.org/download) with P3D renderer support (included by default)
- No additional libraries needed

---

## License

MIT

---

## Credits

Developed by Leopoldo López Reverón — School of Computer Engineering, Universidad de Las Palmas de Gran Canaria.