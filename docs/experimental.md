# Experimental Snapshot

This branch tracks an in-progress Bobbie source snapshot for evaluation before a
public release artifact is cut.

Current source commits:

- `b55b26b` - `Keep Bobbie render contract errors schematic`
- `d64e5f9` - `Make strict bindings the only validation mode`

Current notes:

- strict binding validation is now the only validation mode
- Bobbie-owned validation and render contract failures use the schematic
  `ValidationError[...]` / `RenderError[...]` message shape
- the remaining docs cleanup still needs a cross-repo sweep in both `bobbie`
  and `bobbie-harness`

This branch is intentionally not a published release.
