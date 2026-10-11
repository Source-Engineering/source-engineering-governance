# Source Engineering -- Governance

> Change process, ADRs, RFCs, maintainer responsibilities, release policy.

This repository governs how the Source Engineering discipline evolves. It holds the change process, the maintainer responsibilities, the ADR and RFC catalogues, and the per-channel discipline rules that bind the local tooling to the discipline.

## What is in this repository

- `GOVERNANCE.md` -- the governance charter. Maintainer roles, succession, decision authority, conflict resolution.
- `CONTRIBUTING.md` -- how to engage with the discipline; the change process in detail.
- `CODE_OF_CONDUCT.md` -- community standards.
- `SECURITY.md` -- vulnerability disclosure policy.
- `discipline-rules/` -- the per-channel rules that bind the discipline's local tooling (kanban, snapshot, skills) to the discipline's process. Cardinal-rule clean: no agent identity, no creation-decision narrative.
- `ADR/` -- the architecture decision record catalogue. Each ADR captures one significant decision the discipline has made, with context, options, consequences, and the decision.
- `RFC/` -- the request-for-comments catalogue. RFCs are the public-facing change proposals; they graduate to ADRs when accepted.
- `CHANGELOG.md` -- the change log of the governance itself.

## Provenance

The change process in this repository is the discipline's first governance surface. It was established in Phase 1 of the discipline's polyrepo rollout, per [ADR-0001](ADR/0001-polyrepo-organisation.md). The original governance content lived in the monorepo at `Source-Engineering/project-governance`; it has been migrated here as the authoritative home.

## How to engage

- **Read the charter first.** Start at `GOVERNANCE.md`.
- **Then the change process.** `CONTRIBUTING.md` walks the path from idea to merged change.
- **Propose a change** by opening an issue or filing an RFC under `RFC/`. Foundational changes (anything that touches a pillar, a principle, or a COTS component) require an RFC; routine changes follow the standard PR flow.
- **Record a decision** as an ADR under `ADR/`. Use the template at `ADR/0000-template.md`.

## Organisation

The discipline is published as a set of repositories under the [Source-Engineering](https://github.com/Source-Engineering) GitHub organisation. One authoritative home per artefact:

| Repo | Owns |
|---|---|
| [Source-Engineering/.github](https://github.com/Source-Engineering/.github) | Org profile README |
| [Source-Engineering/project-governance](https://github.com/Source-Engineering/project-governance) | Project-level governance for `project-governance` itself (per-repo notes) |
| [Source-Engineering/source-engineering-governance](https://github.com/Source-Engineering/source-engineering-governance) (this repo) | Org-wide discipline governance: change process, ADRs, RFCs, maintainer responsibilities, release policy |
| [Source-Engineering/source-engineering-foundations](https://github.com/Source-Engineering/source-engineering-foundations) | Discipline definition, principles, terminology |
| [Source-Engineering/source-engineering-research](https://github.com/Source-Engineering/source-engineering-research) | Research agenda, hypotheses, experimental protocols |
| [Source-Engineering/source-engineering-model](https://github.com/Source-Engineering/source-engineering-model) | Canonical conceptual and data model |
| [Source-Engineering/source-engineering-specifications](https://github.com/Source-Engineering/source-engineering-specifications) | Normative technical requirements |
| [Source-Engineering/source-engineering-architecture](https://github.com/Source-Engineering/source-engineering-architecture) | Reference architecture, blueprint stack |
| [Source-Engineering/source-engineering-reference-implementation](https://github.com/Source-Engineering/source-engineering-reference-implementation) | Working reference code |
| [Source-Engineering/source-engineering-conformance](https://github.com/Source-Engineering/source-engineering-conformance) | Conformance test suite |
| [Source-Engineering/source-engineering-tools](https://github.com/Source-Engineering/source-engineering-tools) | Reusable software utilities |
| [Source-Engineering/source-engineering-case-studies](https://github.com/Source-Engineering/source-engineering-case-studies) | Evaluated demonstrations, case studies |
| [Source-Engineering/source-engineering-practice](https://github.com/Source-Engineering/source-engineering-practice) | Practitioner guidance |
| [Source-Engineering/source-engineering-education](https://github.com/Source-Engineering/source-engineering-education) | Curriculum and learning resources |

## Status

This is the governance slice (Phase 1). The charter, the change process, and the per-channel discipline rules are stable. The ADR and RFC catalogues are seeded with this initial set; they grow as the discipline grows.

## License

Apache-2.0. See [LICENSE](LICENSE).
