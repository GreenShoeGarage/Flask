# FLASK

**Field Instrument FI-110 . STL to mold-negative generator**

FLASK turns a positive (a master object as an STL) into a negative: a printable or
machinable mold, exported as STL, for gravity-poured polyurethane resin casting and
for hobby / low-volume plastic injection molding. It offers a one-piece open-face
mold and a two-piece split mold, and it bakes mold-making best practice into both
automatic geometry and guided, explained controls.

It is a single HTML file. Double-click it. There is no server, no build step, and
no network traffic. Nothing you load leaves your machine.

```
Make. Hack. Learn. Share. Repeat.
```

## What it does

- **Loads** ASCII and binary STL by file picker or drag-and-drop, checks the mesh for
  watertightness and manifoldness, unifies inconsistent face normals, fills small
  holes where it can, and reports bounding box and volume. Assumes millimetres; a unit
  rescale is provided.
- **Orients** the part in a 3D viewport and lets you set the pull direction (the axis
  the part releases along). A translucent parting plane and a pull arrow are drawn on
  the part.
- **Analyses draft and undercuts** relative to the pull direction and parting plane,
  colouring the mesh: draftable faces, low-draft walls, and undercuts that would lock
  the part in a rigid mold. It reports the minimum draft and the undercut area, and
  warns instead of silently producing an unreleasable mold.
- **Builds** the mold through the manifold-3d geometry kernel: shrink-scaled cavity,
  parting, sprue (or gate + runner in injection mode), vents with overflow reservoirs,
  asymmetric registration keys (boss on one half, clearance socket on the other), and
  the mold walls. Every exported piece is verified watertight and manifold.
- **Exports** an STL per piece (binary by default, ASCII optional), named by role
  (cope / drag for casting, cavity / core for injection, open-face for one-piece), plus
  a session JSON capturing pull direction, parting and every parameter for re-import.
  A material estimate reports approximate resin per pour.

## Aesthetic

A pattern-shop and foundry instrument: cast iron, moulder's sand, brass fittings and
ledger typography, with the 3D viewport as the centrepiece and a maker's label plate.
Night-default theme, plus Pattern Shop (light) and High Contrast, all WCAG AA.

## The single-file / runs-from-disk tradeoff

The geometry kernel is **manifold-3d** (WASM), chosen for guaranteed-manifold output,
which for a mold tool is the whole point: every exported piece must be watertight and
printable or machinable. To honour the hard rule that a Field Instrument runs offline
straight from disk, the WASM kernel is **inlined as a base64 block and instantiated
from an ArrayBuffer**, so no `.wasm` sibling is ever fetched. The three.js viewport is
inlined the same way. This makes the file heavier (about 1.5 MB, the kernel dominating
at ~0.7 MB) in exchange for the most reliable cavity geometry. Rejected for the record:
three-bvh-csg (lighter pure JS, but can emit non-manifold results needing a cleanup
pass) and a voxel / SDF path (forgiving of messy input but memory-heavy and softens
fine detail).

## Running the tests

Open `test-harness.html` from disk. It loads `flask.html` in a hidden frame, waits for
the kernel, and runs the in-app self-test plus DOM and geometry smoke checks. It must
show **ALL GREEN** before any release. The same core assertions are available on demand
from the **Self-Test** button inside the app.

## Known Limitations (v1.0.0)

- **Not a production injection-tool designer.** Injection mode adds only a basic gate
  and a runner stub. It does not design ejector systems, cooling channels, hot runners,
  slides, lifters, or a mold base. Use it for 3D-printed, resin, or aluminium hobby and
  low-volume tooling, not production steel tools.
- **Cores and internal hollows are out of scope.** FLASK does not generate cores for
  internal cavities or through-holes, and does not make molds beyond two pieces. These
  are on the roadmap.
- **Draft is measured and flagged, not added.** v1.0 performs the required undercut and
  draft analysis and flags low-draft walls, but does not automatically insert draft on
  arbitrary meshes. Robust automatic draft addition is a v1.1 target. Add draft in your
  CAD model where you can.
- **Undercut detection is first-order.** Undercuts are judged per face against the
  chosen pull direction and parting plane. This catches back-draft faces that lock
  against their own half, but it will not catch every occluded overhang formed by
  disconnected geometry. Treat the analysis as guidance, not proof.
- **Parting is a plane at an adjustable height along the pull.** Independent parting
  tilt and non-planar (stepped) parting surfaces are a v1.1 target.
- **Mesh-boolean robustness depends on a clean input STL.** FLASK unifies normals and
  fills small holes, but a badly non-manifold or heavily self-intersecting input will
  be refused at the boolean step with a reason. Repair the source mesh and retry.

## Embedded libraries

- manifold-3d v3.5.1 (Apache-2.0) . https://github.com/elalish/manifold
- three.js r185 (MIT) . https://github.com/mrdoob/three.js

Both are inlined with provenance and license comments at their blocks.

## License

GPL-3.0
