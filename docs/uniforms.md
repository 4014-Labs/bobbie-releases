# Shader Resources And Interfaces

This file is the quick-reference index for Bobbie shader-facing names.

For the full contract, see:

- [`shader_contract.md`](shader_contract.md)

## Canonical readable resources

- `base_tex`
- `base_tex_prev_NNN`
- `base_tex_next_NNN`
- `ext_tex_NNN`
- `pass_NNN`
- `pass_NNN_prev`

Suffix rules:

- `base_tex_prev_NNN`, `base_tex_next_NNN`, `pass_NNN`, and `pass_NNN_prev` use `001` through `999`
- `ext_tex_NNN` uses `000` through `999`

Examples:

- `base_tex_prev_001`
- `base_tex_next_002`
- `ext_tex_000`
- `pass_001`
- `pass_002_prev`

## Canonical globals

- `bobbie_globals`
- `u_time_seconds`
- `u_frame_index`
- `u_width`
- `u_height`
- `u_resolution`
- `u_inv_resolution`

These names live in the Bobbie globals contract. The current globals block does
not include `u_uv` or `u_pixel_coord`.

## Runtime stage inputs and built-ins

- fragment input varying at `location = 0`:
  - `u_uv`
  - `uv`
  - `v_uv`
  - `v_tex_coord`
  - `tex_coord`
- compute built-in:
  - `u_pixel_coord`
- `gl_FragCoord`
- `gl_GlobalInvocationID`

## Canonical writable outputs

- fragment output:
  - `bobbie_out_color`
- compute output image:
  - `bobbie_output_image`

## Practical notes

- numbered resources always use zero-padded suffixes
- external textures are bound explicitly by name with `--ext-texture ext_tex_NNN=SOURCE`
- fragment shaders may declare the runtime UV input at `location = 0` using `u_uv`, `uv`, `v_uv`, `v_tex_coord`, or `tex_coord`
- the runtime UV input is not a bound uniform and is not part of the `BobbieGlobals` block
- `pass_NNN` may only read earlier passes
- `pass_NNN_prev` may only read the same pass or an earlier accumulation-enabled pass
- accumulation history exists only for passes marked with `--accum`
- the validator rejects undocumented resource names and several legacy aliases
