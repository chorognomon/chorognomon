# Thread: Wire format — follow pentabased's per-modality split, or unify?

## Status

Substantially clarified by reading charkha's `splice-wheel.md` verbatim (2026-04-26). Pentabased has a working hybrid; the chorognomon question is whether to follow it or go further.

## Question

Does chorognomon adopt pentabased's *per-modality* wire format (compact streaming bends for shuttle inputs, tagged-operation bends for splice inputs — *"different protocols, same document"*), or unify both modalities under a single extended-token alphabet that the loom parses into AST mutations?

## What we know now

Pentabased's working answer (from `charkha/design/splice-wheel.md`):

- **Shuttle bends** are fine-grained: a mode + a glyph (two petals, 10 bits). Compact, real-time-streaming-friendly. *"Like embroidery thread — many tiny bends per neem."*
- **Splice bends** are compound: an operation + an address + content (variable length). *"Like yarn — each bend carries a neem or phrase worth of material."*
- The loom doesn't care about bend size; both types are applied as atomic mutations. *"Different protocols, same document."*
- In phase 2, when the loom moves to tiraz, these become two API endpoints — different protocols, same document.

This is itself a hybrid (per-modality, not per-user-type), and it's already validated in pentabased.

## What's still open for chorognomon

Whether to extend further into a *unified* token alphabet — content + traversal + metastructure + scratch tags — that *both* modalities serialize into. This is a strict superset of pentabased's pattern: the per-modality split still exists at the user-facing level, but on the wire there's one grammar.

Trade-offs:

- *Stay per-modality (pentabased's pattern).* Each modality's wire matches its natural shape. Loom has two endpoints. Each side stays simple. Open-narration affordance needs explicit support per modality.
- *Unify into one token alphabet.* One parser, one error-recovery story. Open narration is just a tagged region in the same stream. The "nearby valid trees" suggester serves both single-author parse errors and multi-author merge conflicts. Requires GUI client to thin-serialize chord operations into tokens.

## Why it matters

- Determines the agent SDK shape (tool-use schema vs token grammar).
- Determines whether the GUI client is thick (knows ops, serializes them) or thin (emits tokens).
- Determines whether the loom needs a parser at all, and whether the "nearby valid trees" suggester is on the critical path.
- Affects how cherry-picking and merge-conflict resolution work — the unified view lets the same suggester handle parse errors and merge conflicts uniformly.

## What would resolve it

Pick one concrete card edit (e.g. "add a phrase to an existing verse" or "insert a new card linking to two existing cards") and write it two ways:

1. As pentabased-style per-modality: a sequence of shuttle bends (10-bit mode+glyph) for the typing path; a tagged-operation record for the splice path.
2. As a unified token stream: one alphabet, both modalities' inputs serialize into the same kind of stream.

Compare on:

- naturalness of emission from a Tactics Board chord
- naturalness of emission from an LLM
- server-side complexity of applying the bend
- error-handling story when the bend is malformed
- readability for a human reading a wire trace

If the unified version doesn't earn its complexity, default to pentabased's already-working per-modality pattern.

## Adjacent: pirn

Pentabased's splice-wheel for higher-level input (`pirn/`) is currently a stub. Building it would be the natural place to validate either choice in code; tracked separately as a possible side-quest if the worked example doesn't suffice. (Discussed in kickoff dialog 2026-04-26: agreed not to detour now.)
