# Release History

## v0.2.4 (2026-04-22)

### Linux x86_64 Build

- OpenGL ES backend
- Binary: `bobbie-v0.2.4-linux-x86_64.tar.gz`

### Features

- `bobbie validate` now checks shaders against Bobbie's GLES compatibility execution path before render
- canonical readable texture bindings are validated by resource-name order
- binding-schema failures now report the expected binding and canonical ordering hint
- release docs now include the binding formula used by shader generators
- follow-up TODO added for replacing CLI-based SPIR-V reflection with a real SPIR-V API

---

## v0.2.3 (2026-04-22)

### Linux x86_64 Build

- OpenGL ES backend
- Binary: `bobbie-v0.2.3-linux-x86_64.tar.gz`

### Features

- `bobbie validate` now enforces the fragment interface contract used at render time
- runtime UV fragment inputs must be `vec2` stage inputs at `location = 0`
- `bobbie_out_color` must be a `vec4` stage output at `location = 0`
- Linux OpenGL rendering now keeps the GLES compatibility execution strategy as the primary supported path while still using native SPIR-V execution when the runtime advertises it
- release package refreshed from the current Bobbie workspace state

---

## v0.2.2 (2026-04-03)

### Linux x86_64 Build

- OpenGL ES backend
- Binary: `bobbie-v0.2.2-linux-x86_64.tar.gz`

### Features

- release docs now describe the runtime UV fragment input correctly
- UV is documented as a fragment varying at `location = 0`, not a bound uniform
- release package refreshed from the current Bobbie workspace state

---

## v0.2.1 (2026-03-26)

### Linux x86_64 Build

- OpenGL ES backend
- Binary: `bobbie-v0.2.1-linux-x86_64.tar.gz`

### Features

- Structured render error handling
- Improved runtime error reporting for render failures

---

## v0.3.0 (2026-03-25)

### macOS ARM64 Build

- **New:** Metal backend for macOS
- **Architecture:** Apple Silicon (arm64)
- **Binary:** `bobbie-v0.3.0-macos-arm64.tar.gz`
- **Size:** ~1.0 MB compressed, 4.2 MB uncompressed

### Features

- Metal GPU backend for fragment and compute shaders
- SPIR-V to Metal Shading Language (MSL) translation via SPIRV-Cross
- Full texture management (base, temporal, external, pass outputs)
- Uniform buffer support for BobbieGlobals

### Build Details

- Built from `Mac` branch
- CMake flags: `-DBOBBIE_ENABLE_METAL_BACKEND=ON`
- Tested on Apple M2 with 285 frame video processing

---

## v0.2.0

### Linux x86_64 Build

- OpenGL ES backend
- Binary: `bobbie-v0.2.0-linux-x86_64.tar.gz`
