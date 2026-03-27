# Bobbie CLI Reference

This document describes the Bobbie runtime CLI surface for public release
consumers.

For the shader/resource side of the contract, see:

- [`shader_contract.md`](shader_contract.md)

## Top-level commands

Bobbie currently accepts:

- `bobbie license`
- `bobbie validate ...`
- `bobbie render ...`

Aliases:

- `bobbie --license`
- `bobbie -L`
- `bobbie --help`
- `bobbie -h`

## Full current usage shape

```text
bobbie --license
bobbie -L
bobbie license

bobbie validate [--source-prev-frames N] [--source-next-frames N] [--max-accumulation-buffers N] \
    path/to/pass_001.spv [--accum] [path/to/pass_002.spv [--accum] ...]

bobbie render --input-pixels input.rgba|- --output-pixels output.rgba|- \
    --pixel-format rgba8 --width 1920 --height 1080 --fps-num 30000 --fps-den 1001 \
    [--input-row-stride-bytes N] [--output-row-stride-bytes N] \
    [--gpu-batch-frames N] [--frame-timeout-ms N] \
    [--ext-texture-sync frame|time] [--ext-texture-eof-mode hold|clear|loop] \
    [--ext-texture ext_tex_000=/path/to/source ...] \
    [--source-prev-frames N] [--source-next-frames N] [--max-accumulation-buffers N] \
    path/to/pass_001.spv [--accum] [path/to/pass_002.spv [--accum] ...]
```

## `validate`

Purpose:

- validate a pipeline of one or more SPIR-V shader modules

Supported flags:

- `--source-prev-frames N`
- `--source-next-frames N`
- `--max-accumulation-buffers N`
- `--accum`

Defaults:

- `--source-prev-frames 1`
- `--source-next-frames 1`
- `--max-accumulation-buffers 0`

## `render`

Purpose:

- execute a validated SPIR-V pipeline on rawvideo input using the selected Bobbie backend

Required flags:

- `--input-pixels PATH|-`
- `--output-pixels PATH|-`
- `--pixel-format FORMAT`
- `--width N`
- `--height N`
- `--fps-num N`
- `--fps-den N`

Supported flags:

- `--input-pixels PATH|-`
- `--output-pixels PATH|-`
- `--pixel-format rgba8|bgra8|rgb24|gray8`
- `--width N`
- `--height N`
- `--fps-num N`
- `--fps-den N`
- `--input-row-stride-bytes N`
- `--output-row-stride-bytes N`
- `--gpu-batch-frames N`
- `--frame-timeout-ms N`
- `--ext-texture NAME=SOURCE`
- `--ext-texture-sync frame|time`
- `--ext-texture-eof-mode hold|clear|loop`
- `--source-prev-frames N`
- `--source-next-frames N`
- `--max-accumulation-buffers N`
- `--accum`

Current defaults:

- `--gpu-batch-frames 1`
- `--frame-timeout-ms 1000`
- `--ext-texture-sync time`
- `--ext-texture-eof-mode hold`
- row strides default to packed rows

### Pixel formats

Supported values:

- `rgba8`
- `bgra8`
- `rgb24`
- `gray8`

### Row-stride rules

- `--input-row-stride-bytes` and `--output-row-stride-bytes` must be at least the packed row size
- packed row size is `width * 4` for `rgba8` and `bgra8`
- packed row size is `width * 3` for `rgb24`
- packed row size is `width * 1` for `gray8`

### External texture binding syntax

```text
--ext-texture ext_tex_000=/path/to/source
```

Rules:

- `ext_tex_NNN` is the only accepted binding-name family
- the binding name must be zero-padded
- source paths may not be empty
- missing referenced bindings fail render
- duplicate bindings fail render

### Runtime failure reporting

Current render failures are normalized into structured reports containing:

- `class=...`
- `recovery=...`
- `code=...`
- `summary=...`
- `raw=...`
- `log_context=...`

## Current behavior release consumers should assume

- Bobbie operates on SPIR-V modules, not raw GLSL source
- GLSL should be compiled to SPIR-V before calling Bobbie
- the canonical authoring target is GLSL `#version 450`
- the current Linux OpenGL backend supports fragment and compute execution
- the base rawvideo stream may use files or stdin/stdout
- Bobbie does not accept compressed video containers directly for the base stream
