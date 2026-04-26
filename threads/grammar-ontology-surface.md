# Thread: Document grammar = ontology surface

## Status

Deferred at philetus's request (kickoff dialog, 2026-04-26): *"i think your idea that 'Document grammar = ontology surface' is really interesting but also i think i need to explore it a bit more before i can make an informed judgement? lets come back to that!"*

## Question

If chorognomon's typed relationships (Equipment supports Campaign, Niche bordered_by Arena, etc.) live as distinct AST node types in the document grammar — not as inline link extensions, not as structured frontmatter — does that mean the grammar *is* the ontology surface? And if so, what does that imply for how the ontology evolves, who can extend it, and how the parser handles unknown types?

## What we know so far

- The interfaces spec (`spec/interfaces.md` §2) takes "distinct AST node types" as the working assumption for typed relationships.
- The unified-token-alphabet option in `wire-format-choice.md` doubles down on this — it treats traversal and metastructure tokens as part of the document grammar itself.
- If the grammar is the ontology, then introducing a new typed relationship is a grammar extension. That's a real cost (parser updates, migration story for old documents) but a real win (everything the system reasons about has a single source of truth).

## Why it matters

- Affects how scouts and other agents reason about ontology: do they query a catalog, or read the grammar definitions?
- Affects how Stoas and coalitions can introduce their own working vocabularies: open the grammar, fork it per Stoa, or constrain to a system-level closed set?
- Determines the storage and migration story when the ontology changes — content-addressed AST nodes referencing a now-deprecated type need a clear answer.

## Possible models

- **Closed.** The ontology is system-defined and locked. Predictable, easy to reason about; rigid in ways that may not survive contact with real coalitions.
- **Stoa-extensible.** Each Stoa can introduce its own typed-relationship vocabulary, scoped to that Stoa. New types can graduate to system-level via the Boot Room. Promising fit with the Stoa/Boot-Room architecture.
- **Open.** Anyone can introduce a type at any time. Maximum flexibility; risks ontology sprawl and incoherence.

## What would resolve it

- Sketch how a new typed-relationship gets introduced under each model. Compare developer/participant ergonomics, migration story, and consistency guarantees.
- Look at prior art: RDF/OWL conflate schema with data; SQL separates them sharply. Graph databases (Neo4j, JanusGraph) sit in the middle. Which side does chorognomon want to land on?
- Decide whether the ontology evolution itself is something the Boot Room records — i.e. is *the history of the ontology* a first-class kind of functional information?
