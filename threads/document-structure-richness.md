# Thread: Richer document structure nodes beyond markdown reports

## Status

Raised by philetus end-of-kickoff (2026-04-26) to clarify outstanding card / typed-relationship questions in `spec/interfaces.md`. Carry into next session.

## Question

Alongside the 'report' style document structure (markdown / HTML / LaTeX) that a card holds today, what other structured container types should be first-class node kinds in chorognomon's grammar?

## philetus's sketch

Beyond cards-as-atoms-in-a-graph, the system probably wants richer structural kinds:

- **Graph nodes/edges as containers.** A graph (of nodes and edges) where each node — or each *edge* — can itself contain multiple cards. The graph isn't only a way to relate cards; it's a structural object that holds cards as content.
- **Tables with structured columns/rows.** Each cell potentially contains extended markdown — or a diagram, or another document node type entirely.
- **Spatial layouts** à la a Figma board space — child nodes positioned at relative 2D transforms within the board. Maybe higher dimensions too.

The common thread: a card isn't always the right container. Sometimes the container is itself the structure (a table, a graph, a spatial layout), with cards as the cells / nodes / objects inside.

## Why it matters

- Resolves (or sharpens) the §8 open question on card structure: cards aren't just AST + outgoing typed links; some cards *are* containers with their own structure-specific child semantics.
- Forces the document-grammar question (`grammar-ontology-surface.md`) — these structural kinds need to live somewhere in the grammar, with their own children-shape rules.
- Affects all three GUI view modes (`gui-view-modes.md`): each container type has its own natural rendering, and the view modes interact with them differently.

## What would resolve it

- Decide whether each structural kind is its own *card type* (a card *is* a table) or a *container kind* that *holds* cards (a table holds card-cells).
- For each kind, sketch: child-shape grammar, addressing scheme (how do you refer to a cell? a graph edge? a spatial position?), default rendering, default edit affordances.
- Decide which kinds ship in the v0 grammar vs. arrive later. Markdown report is obvious; the others can stage in.
