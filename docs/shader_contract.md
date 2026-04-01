# Bobbie Shader Contract

This document describes the current Bobbie shader contract for public release
consumers. It is intended to be exhaustive for the current implementation.

This contract is about what shader authors and operators may rely on. It does
not describe every internal implementation detail of the Bobbie runtime.

## Scope

This document covers:

- accepted shader stages
- canonical resource names
- pass numbering rules
- temporal source-frame rules
- accumulation-history rules
- external texture naming and binding rules
- global uniforms and the current globals block layout
- fragment and compute output conventions
- validation and render-time rejection rules
- the current render command surface that materially affects shader behavior

## Pipeline model

Bobbie executes an ordered shader pipeline.

- each positional shader path is one pass
- pass numbering starts at `001`
- pass numbering is derived from CLI order, not from filenames
- Bobbie supports at most `999` passes

Examples:

- the first shader is pass `001`
- the second shader is pass `002`
- the third shader is pass `003`

The canonical pass namespace is:

- `pass_001` through `pass_999`
- `pass_001_prev` through `pass_999_prev`

## Accepted shader stages

Bobbie accepts exactly one SPIR-V entry point per module.

Supported stages:

- fragment
- compute

Rejected stages include:

- vertex
- geometry
- tessellation control
- tessellation evaluation

The canonical authoring target is GLSL `#version 450` for both fragment and
compute shaders.

## Canonical readable resources

Bobbie reserves these readable resource names:

- `base_tex`
- `base_tex_prev_NNN`
- `base_tex_next_NNN`
- `ext_tex_NNN`
- `pass_NNN`
- `pass_NNN_prev`

Numbering rules:

- `base_tex_prev_NNN` and `base_tex_next_NNN` require `NNN` in `001` through `999`
- `ext_tex_NNN` uses `NNN` in `000` through `999`
- `pass_NNN` and `pass_NNN_prev` use `NNN` in `001` through `999`
- numbered suffixes are always zero-padded to three digits

### `base_tex`

`base_tex` is the current input frame for the current render step.

### `base_tex_prev_NNN`

`base_tex_prev_NNN` names previous base input frames.

Rules:

- the requested depth must be less than or equal to `--source-prev-frames`
- depths are positive and zero-padded
- if a shader references a depth outside the configured window, validation fails

### `base_tex_next_NNN`

`base_tex_next_NNN` names future base input frames.

Rules:

- the requested depth must be less than or equal to `--source-next-frames`
- depths are positive and zero-padded
- if a shader references a depth outside the configured window, validation fails

### `ext_tex_NNN`

`ext_tex_NNN` names explicitly bound external textures.

Rules:

- names are validated structurally during `bobbie validate`
- actual bindings are enforced during `bobbie render`
- each referenced external texture must be bound with `--ext-texture ext_tex_NNN=SOURCE`
- missing bindings fail render
- duplicate bindings fail render

### `pass_NNN`

`pass_NNN` reads the output of an earlier pass.

Rules:

- a pass may only read earlier passes
- self-reference is illegal
- forward references are illegal
- out-of-order references fail validation

### `pass_NNN_prev`

`pass_NNN_prev` reads accumulation history for a pass.

Rules:

- a pass may read its own or an earlier pass's accumulation history
- forward accumulation-history references are illegal
- the referenced pass must be marked with `--accum`
- if the referenced pass is not accumulation-enabled, validation fails

## Canonical globals and built-ins

Bobbie accepts these canonical global names:

- `bobbie_globals`
- `u_time_seconds`
- `u_frame_index`
- `u_width`
- `u_height`
- `u_resolution`
- `u_inv_resolution`
- `u_uv`
- `u_pixel_coord`
- `gl_FragCoord`
- `gl_GlobalInvocationID`

### Practical meaning of the globals

- `u_time_seconds`
  - floating-point time for the current frame
- `u_frame_index`
  - zero-based frame index
- `u_width`
  - frame width as a float
- `u_height`
  - frame height as a float
- `u_resolution`
  - `vec2(width, height)`
- `u_inv_resolution`
  - `vec2(1.0 / width, 1.0 / height)`
