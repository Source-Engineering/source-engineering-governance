# ADR 0001 -- Polyrepo organisation of the Source Engineering discipline

## Status

Accepted

## Date

2026-10-11

## Context

The discipline's intellectual scope is broader than any single repository can carry. A single repo would conflate the manifesto (a stable prose document) with the reference implementation (a moving code surface) with the conformance suite (a normative test surface) with the case studies (an empirical evidence surface). Conflation produces competing sources of truth, slows governance, and dilutes the signal of authority that the discipline's adopters (legal teams, financial institutions, regulators) need to see.

The original monorepo at `Source-Engineering/source-engineering` held all of these. The first six months of the discipline showed that the monorepo was the right starting point (one repo, one place to look) but the wrong steady state (one repo, too many artefacts fighting for the README's real estate).

The first essay (December 2025) named the five pillars; the one-year-later essay (October 2026) reframed the pillars into a four-layer stack and identified the judgment gap. The discipline's intellectual foundation is stable enough that the artefact ownership map is now well-defined and worth codifying.

## Decision

The discipline is published as a polyrepo under the `Source-Engineering` GitHub organisation. One authoritative home per artefact. Cross-repository references are links, never copies.

The full ownership map is the canonical table in `Source-Engineering/.github` (the org profile README) and `Source-Engineering/project-governance/README.md`. The map is:

- `Source-Engineering/.github` -- org profile README that renders on the org homepage
- `Source-Engineering/project-governance` -- the discipline's project-level governance (change process, ROADMAP, WBS epics, the per-channel kanban rule) for the governance home itself
- `Source-Engineering/source-engineering-governance` -- org-wide discipline governance: change process, ADRs, RFCs, maintainer responsibilities, release policy (this repo)
- `Source-Engineering/source-engineering-foundations` -- discipline definition, principles, terminology
- `Source-Engineering/source-engineering-research` -- research agenda, hypotheses, experimental protocols
- `Source-Engineering/source-engineering-model` -- canonical conceptual and data model
- `Source-Engineering/source-engineering-specifications` -- normative technical requirements; COTS spec lives under `specifications/cots/`
- `Source-Engineering/source-engineering-architecture` -- reference architecture, blueprint stack, per-layer interface contracts
- `Source-Engineering/source-engineering-reference-implementation` -- working reference code
- `Source-Engineering/source-engineering-conformance` -- conformance test suite
- `Source-Engineering/source-engineering-tools` -- reusable software utilities (CLI, visualizer, model-card-gen, SDKs)
- `Source-Engineering/source-engineering-case-studies` -- evaluated demonstrations, case studies
- `Source-Engineering/source-engineering-practice` -- practitioner guidance
- `Source-Engineering/source-engineering-education` -- curriculum and learning resources

## Consequences

Easier:

- A visitor to any one repo can tell what that repo is for within seconds (polyrepo signal of authority).
- Each artefact has its own governance, versioning, and adoption path.
- The discipline's adopters can subscribe to the repo that matters to them (e.g. a legal team subscribes to `source-engineering-case-studies` and `source-engineering-specifications`, not the reference implementation).
- The cardinal rules about identity and creation decisions are easier to enforce per-repo than across a monorepo.

Harder:

- Cross-repo references must be links, never copies. Drift is the main risk; the change process addresses it.
- The discipline's release process must accommodate the polyrepo (one release per artefact, with compatibility rules between them).
- A new contributor faces more repos to understand before they can land a change. The `terminology.md` in foundations and the org profile README mitigate this.

Commitments:

- The discipline commits to publishing a dependency graph between the aspect repos in `Source-Engineering/source-engineering-architecture` so cross-repo compatibility is documented.
- The discipline commits to one canonical home per artefact; new artefacts that do not have a home yet get one created via an RFC, not by being added to an existing repo's `docs/`.

## Alternatives considered

- **Monorepo.** Rejected: the visitor signal of authority is too weak; the cardinal rules are harder to enforce; the artefact owners are implicit rather than explicit.
- **Two-repo split (governance + everything else).** Rejected: too coarse; everything-else still conflates the manifesto with the reference code.
- **Five-repo split (governance, foundations, specifications, implementation, evidence).** Rejected: specifications and implementation are too coarse; specifications includes both the normative spec and the conformance suite, which need separate governance.
- **User-namespace project (Source-Engineering as a personal namespace, not an org).** Rejected: a discipline is not a side project; the org signals that the discipline is bigger than one person's repository.

## Migration path

Phase 0 (complete): the org description is set; the `.github` org-profile repo is created with the public introduction; the existing monorepo is renamed to `project-governance`; the per-channel discipline rule is updated; the GitHub Project v2 is created with the Stage and Priority fields.

Phase 1 (in progress): `source-engineering-foundations`, `source-engineering-governance`, `source-engineering-research` are created with their canonical content. The monorepo's `docs/00-manifesto.md`, `docs/05-positioning.md`, and `.github/discipline-rule.md` are deprecated with pointers to the new homes.

Phase 2: `source-engineering-model`, `source-engineering-specifications`, `source-engineering-architecture` are created with the discipline's formal artefacts.

Phase 3: `source-engineering-reference-implementation`, `source-engineering-conformance`, `source-engineering-tools` are created with working code.

Phase 4: `source-engineering-case-studies`, `source-engineering-practice`, `source-engineering-education` are created with the discipline's adoption surface.

At each phase, the monorepo loses the corresponding `docs/` content via a deprecation pointer (not a silent deletion).

## Notes

This ADR was authored as part of Phase 1 of the polyrepo rollout. It is the discipline's first decision record. The decision was informed by two upstream proposals (`inbox/SRC-ENG-000.md` and `inbox/SRC-ENG-000a.md` in the local work folder) and the prior in-thread Path Z proposal.
