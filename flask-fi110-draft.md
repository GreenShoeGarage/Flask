---
title: "FLASK: turning a thing into the hole it leaves behind"
series: Field Instruments
instrument: FI-110
status: draft
---

Every object you can hold started, at some point, as a decision about the space around
it. A cast part is not really the part. It is the absence you poured into. FLASK, the
110th Field Instrument, is a browser tool that takes a 3D object as an STL and hands you
back that absence: a mold, also as STL, ready to print or machine, for pouring
polyurethane resin or for hobby-scale injection.

I have wanted this one for a while. The bench already has instruments that flatten an
STL for the laser, nest sheet parts, and prep a print. What it did not have was the tool
that inverts an object into the thing that makes copies of it. That inversion is the
oldest trick in fabrication, cope and drag, the flask that holds the sand, and it turns
out to be a genuinely hard piece of geometry to do honestly.

## Honest is the whole job

It is easy to make a program that carves an object-shaped hole in a block and calls it a
mold. It is much harder to make one that will not lie to you. A mold has to release. If
a surface curls back under itself relative to the direction you pull the part, no rigid
mold on earth will let go of it, and a tool that quietly generates that mold anyway has
wasted your filament and your afternoon.

So the heart of FLASK is not the boolean subtraction. It is the analysis that runs
before it. Set the pull direction and the part lights up: faces that draft cleanly in
one colour, faces that draft toward the other half in another, near-vertical walls that
will drag flagged in amber, and true undercuts, the ones that trap the part, in red. It
reports the minimum draft it can find and refuses to pretend an undercut is fine. When
the part locks, it tells you to reorient the pull, move the parting, or reach for a
flexible mold. That refusal is the feature.

## Why a WASM kernel earns its megabyte

The hard rule for a Field Instrument is that it runs from a double-clicked file, offline,
forever. No server, no build step. FLASK breaks the spirit of "keep it tiny" and keeps
the letter of "runs from disk." The geometry kernel is manifold-3d, compiled to
WebAssembly, and it is the reason every piece FLASK exports is guaranteed watertight and
manifold, which for a mold is not a nicety but the entire point. A cavity with a leak in
its topology is not a mold, it is a mess.

To keep the single-file promise, that kernel is not fetched at runtime. It is inlined,
base64, and handed to the browser as raw bytes, instantiated straight from memory. The
file is heavier for it, around a megabyte and a half. I will take a heavier file over an
unreliable cavity every time, and I wrote the reasoning into the About panel and the
README so the tradeoff is on the record, the way the earlier server-touching instruments
carry their asterisks.

## What it does, plainly

Load an STL. FLASK welds it, checks it for holes and flipped normals, fixes what it can,
and shows you the volume and the box it lives in. Pick a pull direction and a parting
height. Choose a one-piece open-face block for a relief or a medallion, or a two-piece
split for a full object. Set your shrink for the material, your wall thickness, your key
clearance. Generate. Out comes a mold with a sprue, vents at the high points with little
overflow reservoirs, and registration keys laid out asymmetrically so the halves only
mate one way. Every control carries a short note on what it does and why it matters,
because the point was always to account for best practice, not to hide it.

## What it is not

FLASK builds the geometric heart of a mold. It is not a production injection-tool
designer. No ejector pins, no cooling channels, no hot runners, no slides or lifters, no
mold base. Injection mode gives you a cavity, a core, a basic gate and a runner stub, and
says so plainly. Cores for internal hollows are on the roadmap, not in v1.0. And draft,
for now, is measured and flagged rather than added automatically to arbitrary meshes,
because doing that robustly is its own project. I would rather ship the honest analysis
now and the automatic draft when it actually works.

That is the shape of it. A tool that inverts an object into the space that copies it, and
that respects you enough to tell you when the space will not let go.

Make. Hack. Learn. Share. Repeat.
