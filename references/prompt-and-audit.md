# Prompt Compiler and Continuity Audit

Compile only after the Scene Continuity Contract is coherent. Omit empty sections and unsupported details.

## Prompt template

```text
TASK
Generate a new camera view of the exact same physical scene represented by the Scene Anchor. Move the camera, not the scene. This is source-anchored plausible coverage, not a claim that unseen geometry was recovered.

REFERENCE ROLES
Image 1 = Scene Anchor and highest-priority visible scene truth.
Image 2 = Spatial Layout Reference, when supplied. It controls only registered topology, openings, fixed-element footprints, camera placement, orientation, and scale. Do not copy its diagram style, labels, grid, colors, or annotations.
[Give every additional reference a narrow role and state what it must not contribute.]

SCENE IDENTITY
[Period, region, room identity, scale, temporal state, and approved set state.]

CAMERA TRANSFORM
[Camera A evidence and uncertainty. Camera B position, height, yaw/pitch/roll, lens/FOV behavior, look-at target, aspect ratio, shot scale, frame boundaries, and physical feasibility.]

SPATIAL BINDING
[State whether Camera B comes from natural language, a plan marker, or both. For natural language, name the stable anchors and parsed relations. For a plan, name the registered camera marker, wall/opening IDs, handedness, scale status, and confidence. Record any conflict resolution.]

PLAN AND WALL CONTRACT
[Registration confidence; projected wall IDs; openings; fixed elements; wall ownership; contact; clearance; gap policy; camera-boundary mode; approved cutaway if any.]

VISIBILITY AND OCCLUSION
[For every critical anchor: Camera A relation, Camera B relation, expected screen side, and action: inherit, partial_edge, occluded, or exit.]

SUPPORTED-OBJECT CONTINUITY
[For every visible support surface: enumerate critical child objects by count, identity, state, support slot, and expected Camera B visibility.]

UNSEEN SPACE POLICY
Infer only the minimum continuation required by the requested view. Do not add elaborate structures or signature features to unsupported regions.

PRODUCTION-DESIGN LOCKS
[Materials, aging, use history, set-dressing density, local color, social class, and exclusions.]

LIGHTING CONTINUITY
Preserve the same moment and world-space light sources. Allow only the view-dependent change in visible direction, shadow projection, reflections, and exposure.

DO NOT
Do not mirror the anchor, generate a different room, redesign or clone architecture, duplicate signature elements, move openings, change wall ownership, detach fixtures, create gaps behind no-gap objects, expand the room, erase an opaque wall without an approved cutaway, change period or region, or beautify unknown space.
```

Generate one view at a time. Keep the original scene anchor attached to every attempt. When using a plan, keep it second and repeat its spatial-only role.

## Audit

Inspect actual pixels, not merely a successful tool response.

```yaml
continuity_audit:
  same_scene_identity: PASS | MINOR_DRIFT | FAIL
  camera_transform: PASS | MINOR_DRIFT | FAIL
  camera_b_shot_specification: PASS | MINOR_DRIFT | FAIL
  natural_language_binding: PASS | MINOR_DRIFT | FAIL | NOT_APPLICABLE
  plan_binding: PASS | MINOR_DRIFT | FAIL | NOT_APPLICABLE
  topology_and_scale: PASS | MINOR_DRIFT | FAIL
  wall_graph_projection: PASS | MINOR_DRIFT | FAIL
  wall_contact_and_clearance: PASS | MINOR_DRIFT | FAIL
  camera_wall_feasibility: PASS | MINOR_DRIFT | FAIL
  fixed_elements: PASS | MINOR_DRIFT | FAIL
  openings: PASS | MINOR_DRIFT | FAIL
  duplication_and_mirroring: PASS | MINOR_DRIFT | FAIL
  supported_object_continuity: PASS | MINOR_DRIFT | FAIL
  materials_and_aging: PASS | MINOR_DRIFT | FAIL
  period_and_region: PASS | MINOR_DRIFT | FAIL
  lighting_physics: PASS | MINOR_DRIFT | FAIL
  unsupported_invention: PASS | MINOR_DRIFT | FAIL
  plan_registration_adherence: PASS | MINOR_DRIFT | FAIL | NOT_APPLICABLE
```

Use `FAIL` when the defect breaks scene identity, camera meaning, topology, a locked element, or the evidence boundary. Use `MINOR_DRIFT` only for a local defect that can be repaired without changing the transform or scene contract.

Audit every known opening, fixed installation, and critical near-camera object individually. A missing known anchor, unsupported opening, wrong-side placement, mirrored landmark, changed wall owner, gap behind a flush object, or critical support-object replacement is `FAIL`.

## Repair order

Repair the earliest failed contract:

1. camera transform, camera-wall feasibility, or framing;
2. wall graph, topology, openings, contacts, or fixed installations;
3. visibility, duplication, or supported-object continuity;
4. materials, aging, period, or region;
5. lighting physics;
6. minor photographic finish.

Change one failed target per attempt and restate every higher-priority invariant. After two targeted failures on the same critical contract, stop and request stronger evidence or deterministic 3D geometry.
