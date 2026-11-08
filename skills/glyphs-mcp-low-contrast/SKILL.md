---
name: glyphs-mcp-low-contrast
description: Draft a low-contrast version of Glyphs outlines by thickening only thin strokes on the background layer while locking thick stems, outer extrema, baseline, and existing corner/cap components. Use when the user asks for low contrast, reduced contrast, mono-linear-ish drafts, thickening thins, or background comparison drafts for contrast.
---

# Glyphs MCP low-contrast draft

Experimental construction aid. Put a **low-contrast draft on the background** of the target master layer so the designer can compare it with the live foreground. This does not invent a finished style; it is a measurable first pass to edit later.

## Core rules

- Read font, master, glyph, paths, and hints before any edit.
- Draft on **background only**. Leave foreground paths and foreground corner/cap hints unchanged unless the user explicitly asks to commit to foreground.
- Never auto-save the font.
- Reduce contrast by changing **only thin strokes / thin joins**. Lock thick stems and thick diagonals.
- Do **not** make the glyph taller or deeper: lock outer `maxY` / `minY` (and top bars / baseline terminals) unless the user asks otherwise.
- Prefer existing **corner and cap components** (`_corner.*`, `_cap.*` as `GSHint` CORNER/CAP). Copy them onto the background with the paths. Do not invent stem `_part.*` stand-ins when corners/caps already carry serifs and terminals.
- No whole-glyph Offset Curve / Transformations inflate. That thickens stems too.
- Default thin target for this draft: about **2× measured thin**, still well below the thick stem. If the user gives another ratio (for example 75% of stem), use that instead.
- After mutation, re-measure and report a before/after table. Focus the edit tab on the glyph and remind the user to enable **View → Show Background**.

## Principle

High contrast is mostly thick vs thin. A low-contrast draft keeps the thick skeleton and moves only the thin edges **apart along the local stroke normal**, so thickness rises without rewriting the design’s structure.

## Workflow

1. **Context**
   - `get_server_info` / `list_open_fonts` / `get_selected_font_and_master` as needed.
   - Resolve exact `font_index`, master (`id` + name), and glyph name(s).
   - Snapshot foreground paths, anchors, width, and all layer hints (especially corner/cap).

2. **Measure**
   - Identify thick vs thin with geometry, not guesswork:
     - opposite-edge min distance on known stroke pairs
     - vertical/horizontal cuts for bowls (`o`)
     - tip-segment length when the thin ends in a short cap edge
   - Avoid trusting raw ray samples alone on complex topology (`w`, `ampersand`).
   - Record: thick width(s), thin width(s), outer `maxY`/`minY`, corner/cap hint list.

3. **Classify editable vs locked**
   - **Locked:** thick stem/diagonal nodes; outer silhouette extrema; baseline nodes’ `y`; top-bar nodes; nodes that only belong to thick strokes.
   - **Editable:** thin stroke side nodes, thin joins/crotches, thin bowl tops/bottoms (usually the **inner** edge when outer extrema stay fixed), thin arm sides.
   - Re-copy foreground corner/cap hints onto background after path copy; keep their scales unless the user asks to fatten serifs/caps too.

4. **Materialize background**
   - Clear target master background.
   - Copy foreground paths into background.
   - Recreate CORNER/CAP hints bound to the copied nodes (same glyph name, node index, options, scale).
   - Do not leave mistaken stem-part components from older experiments.

5. **Thicken thins**
   - Compute `half = (target_thin - measured_thin) / 2` when both sides move, or full `delta` when only the inner edge moves against a locked outer.
   - Move opposite thin edges along a **stroke-perpendicular** normal oriented from one side toward the other.
   - Tip nodes: classify carefully. The short tip edge has one **inner** end and one **outer** end; they must move apart, not translate together.
   - Keep baseline `y` fixed when shifting bottom terminals; keep outer top `y` fixed when thickening upper thins.
   - Use `execute_code` / `execute_code_with_context` for this multi-step geometry; there is no dedicated MCP thicken-thin tool yet.

6. **Verify**
   - Foreground snapshot unchanged.
   - Background thick measurements ≈ before.
   - Background thin measurements ≈ target.
   - Outer bounds unchanged (unless intentionally edited).
   - Corner/cap hints present on background when they existed on foreground.
   - Report a compact table; leave document unsaved.

## Glyph pattern notes

These are starting heuristics from early Standon Super drafts; revise as the skill is edited.

| Pattern | Typical thins | Lock | Edit approach |
| --- | --- | --- | --- |
| `n`-like | Stem–arch crotch / thin arch join; keep corner/cap serifs | Vertical stems; arch top `y` | Thicken join along local normal; do not raise outer arch |
| `o`-like | Top/bottom arcs | Outer path; side stems | Move counter (inner) top down / bottom up |
| `w`-like | Alternating thin diagonals | Thick diagonals; top bars; baseline | Offset each thin diagonal pair on stroke normals |
| `ampersand`-like | Thin arm; upper-bowl top | Thick bowl sides; outer bounds; arm top bar | Arm sides + tip apart; inner upper-bowl top down |

## Safety stops

- Stop and ask if thick/thin classification is ambiguous.
- Stop if the only way to hit the target is to move locked outer extrema.
- Stop if corner/cap recreation would rebind to the wrong nodes after a topology change.
- Treat self-intersections, collapsed tips, or waist kinks as failed drafts; rebuild background from foreground and retry with corrected node roles.

## Out of scope (unless user asks)

- Committing the draft to foreground
- Saving the font
- Global stem equalization / Offset Curve
- Auto-scaling every corner/cap for “heavier serifs”
- Other masters than the requested target
- Spacing, kerning, or component renames

## Later edits

This skill is intentionally incomplete. When refining it, update:

- target ratios per master or family
- whether corner/cap scales should grow with thins
- candidate-session flow instead of direct background writes
- multi-glyph batch rules and proof strings

See [references/draft-lessons.md](references/draft-lessons.md) for concrete measurement lessons from the first Super drafts.
