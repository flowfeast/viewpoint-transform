# Scene Continuity Contract

Use this contract before planning or generating a transformed view.

## Evidence states

- `KNOWN`: directly visible in an image or stated by an authoritative plan/specification.
- `LOCKED`: explicitly approved and must be preserved. A lock does not convert an inference into fact.
- `INFERRED`: a conservative continuation supported by the available evidence.
- `UNKNOWN`: insufficient evidence. Fill only the minimum required for a coherent requested view.

Keep evidence and approval separate when needed, for example `evidence: INFERRED` and `approval: LOCKED`.

## Compact schema

```yaml
scene_continuity_contract:
  identity:
    scene_id:
    period:
    geographic_context:
    temporal_state:
  reference_roles:
    scene_anchor:
    set_spec:
    spatial_layout: []
    architecture: []
    props: []
    materials: []
    lighting:
    cinematography:
  topology:
    geometry_accuracy: G0 | G1 | G2
    coordinate_frame:
    wall_graph:
      - wall_id:
        adjacent_walls: []
        openings: []
        evidence: KNOWN | INFERRED | UNKNOWN
        approval: OPEN | LOCKED
    circulation: []
  spatial_anchors:
    - id:
      category: wall | opening | fixed_installation | large_movable | signature_prop
      wall_owner:
      contact_type: flush | embedded | wall_mounted | corner_locked | freestanding | suspended | unknown
      clearance_to_wall:
      no_walkable_gap: true | false | unknown
      plan_position:
      evidence: KNOWN | INFERRED | UNKNOWN
      approval: OPEN | LOCKED
      child_assembly_id:
  supported_object_assemblies:
    - id:
      support_anchor_id:
      children:
        - id:
          object_type:
          count:
          material_or_color:
          state:
          support_slot:
          continuity_priority: critical | high | normal
  materials: []
  aging_and_use_history: []
  world_light_sources: []
  exclusions: []
  camera_a:
    position:
    height:
    yaw:
    pitch:
    roll:
    fov_or_lens_behavior:
    boundary_mode: physical_interior | through_opening | exterior_looking_in | virtual_cutaway | unresolved
    confidence:
  camera_b:
    placement_source: natural_language | plan_marker | natural_language_plus_plan | approved_candidate | unresolved
    natural_language_binding:
      raw_description:
      reference_anchors: []
      spatial_relation:
      facing_direction_or_look_at:
      coverage_constraints: []
      parsed_uncertainties: []
    plan_binding:
      plan_id:
      camera_marker_id:
      registered_position:
      registered_direction:
      confidence: none | low | medium | high
    position:
    height:
    yaw:
    pitch:
    roll:
    fov_or_lens_behavior:
    look_at:
    aspect_ratio:
    environment_shot_scale: detail | medium_space | wide_space | full_room_establishing
    frame_boundaries:
      left:
      right:
      top:
      bottom:
    boundary_mode: physical_interior | through_opening | exterior_looking_in | virtual_cutaway | unresolved
  visibility_by_anchor:
    - anchor_id:
      camera_a_relation:
      camera_b_relation:
      expected_screen_side:
      expected_action: inherit | partial_edge | occluded | exit | unresolved
      reasoning:
  uncertainties: []
```

Geometry levels:

- `G0`: visual plausibility only; no topology claim.
- `G1`: relational layout, adjacency, orientation, and circulation remain coherent.
- `G2`: measured plan, CAD, photogrammetry, or 3D geometry is authoritative; image generation is a visualization layer.

## Visibility reasoning

For each anchor, reason from its floor footprint or wall ownership rather than its image bounding box:

1. Is it in front of Camera A, behind it, crossing the camera plane, or on a side boundary?
2. Which wall or floor zone owns it?
3. After the Camera B transform, is it in front of the new camera and inside the frustum?
4. Is it hidden by architecture or another object?
5. If it leaves frame, can its footprint still create a physically valid edge glimpse?
6. Is a newly visible region known, conservatively inferred, or unsupported?

Project walls before objects. Preserve the wall owner and contact relation even when a wall changes from side wall to frontal wall on screen.

Treat objects resting on a stove, counter, shelf, or table as a parent-child assembly. Perspective and occlusion may change apparent overlap, but must not arbitrarily replace, multiply, remove, restack, recolor, or relocate critical children.

## Stop conditions

Stop before generation when:

- two locked sources specify incompatible topology;
- Camera B lacks a resolved natural-language, plan, or explicitly approved placement;
- an opaque wall crossing lacks a real opening or approved virtual cutaway;
- a critical opening, wall owner, contact relation, or near-camera anchor is unresolved;
- the user asks for the true unseen reverse side from one image;
- exact dimensions or a repeatable camera path are required without deterministic geometry;
- the requested transform requires a materially different set design.
