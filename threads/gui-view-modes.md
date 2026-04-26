# Thread: Tactics Board view modes (stream / mat / blinder) with margins and over-the-shoulder

## Status

Raised by philetus end-of-kickoff (2026-04-26). Carry into next session — this is the level of UX detail that probably wants to land in `spec/tactics-board.md` (not yet drafted) once it firms up.

## Question

What GUI view modes does the Tactics Board need, how do focus / scratch / yank-stack land in each, and how does collaborator awareness work?

## philetus's sketch

A unifying frame: every view has a **focus area** (where active editing happens) and **margins** (where scratch space and yank stacks live). What changes across modes is how much of the screen the focus claims and how the cards are arranged.

### Stream view

Like charkha's TUI: a one-dimensional 'frame' that is a *dynamic perspective* over the full card space, plus 'focus' frames each holding one (or a few) cards being actively edited, plus scratch in the margins.

Chorognomon-specific twist: each participant has their *own* full stream view. Collaborators can "look over the shoulder" of a teammate — see what their current frame selection is, see their yank stack, make scratch comments and suggestions there, and **promote things back to their own space.** Over-the-shoulder is the concrete mechanism for collaborator awareness without forced turn-taking.

### Mat view (also: table / board?)

A 2D arrangement of cards — for example, a spreadsheet or table of information within a paper.

In this view the focus is significantly expanded; other participants' focus + scratch + stack space is reduced. They can still switch to an over-the-shoulder view of a collaborator (and vice versa).

### Blinder view

Focus takes up the entire GUI window. Either multiple cards or expanded details within a single card (especially diagrams / raster images / video frames / etc.).

Over-the-shoulder still accessible; modal scratch space available alongside.

## Common mechanics across views

- **Margins hold scratch and yank stacks** (carrying forward charkha's right-edge yank scratch buffer concept; chorognomon makes scratch and yank stacks visible-but-distinguished).
- **Over-the-shoulder is a first-class action**: jump into a view of a collaborator's space; see their frame, their scratch, their yank stack; comment/suggest into their scratch; promote things back into your own space.
- **Modal scratch space** stays available even when focus takes the whole window (blinder mode).

## Why it matters

- Concretizes the "every participant's input visible to the group" principle (carried forward from pentabased) — over-the-shoulder is the *mechanism*, not just the value.
- Connects the suggestions section of `spec/interfaces.md` §6 to a UX surface: inter-participant suggestions get composed by leaving notes in someone's scratch from over-the-shoulder.
- Suggests scratch and yank stacks aren't just grammar tags but *spatial* features of the GUI (margins). Worth checking that the grammar tags compose cleanly with the spatial rendering.
- Three view modes give different answers to the cursor-scope-vocabulary TBD: stream wants fine scopes, mat wants cell-level, blinder wants whole-card.

## What would resolve it

- Pick one card edit and walk through what it looks like in all three views.
- Sketch the over-the-shoulder protocol: what state is exposed to the observer? Is the collaborator notified? Are scratch comments left over-the-shoulder addressable in the observer's own space, the observed's space, or a shared layer?
- Decide if there are other modes (mobile? AR? agent-only?) and how the view-mode set extends.
- Sketch the relationship between view modes and the richer document structure kinds (`document-structure-richness.md`) — a graph node may want its own preferred view; a table cell wants to expand into blinder when edited.
