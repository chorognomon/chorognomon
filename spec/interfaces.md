# Palaestra Interfaces

How clients interact with the Palaestra: the Tactics Board (human-facing GUI) and the MCP surface (agent-facing).

This is an early-draft spec. Sections marked **TBD** reflect open design questions tracked in `../threads/`.

## Purpose & scope

This document specifies the interaction model between the Palaestra and its clients. It covers:

- The asymmetric-clients principle and how human and agent clients differ.
- The wire format(s) and how content + structure + navigation travel between clients and server.
- The shape of human-side input (Tactics Board) and agent-side input (MCP).
- The promotion flows that move content from scratch into refined institutional memory.

It does *not* cover:

- The detailed document model — to live in `spec/document-model.md` (not yet drafted).
- Palaestra internal architecture (storage, indexing, replication).
- The Boot Room / Stoa graduation ritual specifics — see `../threads/functional-information.md`.
- The laurel-bestowal DSL — see `../threads/laurel-dsl.md`.

## 1. Asymmetric clients

> *Willful asymmetry is the soul of modernism.* (See `../threads/symmetry-breaking.md`.)

Carrying forward from pentabased: clients are split into **input-only** and **output-only** roles, independently swappable. A participant interacts via at least one of each. Because the roles are separable, a phone can be a wheel for a tabletop display, an agent can be a wheel for a human's display, and so on.

What chorognomon adds: input clients come in two granularities, lifted directly from pentabased's wheel taxonomy:

- **Shuttle-style** — sequential, character-of-thought-at-a-time, with persistent cursor state. Streaming input model.
- **Splice-style** — chunk-based, position-addressed (each operation specifies its target explicitly), no cursor state. Atomic-operation model.

The asymmetry is **granularity-of-input, not user-type.** Defaults route humans toward shuttle-style (their strengths: sequential drafting, cursor-aware editing, in-the-moment responsiveness) and agents toward splice-style (their strengths: holding wide context, addressing positions explicitly, atomic operations). But either modality is available to either kind of participant — a human can issue bulk edits via a splice-style interface (e.g. dictation), an agent can draft openly via a shuttle-style interface when "thinking out loud" is the goal.

The Palaestra's loom mediates between input streams and the canonical document, automatically adjusting any active shuttle indices when splice operations move document positions.

## 2. Document model in brief

Detail belongs in `spec/document-model.md` (TBD). The interface spec needs only the contract:

- The unit of identity is the **card.** Cards are addressable from anywhere. Each card has its own identity, provenance, and version history.
- A card holds an **extended-markdown AST** — a strict internal hierarchy (chorognomon's analog of pentabased's verse/phrase/neem/petal levels). The AST is the canonical form; markdown rendering is derivative.
- Cards link to other cards via **typed relationships** (e.g. `Equipment supports Campaign`, `Niche bordered_by Arena`, `Player belongs_to Team`). Inside a card: hierarchy. Between cards: graph.
- Cards may share a **background** — a structural skeleton (e.g. a "campaign retrospective template") that provides slots and shape; the card supplies the slot contents. (HyperCard reference: a background is a layer shared across many cards; each card adds card-specific content on top.)
- The grammar includes a **scratch tag** distinguishing scratch from durable content. Scratch nodes are first-class — addressable, persistent, citable — but rendered and treated differently. (See §7.)

**TBD:** whether typed relationships live as inline link-extensions, structured frontmatter, or distinct AST node types. The interface spec assumes "distinct AST node types" as the working assumption (so the same machinery handles content, structure, and links uniformly), but this is not pinned down. See `../threads/grammar-ontology-surface.md`.

## 3. Wire format

