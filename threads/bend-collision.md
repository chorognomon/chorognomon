# Thread: *bend* vocabulary collision with pentabased

## Status

Likely a non-collision in practice — but worth keeping the note.

## Question

The word *bend* is used by both pentabased and chorognomon with different meanings. Real problem or apparent problem?

## What we know so far

- **pentabased** — *bend* = one input event from a wheel: a mode (bloom/loop/yank/swerve/cant) + one glyph. Atomic unit of input streamed in a thread.
- **chorognomon** — *bend* = the curving trajectory of a campaign as it converges on a niche. From the README: wordplay on "the arc of the universe bends toward justice" + "bend it like Beckham."

## philetus's read (kickoff dialog, 2026-04-25)

The two senses live in disjoint conceptual zones. Pentabased's `bend` belongs to the input/encoding layer (32-glyph alphabet, wheel input scheme) — exactly the layer chorognomon does *not* carry forward from pentabased. Chorognomon's `bend` belongs to the campaign-trajectory layer — a layer pentabased doesn't reach. So the word is reused but the contexts don't overlap.

## Why it matters (and where it might still bite)

- Inside each system the meaning is unambiguous from context.
- Becomes ambiguous if (a) we ever share a library across systems, (b) a single reader follows both projects, or (c) a chorognomon component ever talks directly to a pentabased component (e.g., a chorognomon scout writing to a pentabased loom).

## Resolution direction

Live with it. Add a vocabulary note to chorognomon's spec README pointing at the collision and the layer-disjointness so future readers aren't caught off-guard. Revisit if/when a shared-library scenario actually appears.
