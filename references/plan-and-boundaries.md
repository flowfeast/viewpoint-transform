# Plan Registration and Boundaries

Read this reference whenever the user supplies a floor plan, sketch, survey, elevation, top-down layout, or requests a camera close to or beyond a wall.

## Two binding modes

### Natural-language binding

Natural language is a first-class spatial input, not a decorative prompt fragment. Parse it into:

- the camera's support zone or position relative to named, stable anchors;
- facing direction or look-at target;
- camera height and level/tilt/roll constraints;
- lens or FOV behavior and environment shot scale;
- left, right, top, and bottom coverage boundaries;
- clearance, circulation, and wall-boundary implications;
- unresolved ambiguity.

Prefer relations that can be checked: “between Door D1 and Stove S1,” “0.5 m from Wall W2,” or “face Wall W4.” Terms such as “behind,” “opposite,” “wide,” or “reverse” are incomplete until tied to a coordinate frame, stable anchors, or observable frame boundaries.

### Plan binding

Use a readable plan to register Camera A, Camera B, walls, openings, fixed-element footprints, handedness, orientation, and scale. The plan is spatial evidence only.

### Combined binding

When both are supplied, map each natural-language anchor to a plan ID and verify that position, direction, clearance, and sightline agree. Explicit user locks take priority, then authoritative plan topology. Surface unresolved conflict instead of averaging two placements.

## Authority boundary

A plan may control only spatial facts it actually contains: walls, adjacency, openings, circulation, fixed-element footprints, orientation, camera marks, and scale. It does not control materials, color, lighting, aging, decoration, or photographic style.

The scene anchor remains visual truth. A verified plan wins a spatial conflict only where registration is reliable and it represents the same scene state.

## Register the plan

- Preserve handedness; detect rotated or mirrored scans before assigning left/right.
- Give each wall segment, opening, fixed installation, and camera mark a stable ID.
- Match stable landmarks such as corners, posts, door/window openings, wells, built-ins, and floor-contact features—not decorative similarity.
- Record scale status and confidence. High confidence normally needs a camera marker or scale plus three consistent non-collinear landmarks; medium needs two landmarks with coherent wall/opening handedness.
- Low-confidence registration may guide conservative completion but cannot delete, relocate, or invent a critical known feature.

```yaml
plan_registration:
  scene_anchor_id:
  plan_id:
  plan_kind: measured | production | architectural | sketch | inferred
  orientation:
  scale_status: measured | approximate | absent
  handedness: normal | mirrored | unresolved
  shared_landmarks: []
  wall_graph: []
  openings: []
  fixed_element_footprints: []
  camera_a_on_plan:
  camera_b_on_plan:
  registration_confidence: low | medium | high
  conflicts: []
  unresolved: []
```

Camera A registration does not determine Camera B. If Camera B is absent, ask the user to mark it or describe it relative to stable anchors; do not silently choose an in-place 180-degree yaw. A complete natural-language binding is sufficient when no plan exists, but its uncertainty must remain explicit.

## Preserve wall-object contact

For each fixed installation, near-camera object, opening, or wall-mounted prop, record:

```yaml
boundary_contact:
  anchor_id:
  wall_owner:
  contact_type: flush | embedded | wall_mounted | corner_locked | freestanding | suspended | unknown
  clearance_to_wall:
    value:
    unit:
    evidence: KNOWN | INFERRED | UNKNOWN
  orientation_to_wall: parallel | perpendicular | angled | radial | unknown
  wall_station_or_corner_relation:
  no_walkable_gap: true | false | unknown
  confidence: low | medium | high
```

Do not invent zero clearance because an object merely looks close. Once `flush` or `embedded` is confirmed, forbid visible floor, a corridor, or a void behind the object.

## Resolve camera-boundary mode

```yaml
camera_boundary:
  camera_id:
  mode: physical_interior | through_opening | exterior_looking_in | virtual_cutaway | unresolved
  body_position_relative_to_room:
  near_plane_relative_to_room:
  intersected_wall_ids: []
  view_path_opening_id:
  hidden_or_cutaway_wall_ids: []
  geometry_preservation: required
  user_approval: explicit | not_required | missing
```

- `physical_interior`: camera, near plane, and sightline remain inside the room.
- `through_opening`: the view uses a registered opening; preserve jambs, wall thickness, and occlusion.
- `exterior_looking_in`: the camera is outside and looks through a real opening.
- `virtual_cutaway`: an opaque wall segment is render-hidden with explicit user approval; its plane, thickness, corners, and attached-object constraints remain active.
- `unresolved`: stop before generation.

An extreme lens does not authorize a cutaway. A difficult camera does not authorize moving a wall, enlarging the room, detaching fixtures, or inserting floor behind a wall-flush object.

## Bind a plan to image generation

Attach the scene anchor first and the plan second. State that the plan controls registered geometry only and must not appear as a diagram, overlay, grid, label, color scheme, or visual style in the result. Describe Camera A and Camera B in plain spatial language, enumerate visibility changes, and name every critical contact and boundary mode.

Attaching a plan does not guarantee exact perspective. For exact matching, use CAD/3D as the deterministic geometry owner and image generation as visualization only.
