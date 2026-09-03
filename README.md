# Viewpoint Transform / 视角变换

A Codex Skill for generating a new camera viewpoint of the **same** reference-anchored scene without silently redesigning the environment.

它解决的不是“生成一张风格相似的图”，而是“移动摄影机，同时尽量保持同一场景的空间、固定物、材质和光源连续性”。

## What it handles

- reverse shots / 反打
- 90-degree camera turns / 90° 转向
- lateral, dolly, and translated camera moves / 横移与前后移动
- camera-height changes / 机位升降
- wider or tighter environmental coverage / 环境景别变化
- floor-plan registration, wall contact, and cutaway reasoning / 平面图、墙体接触与剖切判断
- natural-language camera binding / 自然语言机位绑定
- post-generation continuity audit / 生成后连续性审计

The Skill supports prompt-only planning and image generation. It is backend-agnostic, while working especially well with reference-image-capable models such as GPT Image 2.

## Core principle

> Move the camera, not the scene.

A single image cannot prove the true unseen geometry. The Skill therefore distinguishes known, locked, inferred, and unknown facts; it produces source-anchored plausible coverage and recommends CAD/3D when exact repeatable geometry is required.

## Install

Clone or copy this repository into your Codex skills directory:

```bash
git clone https://github.com/flowfeast/viewpoint-transform.git ~/.codex/skills/viewpoint-transform
```

Then invoke it explicitly:

```text
Use $viewpoint-transform to create a Camera B reverse shot of this same room.
Keep the camera at 1.5 m, use a level wide-angle view, and preserve all fixed installations.
```

Codex may also select the Skill automatically for matching camera-view transformation tasks.

## Recommended inputs

1. An approved scene anchor image.
2. A natural-language spatial description, a marked floor plan, or both.
3. Narrowly assigned references for architecture, props, materials, lighting, or cinematography.

For a reverse shot, specify where Camera B stands and what it faces. “Reverse shot” alone does not define camera position, lens, height, or framing.

Natural-language example:

```text
Place Camera B beside the rear wall, between the door and stove, 1.5 m above the floor. Face the opposite wall with a level wide-angle view. Keep the well at the left edge and the doorway in the far-right third.
```

With a plan, mark or describe Camera A and Camera B, then let the Skill bind those cameras to stable wall, opening, and fixed-object IDs. When words and plan disagree, the Skill reports the conflict instead of blending them.

## Repository structure

```text
viewpoint-transform/
├── SKILL.md
├── agents/openai.yaml
└── references/
    ├── continuity-contract.md
    ├── plan-and-boundaries.md
    └── prompt-and-audit.md
```

## License

MIT
