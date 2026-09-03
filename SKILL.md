---
name: viewpoint-transform
description: Generate or plan new camera viewpoints of the same reference-anchored scene while preserving spatial identity, topology, fixed objects, materials, and world-space lighting. Use for reverse shots, 90-degree turns, camera translations, height changes, wider coverage, and other same-scene view changes. Do not use for free redesign, unsupported exact reconstruction, character-only coverage, or video generation.
---

# Viewpoint Transform / 视角变换

Move the camera, not the scene.

Create source-anchored plausible coverage of the same physical environment. Never claim that a single image reveals the true unseen side of a location.

## Choose the mode

- `prompt_only`: return a continuity contract and production-ready prompt when the user asks for analysis, planning, or a prompt.
- `generate`: generate or edit an image only when the user explicitly asks to create, render, or make the image.

Use the host's installed image-generation skill or tool in `generate` mode. If the user explicitly requires GPT Image 2 or another named backend, follow that backend's execution and cost rules.

## Required evidence

Require a usable `scene_anchor`: an approved scene image, or an approved set master with its scene specification.

Camera B may be bound in either of two first-class ways:

- `natural_language_binding`: a spatial description relative to stable anchors, such as “stand beside the rear wall between the door and stove, 1.5 m high, face the opposite wall, keep the well at the left frame edge.” Convert it into explicit position, orientation, height, look-at target, coverage boundaries, and uncertainty.
- `plan_binding`: a marked floor plan, stage plan, sketch, elevation, or top-down layout. Register Camera A, Camera B, walls, openings, fixed-element footprints, handedness, and scale where available.

Optional evidence may also include alternate verified views, CAD renders, and narrowly assigned architecture, prop, material, lighting, or cinematography references. Give each source one explicit role; never let a prop reference redesign the room or a plan dictate photographic style.

When natural language and a plan are both present, bind the words to stable plan IDs and check for conflicts. Explicit user locks win; do not silently average or blend incompatible placements.

If only one image exists, label unseen regions `INFERRED` or `UNKNOWN` and complete only what the requested view requires. If exact dimensions, repeatable parallax, or a measured camera path is required, stop and recommend a plan/CAD/3D workflow.

## Workflow

1. Inventory the scene anchor: topology, openings, fixed installations, signature props, materials, aging, set-dressing density, and world-space light sources.
2. Build stable world-space IDs for walls and spatial anchors before using screen-left or screen-right language. Image position is a projection, not object identity.
3. Resolve Camera B from natural language, a plan, or both. When a plan or boundary-sensitive camera is involved, read [references/plan-and-boundaries.md](references/plan-and-boundaries.md). Register walls, openings, camera positions, and wall-object contact without importing diagram appearance.
4. Read [references/continuity-contract.md](references/continuity-contract.md). Build the compact Scene Continuity Contract and classify claims as `KNOWN`, `LOCKED`, `INFERRED`, or `UNKNOWN`.
5. Freeze Camera A only as precisely as the evidence allows: position, height, yaw, pitch, roll, lens/FOV behavior, support surface, clearance, and boundary mode.
6. Resolve Camera B as an explicit transform of Camera A: position, height, yaw/pitch/roll, lens/FOV behavior, aspect ratio, environment shot scale, coverage boundaries, look-at target, and boundary mode. “Reverse shot” alone is not a complete Camera B specification.
7. Project the wall graph through Camera B, then transform every registered anchor while preserving wall ownership, contact, footprint, and support-object relationships. Classify each anchor as `inherit`, `partial_edge`, `occluded`, `exit`, or `unresolved`.
8. Do not generate while Camera B, an opaque-wall crossing, a critical opening, a wall/contact relation, or a near-camera anchor remains unresolved.
9. Read [references/prompt-and-audit.md](references/prompt-and-audit.md), compile the prompt, and generate one view at a time. Attach the original scene anchor to every attempt; attach a plan second when it is authoritative for layout.
10. Inspect the actual output anchor by anchor. Repair only the earliest failed contract and repeat all higher-priority invariants.

## Transform presets

### Reverse shot / 反打

- Treat reverse as a directional relationship, not an automatic in-place 180-degree yaw.
- Require Camera B's plan position or a user-approved description relative to stable anchors.
- Resolve camera height, facing direction, lens/FOV, aspect ratio, shot scale, and frame boundaries separately.
- Never mirror the scene or copy Camera A's frontal wall onto the reverse wall.
- Preserve each object's world-space wall owner even when it moves to another screen side.

### Quarter turn / 90-degree turn

- Keep position and height stable unless the user declares a move.
- Apply the requested yaw direction.
- Preserve world-space object positions and light sources.

### Translation, dolly, lateral move, or height change

- Change only the declared camera variables.
- Require plausible parallax, reveal order, occlusion, and perspective change.
- Do not present a crop, zoom, mirrored image, or rotated subject as camera movement.

## Invariants

Unless explicitly changed, lock scene identity, period, region, room scale, structural system, openings, fixed installations, material family, aging/use history, set-dressing density, temporal state, and world-space lighting.

Allow only the declared camera variables and their natural visual consequences. Never repair a camera problem by expanding the room, moving a wall, detaching a fixture, duplicating a landmark, or beautifying unknown space.

A generated view is an output, not new source evidence. Return every subsequent view to the original scene anchor and canonical continuity contract.

## Completion

For `prompt_only`, return the compact contract, uncertainty note, and complete prompt.

For `generate`, return the image, the resolved transform, and an audit verdict of `PASS`, `MINOR_DRIFT`, or `FAIL`. A result is complete only when no critical continuity item is `FAIL`. After two targeted failures on the same critical contract, stop and request stronger evidence or deterministic geometry.
