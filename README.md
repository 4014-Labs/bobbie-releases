# Bobbie Releases

Public binary distribution repository for Bobbie.

This repository contains release artifacts for the Bobbie CLI runtime. It does
not contain the private Bobbie source code.

Documentation and harness/orchestration code live in the separate
`bobbie-harness` repository:

- `https://github.com/4014-Labs/bobbie-harness`

This repository now also includes a local copy of the current runtime contract
docs for release consumers.

See:

- [`docs/README.md`](docs/README.md)
- [`docs/shader_contract.md`](docs/shader_contract.md)
- [`docs/uniforms.md`](docs/uniforms.md)
- [`docs/cli_arguments.md`](docs/cli_arguments.md)

The shader contract docs include the current UV conventions. Bobbie provides a
runtime fragment input varying at `location = 0`, and fragment shaders may name
that input `u_uv`, `uv`, `v_uv`, `v_tex_coord`, or `tex_coord`.

The harness repo remains the main public documentation location for:

- install and usage guidance
- harness setup
- sandbox workflows
- provider/model integration notes

## Current Releases

### macOS (Apple Silicon)

- [`releases/v0.3.0/bobbie-v0.3.0-macos-arm64.tar.gz`](releases/v0.3.0/bobbie-v0.3.0-macos-arm64.tar.gz)

Contents:
- `bobbie` - Metal backend binary for macOS

Quick install:
```bash
wget https://github.com/4014-Labs/bobbie-releases/releases/download/v0.3.0/bobbie-v0.3.0-macos-arm64.tar.gz
tar xzf bobbie-v0.3.0-macos-arm64.tar.gz
chmod +x bobbie
./bobbie --help
```

### Linux (x86_64)

- [`releases/v0.2.4/bobbie-v0.2.4-linux-x86_64.tar.gz`](releases/v0.2.4/bobbie-v0.2.4-linux-x86_64.tar.gz)

Contents:
- `bin/bobbie`
- `LICENSE.md`
- `COMMERCIAL.md`
- `README.txt`

Quick install:
```bash
wget https://raw.githubusercontent.com/4014-Labs/bobbie-releases/main/releases/v0.2.4/bobbie-v0.2.4-linux-x86_64.tar.gz
```

## License

See:

- [`LICENSE.md`](LICENSE.md)
- [`COMMERCIAL.md`](COMMERCIAL.md)