- `u_uv`
  - canonical Bobbie name; accepted by validation as part of the contract
- `u_pixel_coord`
  - canonical Bobbie name; accepted by validation as part of the contract
- `gl_FragCoord`
  - fragment built-in for pixel-space addressing
- `gl_GlobalInvocationID`
  - compute built-in for dispatch-space addressing

### Current globals block layout

If you choose to declare the canonical global block, the current OpenGL runtime
fills it with this std140-compatible layout:

```glsl
layout(std140) uniform BobbieGlobals {
    float u_time_seconds;
    int u_frame_index;
    float u_width;
    float u_height;
    vec2 u_resolution;
    vec2 u_inv_resolution;
};
```

Notes:

- the validator accepts the resource name `bobbie_globals`
- the current OpenGL source-fallback path may look for block name `BobbieGlobals` in fragment shaders and `bobbie_globals` in compute shaders
- the individual canonical names are also reserved as contract names even when authors do not declare the block explicitly

## Canonical writable outputs

### Fragment output

Fragment shaders should write the final pixel color to:

- `bobbie_out_color`

### Compute output image

Compute shaders should write to:

- `bobbie_output_image`

## Temporal window semantics

The base-stream temporal window is controlled by CLI flags, not by automatic
shader scanning.

Relevant flags:

- `--source-prev-frames N`
- `--source-next-frames N`

Defaults in the current CLI:

- `--source-prev-frames 1`
- `--source-next-frames 1`

This means the default accessible temporal names are:

- `base_tex`
- `base_tex_prev_001`
- `base_tex_next_001`

## Accumulation semantics

Accumulation is opt-in per pass.

The CLI marks accumulation-enabled passes with:

- `--accum`

Rule:

- `--accum` applies to the immediately preceding shader path

Pipeline-level accumulation capacity is limited by:

- `--max-accumulation-buffers N`

Validation fails if the number of accumulation-enabled passes exceeds that
maximum.

## External texture binding contract

External textures are explicit named bindings.

Binding syntax:

```text
--ext-texture ext_tex_000=/path/to/source
```

Binding-name rules:

- the left side must match `ext_tex_NNN`
- `NNN` is exactly three digits
- source paths may not be empty

Supported source forms in the current runtime:

- normal image/video/media files decoded via external FFmpeg tools
- Bobbie rawvideo manifests ending in `*.bobbie.json`

### Bobbie rawvideo manifest shape

The current runtime expects these manifest fields:

- `"kind": "rawvideo"`
- `"pixel_format"`
- `"width"`
- `"height"`
- `"fps_num"`
- `"fps_den"`
- `"data_path"`
- optional `"row_stride_bytes"`

`data_path` may be relative to the manifest file location.

Current manifest pixel formats:

- `rgba8`
- `bgra8`
- `rgb24`
- `gray8`

Directory-backed external textures are not implemented in the current runtime.

### External texture timeline policy

External texture frame selection is controlled by:

- `--ext-texture-sync frame|time`
- `--ext-texture-eof-mode hold|clear|loop`

Current modes:

- sync:
  - `frame`
  - `time`
- EOF:
  - `hold`
  - `clear`
  - `loop`

## Current render-path limits

These current implementation details are important for release consumers:

- the current OpenGL backend supports `rgba8` render execution
- the CLI accepts `rgba8`, `bgra8`, `rgb24`, and `gray8` metadata values
- the canonical shader authoring target is GLSL `#version 450`
- the current runtime rejects software renderers such as `llvmpipe` and `SwiftShader` as valid GPU devices
- external textures are explicit named bindings, not implicit ordered inputs
- the base stream may come from file paths or `-` for stdin/stdout
- external textures are file-backed sources, not anonymous pipe streams

## Unsupported or legacy names

The validator explicitly rejects several old naming schemes.

Legacy names that are rejected:

- `u_current_frame`
  - use `base_tex`
- `accum_prev_tex`
  - use `pass_NNN_prev`
- `tex_NNN`
  - use `ext_tex_NNN`
- `u_prev_frame_NNN`
  - use `base_tex_prev_NNN`
- `u_next_frame_NNN`
  - use `base_tex_next_NNN`

Any undocumented resource name is rejected.
