# Low-contrast draft lessons (early Super passes)

Working notes from first background drafts on Standon Super. Use these as revision fuel for the skill, not as universal law.

## Shared mistakes

1. **Raising the thin arch** (`n`): moving outer arch nodes up increased height and missed the real thin, the stem–arch crotch (~21). Fix: lock outer top `y`; thicken the join only.
2. **Fake stem parts**: user asked for components meaning **corner/cap** (`_corner.serif.Lc`, `_cap.lc`), not `_part.n.stem.*`. Always inspect `layer.hints` first.
3. **Bad normals**: midpoint-to-midpoint vectors on diagonals can be nearly vertical and push nodes off-baseline without thickening. Use stroke-perpendicular normals, or the tip-edge direction for terminals.
4. **Tip node roles** (`ampersand` arm, `w` bottoms): translating both tip nodes with the same sign preserves tip width. One tip node is outer, one is inner; they must move apart.
5. **Measurement traps**: ray casting and “min distance between combined polys” can pick tip-cap edges or unrelated pairs. Prefer labeled opposite edges, cardinal cuts for bowls, and explicit tip-segment length when relevant.

## Approximate Super measurements (for comparison only)

| Glyph | Thick | Thin focus | Draft target used |
| --- | ---: | --- | --- |
| `n` | stems ~120 | crotch join ~21 | ~42 (2×), arch top 472 locked |
| `o` | sides ~128 | top/bottom ~28 | ~56 via inner-only move; outer locked |
| `w` | diagonals ~110 | thin diagonals ~27 | ~52–56; tops/baseline locked |
| `ampersand` | bowl sides ~100–107 | arm tip ~26; upper top ~39 | ~55–56; bounds −10…682 locked |

## Corner / cap checklist

- Read hints: type `CORNER` (16) / `CAP` (17), `name`, origin path/node, `options`, `scale`.
- After background path copy, recreate hints on matching copied nodes.
- Super `n` already had four `_corner.serif.Lc` and one `_cap.lc`. Super `o` / `w` / `ampersand` had none in the first draft file.

## Verification table template

| Metric | Before | After | Locked? |
| --- | ---: | ---: | --- |
| Thick stem/diagonal | | | yes |
| Thin stroke/join | | | no (raised) |
| Outer maxY / minY | | | yes |
| Corner/cap hints on bg | | | preserved |

Foreground unchanged: yes/no. Saved: no.
