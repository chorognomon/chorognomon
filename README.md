# Chorognomon

*A space for humans and agents to read the field together — and decide where to play.*

Chorognomon (χωρογνώμων, roughly *kor-og-NO-mon*) is a document management system designed from the ground up for collaboration between humans and AI agents. Its purpose is to help people and agents jointly interpret the structure of design spaces, identify niches that are open now or are about to open, and form coalitions around campaigns to bend into them.

This README is a vision document, used to bootstrap further discussion — much of it eventually inside Chorognomon itself.

## The metaphor

The name borrows from Thomas Müller's self-described football position: *raumdeuter*, "space interpreter" — a player whose job is not to be where the action is, but to read where space is opening and arrive there before anyone else realizes it should have been theirs.

Translated into classical Greek: **χώρα** (*chōra*, space / field / region) + **γνώμων** (*gnōmōn*, one who knows or judges; also the pointer of a sundial, which interprets a field of light into a reading). A chorognomon, then, is one who reads the field.

The classical framing is not incidental. The project is partly inspired by Plato and Aristotle's habit of treating inquiry as a *collaborative* practice — symposia, dialogues, peripatetic walks. Chorognomon is for the kind of work that benefits from a porch full of friends arguing toward something.

## What it does

Targets, not features. Status is early.

- Ingests documents, models, sketches, and notes describing some design space of interest.
- Maintains rich semantic structure over them — not just embeddings but typed relationships, time, and provenance (who said what, human or agent).
- Lets humans browse, annotate, and reorganize the space through a desktop-style client.
- Exposes the same store to AI agents over the Model Context Protocol, so agents can read, reason, and contribute as first-class participants.
- Helps coalitions form: surfaces overlapping interests across people and agents and proposes campaigns they can ratify together.
- Tracks campaigns from declaration through outcome, with explicit retrospective evaluation.

## Architecture

Three components, named for clarity rather than literal anatomy.

### Palaestra — the backend

Named for the ancient Greek wrestling-school and training ground (Plato set several dialogues in one). The Palaestra is where preparation, conditioning, and shared knowledge live. It holds the documents, the relationships, the timelines, and the API surface everything else connects through. It speaks two protocols natively: a GUI-facing protocol for human clients, and MCP for agents.

Inside the Palaestra:

- **Boot Room** — the persistent institutional memory layer. Borrowed from Liverpool FC's legendary Boot Room of the 1960s–80s, where the coaching staff distilled tactical wisdom across generations of teams. Here it's where retrospective lessons, earned laurels, and base-rate patterns are stored and made available to future coalitions.
- **Stoa** — scoped workspaces, one per coalition or campaign. Named for the colonnade where the Stoics gathered to argue. Each Stoa is an active discourse zone for a specific group working on a specific region of a field.

### Tactics Board — the GUI client

The desktop application humans use to drag, draw, annotate, and plan. Modeled on the magnetic boards coaches use to walk through formations and set pieces. Where the Palaestra is the substrate, the Tactics Board is where humans actually *think* about it.

### Scout — the agentic stack (future work)

The agent-side counterpart to the Tactics Board. A scout's job in football is to range over territory, notice what others miss, and report back with recommendations — same here. The Scout reads the Palaestra through MCP, contributes back through MCP, and serves as the agentic half of any human–agent coalition. Planned to include a custom KV cache that operates on document nodes from the Palaestra rather than raw token streams — research-y, lives further out on the roadmap.

## Vocabulary

Chorognomon talks about its analytical objects with a deliberately mixed bag of sports terms (the field of analysis, a campaign's roster, the puck-line below). The mixing is intentional — pure-soccer metaphors slide toward a register the project would rather avoid, and the slight cross-sport vibe keeps things curious rather than tribal.

- **Field** / **Pitch** — the design space under analysis.
- **Arena** — a specific scoped region of a field.
- **Team** — a coalition of humans and agents working together.
- **Players** — individual participants, human or agent.
- **Equipment** — the tools, models, datasets, or strategies a team can deploy.
- **Campaign** — a planned, declared effort by a team to bend into a niche.
- **Niche** — an opportunity in the field; an opening a team can bend into.
- **Bend** — the curving motion of a campaign as it converges on a niche; the trajectory of skilled force finding its target. Wordplay yoking "the arc of the universe bends toward justice" with "bend it like Beckham" — the ball coming around the wall and into the top corner. Colloquially, a campaign that successfully reached its niche *had a good bend on it.*
- **Laurel** — a competitive goal that can be earned by successfully bending into a niche. See below.

## Laurels

A laurel is a goal a team can declare and, eventually, earn. Each laurel is personified as a Greek deity with a specific epithet — and the epithet matters as much as the deity. The character of the laurel tells you what *kind* of victory is being sought, which is informative about how the campaign should be played. An Athena Nike campaign is run differently from a Dionysus Eleuthereus campaign.

Initial roster, extensible:

- **Athena Nike** — victory through wisdom and clear sight. Strategic clarity, sound judgment, the right move at the right moment.
- **Hermes Logios** — victory through eloquence and cunning. Framing, narrative, the deal that closes because it was *spoken* well.
- **Apollo Mousagetes** — victory through art and inspiration. Aesthetic excellence, the campaign that wins because the work itself is beautiful.
- **Hephaestus Kallitechnes** — victory through craft. Better tools, better engineering, technical excellence as the moat.
- **Demeter Thesmophoros** — victory through cultivation. Slow, structural, infrastructural — the laurel for campaigns that win by patiently changing the substrate.
- **Artemis Hegemone** — victory through leadership of a band. Coalition-building; the laurel for teams whose strength is the team itself.
- **Dionysus Eleuthereus** — victory through liberation or disruption. Breaking a status quo, opening space others can move into.
- **Poseidon Asphalios** — victory through endurance and stability. Outlasting, holding ground; the laurel for campaigns that win by not losing.

Laurels are not assigned — they are *declared* at campaign launch and *bestowed* retrospectively by the system when the campaign's stated criteria are met. The accumulated record of bestowed laurels lives in the Boot Room and feeds back into how future coalitions reason about analogous niches.

## Scope and roadmap

Initial focus is on **current and very near-future niches** — the interpolation problem, where signals are observable now and validation loops are short. The architecture aims to leave room for **future niches** — the extrapolation problem, where the system would need explicit base-rate libraries and trajectory-aware retrieval modes — but that work comes later.

> "Skate to where the puck is going to be, not where it has been."
> — attributed to Walter Gretzky, repeated by his son Wayne

The Gretzky line names the long-horizon goal. Reading the present field well is the prerequisite for reading future fields at all.

## A note on the name

*Chorognomon* is the formal spelling, following classical transliteration conventions. English speakers around the Salish Sea (and elsewhere) will commonly pronounce the *ch-* as /tʃ/ rather than /k/; this is fine. The project does not stand on its pronunciation.

## Status

Early days. Component repos to follow.