The Palaestra and its clients communicate via streams of **bends** (carrying forward pentabased's name, scoped to this layer — see `../threads/bend-collision.md`). A bend is one atomic instruction expressing intent to read, navigate, or mutate the document.

### Pentabased's precedent: wire format follows input modality

Pentabased already has a working answer worth following: **the wire format depends on the input modality, not on who's using it.**

- **Shuttle-style** input emits compact streaming bends (charkha's shuttle bends are 10-bit fixed-width — a mode plus a glyph). Many tiny bends per word; suited to real-time keystroke streaming.
- **Splice-style** input emits tagged-operation bends — operation + address + variable-length payload. Each bend is compound, carrying a neem-or-phrase worth of material.

Pentabased's framing: *"different protocols, same document."* Both bend types are accepted by the loom and applied as atomic AST mutations. The loom is the arbiter regardless of which protocol issued the bend.

This is the working assumption for chorognomon. Tactics Board input (defaulting to shuttle-style) emits streaming bends; MCP input (defaulting to splice-style) emits tagged-operation bends. Either client can elect the other modality and emit the corresponding wire format.

### Open: should chorognomon unify under a single extended-token alphabet?

The remaining wire-format question (`../threads/wire-format-choice.md`) is whether chorognomon goes further than pentabased and unifies both modalities under a single extended-token alphabet — content tokens + traversal tokens + metastructure tokens + scratch tags — with parsing (tree-sitter or similar) producing AST mutations and a *nearby-valid-trees* suggester handling parse failure as cherry-picking-at-different-scale.

Trade-offs:

- *Per-modality (pentabased's pattern).* Each modality's wire matches its natural shape; loom's API surface doubles but each side stays simple. Open narration needs explicit support per modality.
- *Unified token alphabet.* One parser, one error-recovery story; open narration is a tagged region in the same stream; the "nearby valid trees" engine serves both single-author parse errors and multi-author merge conflicts. Requires the GUI client to thin-serialize chord operations into tokens.

Pinning this down probably wants a concrete worked example: pick one card edit and write it both ways before committing. See the thread.

### What's stable across views

Independent of the choice above:

- Every bend carries **provenance** (who issued it, when, from which client, in which Stoa, on which card).
- Every bend is **content-addressed** once accepted — bends accumulate on the loom's append-only log; old states remain reachable.
- The branch axis of editing history is a **binary tree, not a linear stack** (carrying forward from charkha) — alternative paths coexist and are addressable, with explicit redo-left and redo-right at forks.

## 4. The human side: Tactics Board

The Tactics Board is the desktop GUI client. Modeled on the magnetic boards coaches use to walk through formations and set pieces.

### Display

- A **stack view** of cards. Cards are atomic units of viewing — one or a small grid at a time, not infinite scroll.
- The AST renders directly, parallel to markdown's visual conventions (headings, lists, emphasis), but the rendered surface is the AST view, not a serialized markdown rendering. Scratch-tagged regions render with reduced visual weight (dim, grayed, or in the margin).
- Multi-pane: the participant's own input thread is one pane; other live participants' threads are visible alongside (read-only views of in-progress work). Carrying forward pentabased's "every author's input visible to the group."

### Input

- **Shuttle-style by default.** A cursor (the shuttle) has a position and a scope. Scope levels generalize pentabased's petal/neem/phrase/verse to chorognomon's AST node levels (TBD precise vocabulary).
- **Vim-like momentary modes.** Command keys are *held*, not toggled — the mode lasts only as long as the key is down. No sticky-modal-state between operations.
- **Three insert principles** (carrying forward from pentabased):
  1. On insert, the shuttle takes the scope of the inserted thing.
  2. Insert direction depends on scope (≥ current scope → right; smaller than current → left/inside).
  3. Only an explicit *yank* removes content; bloom and loop only create.
- **Splice-style affordances available.** Card-level operations (move, link, replace whole card) and bulk edits use splice-style interfaces that don't depend on the shuttle.

### Output

- The Tactics Board is also a display — it receives panel renderings of the document state from the loom.
- Panels include affordances for navigation between cards, following typed relationships, and inspecting provenance / scratch / history.

## 5. The agent side: MCP

The Palaestra exposes Model Context Protocol endpoints so agents are first-class participants, not bots scraping a human UI.

### Read surface

- **Subnode addressability.** Read operations target specific AST nodes by path (e.g. `card:abc/verse:2/phrase:1`). The MCP returns the requested subtree plus its citable identity. This is the core read shape — agents fetch only what they need rather than whole cards or whole stacks. (Carrying forward charkha's `read_screed` mechanism: a parent screed returns its content *plus* metadata about its children, so the agent can decide which children to descend into rather than loading the entire document into context.)
- **Typed-link traversal.** From any node, follow typed links to related nodes. Returns are nodes, not URLs.
- **Provenance and scratch context** are part of the read result: agents see who wrote a node, when, and whether it's scratch or refined.

### Write surface

- **Splice-style by default.** Write operations are atomic, position-addressed bends — `{op, target, content}` — drawn from the splice-bend vocabulary (insert / replace / delete at the card and AST-node scopes; chorognomon's analogs of charkha's `insert_neem`, `insert_phrase`, `replace_*`, `delete_*`). No persistent cursor state.
- **Open-draft mode** for collaborative composition where atomicity is wrong (e.g. the agent wants other participants to watch the shape emerge before commit). In this mode, the agent's bends stream into a draft branch visible to others; an explicit `commit` bend folds the draft into the canonical AST.
- **Narration as a grammar feature.** The agent's reasoning lives inside the document, not in a side channel — but it lives inside `scratch` nodes, addressable and citable but distinguished from durable content. The renderer can hide, dim, or surface scratch on demand.

### Scout pattern

Scouts (per the project README) are a future agentic-stack feature. The interface contract:

- A scout's findings are produced as **scout-report cards** in the Palaestra, with provenance pointing back to the source nodes the scout traversed.
- Reports are *nodes*, not chat messages. They accumulate as functional information; they can be cited from other cards; they survive the scout that produced them.
- Other participants (human or agent) can subscribe to a scout's reports via standard typed-relationship reads.

## 6. Suggestions and cherry-picking

Pentabased treats two distinct things as "suggestion" — both useful, both kept in chorognomon:

- **Tooling suggestions** — autocomplete-style assistance for shuttle-wheel typing (charkha's cant-suggest mechanism: enter alone accepts a suggestion offered by an attached language model or LSP). Useful for shuttle-style input where the operator is composing one neem at a time; not useful for splice-style input which already emits whole phrases.
- **Error annotations** — read-only decorations on the document tree (type mismatches, malformed nodes). Useful to all input modalities as input to the operator's judgment, but not part of an accept/decline flow.

Chorognomon adds a third concept that is genuinely first-class:

- **Inter-participant suggestions** — proposed edits from one author (human or agent) to another's content. A suggestion is a first-class node — a draft branch attached to a target node, with provenance and a status (open / accepted / declined / superseded).
- **Anyone (human or agent) can suggest** on anyone's content, including their own draft work.
- **Acceptance is explicit.** The owner of the target (or a quorum, depending on the Stoa's rules — TBD) chooses to fold a suggestion into the canonical AST.

The "nearby valid trees" suggester (under the unified-token-alphabet wire-format option) connects parse-recovery and merge-conflict mechanics — same engine, two uses.

Inter-participant suggestions are the cherry-picking mechanic at the editing scale: every participant has a draft branch, the canonical AST is the merge of accepted suggestions, and acceptance is a deliberate ritual.

## 7. Promotion flows

Carrying forward the principle from `feedback_willful_asymmetry.md` and `project_scratch_first_class.md`: scratch is first-class, refined is distinct, and the path from one to the other is explicit and auditable.

Three promotion gates:

1. **Refine** — scratch → draft. The author (or a collaborator) marks scratch content as a candidate for the durable layer. The grammar tag flips; the node remains in place; provenance preserves the prior scratch state.
2. **Accept** — draft → canonical. Group-agreement weaving. A draft branch (whether from a single author's working copy or from a suggestion) is folded into the card's canonical AST. The merge is recorded as a bend with provenance.
3. **Bestow** — canonical → Boot Room. Laurel bestowal is the highest-stakes promotion: a campaign's canonical artifacts graduate into the persistent institutional-memory layer, where they become base-rate inputs for future coalitions. (Mechanics TBD, see `../threads/laurel-dsl.md`.)

Each promotion is a typed operation. Each is reversible in principle (retract a refine, decline a previously-accepted suggestion, demote from Boot Room) — the content-addressed log preserves prior states.

## 8. Open questions

Tracked in `../threads/`. Active questions for this spec:

- **Wire format unification** — follow pentabased's per-modality split, or unify both modalities under a single extended-token alphabet (`../threads/wire-format-choice.md`).
- **Grammar = ontology surface** — whether typed relationships are AST nodes, link extensions, or frontmatter (`../threads/grammar-ontology-surface.md`).
- **Cursor scope vocabulary** — chorognomon's analog of pentabased's petal/neem/phrase/verse for the shuttle's scope levels.
- **Backgrounds: schema objects vs UX pattern** — first-class shared structural skeletons, or convention over shared cards?
- **Affordances on cards** — explicit buttons (HyperTalk-style) or affordances inferred from typed relationships?
- **Suggestion quorum** — owner-only, group-quorum, or Stoa-configurable?
- **Promotion reversibility** — what does "retract" actually mean given append-only storage and content addressing?

## Carryforward notes

This spec carries forward from pentabased / charkha / tiraz:

- Wheels (input) / displays (output) split, independently swappable.
- Shuttle-wheel and splice-wheel granularities (the two-wheel-types pattern; granularity-of-input, not user-type).
- Per-modality wire format (compact streaming for shuttle bends; tagged operations for splice bends; *"different protocols, same document"*).
- Screed-as-persistence-unit factoring (each card here is the persistence unit).
- The `read_screed` pattern for context-budget-aware subnode reads (parent returns content plus child metadata; agent decides what to descend into).
- Append-by-default editing (only explicit yank removes; binary-tree branching undo with redo-left / redo-right at forks).
- Per-author input threads visible to all; group-agreement weaving.
- Tape's three insert principles (shuttle scope on insert; insert direction by relative scope; only yank removes).
- Tooling-style suggestions (LSP-flavored, cant-suggest accept) and error annotations as read-only tree decorations.

Where chorognomon diverges:

- Typed relationships, time, and provenance as first-class — the grammar carries them, not just adjacency.
- Scratch as a first-class grammar tag, with explicit promotion flows.
- **Inter-participant** suggestions as first-class artifacts (a third suggestion concept on top of pentabased's tooling-suggestions and error-annotations).
- Multi-document orchestration via the card graph (vs. pentabased's per-tapestry scope).
- HyperCard-flavored cards/stacks/backgrounds layered on top of the screed/AST substrate.
