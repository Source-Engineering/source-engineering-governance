# Contributing to the Source Engineering discipline

> How to propose, draft, and land a change. The discipline grows by closing questions, not by accumulating epics.

This document is the practical companion to `GOVERNANCE.md`. It walks the change process from the first idea to the merged change.

## The two paths

A change is either a **routine change** or a **foundational change**.

- **Routine change.** Typos, dependency bumps, formatting, single-line fixes, refactors that preserve behaviour. Standard PR flow.
- **Foundational change.** Anything that touches a pillar, a principle, a COTS component, a specification, a model, an architecture layer, a blueprint, a governance process, a maintainer role, or a release policy. Requires an RFC.

When in doubt, file an RFC. The cost of a small RFC is much smaller than the cost of a foundational change that was made without review.

## The change process

### Step 1: Idea

A change starts as a discussion. Where the discussion lives depends on the audience:

- **Public, asynchronous:** open a GitHub issue in the relevant aspect repo.
- **Private, exploratory:** start a Discord thread in the discipline's channel.
- **Internal tracking:** add a card to the discipline's kanban board.

The person who proposes the change is the **champion**. The champion drives the change through the process.

### Step 2: RFC (foundational changes only)

For foundational changes, the champion files an RFC. The RFC lives at `RFC/NNNN-short-slug.md`, where `NNNN` is the next available number and `slug` is a short identifier. The RFC template is at `RFC/0000-template.md`.

The RFC describes:

- The motivation. What problem is the change solving? What evidence shows the problem is real?
- The proposed change. What does the discipline look like after the change?
- The alternatives considered. What other approaches were considered, and why are they not as good?
- The consequences. What does the change break? What does it enable? What does it commit the discipline to?
- The migration path. If the change is breaking, how do existing implementations migrate?

### Step 3: Review

The RFC is open for review for a minimum of 14 days. Reviewers include:

- The domain maintainer of the artefact the change affects.
- The founding author (or a designated reviewer) if the change is foundational.
- Anyone in the community who has expertise in the area.

The champion updates the RFC in response to comments. Significant changes should be announced in the Discord channel.

### Step 4: Decision

The accepting authority (per `GOVERNANCE.md` section 2) approves or rejects the RFC.

If approved, the decision is recorded as an ADR at `ADR/NNNN-short-slug.md`. The ADR captures the decision, the rationale, the alternatives that were considered and rejected, and the consequences. The ADR template is at `ADR/0000-template.md`.

If rejected, the rejection is documented in the RFC's "Status" section with the reason.

### Step 5: Implementation

The accepted change is implemented as a PR. The PR:

- Targets the aspect repo whose artefact the change affects.
- References the ADR in its description (`Implements ADR-NNNN`).
- Has a commit message in the conventional-commits format (`feat:`, `fix:`, `refactor:`, `docs:`, `test:`, `chore:`).
- Passes any conformance checks that apply.

### Step 6: Review and merge

The PR is reviewed by the domain maintainer and one additional reviewer. Once both approve, the PR is merged to `main` of the aspect repo.

### Step 7: Reflection

When the slice is complete (the change is shipped and adopted), a reflection lands in the discipline's snapshot doc:

- What shipped
- What fell short
- Which open question got answered
- Which new open question got opened

The reflection is the discipline's way of closing the loop. Without a reflection, the change is not finished.

## Conventions

- **Language rules.** No en-dashes (U+2013), em-dashes (U+2014), or U+2E3B in GitHub artefacts. Use ASCII hyphens or sentence punctuation. No triple-semicolons in prose. The `;;` divider is preserved.
- **Cardinal rules.** No decisions leading to creating an org or repo are logged on GitHub. No reference or tagging to the coding agent or another agent in any GitHub surface. Activity/identity refers to the user's GitHub id or name only.
- **Commit messages.** Conventional-commits format. Cardinal author rule (the user is the author of every commit).
- **PR titles.** Same as the commit message's first line.

## Where to start

If you are new to the discipline, start with `Source-Engineering/source-engineering-foundations`. Read the manifesto, then the positioning matrix, then the terminology. Then pick a card on the discipline's kanban board and propose a change.
