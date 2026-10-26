# Changelog

All notable changes to the Source Engineering governance repo are recorded here. The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

## 2026-10-11

### Added

- `GOVERNANCE.md` -- the governance charter. Maintainer roles, succession, decision authority, conflict resolution.
- `CONTRIBUTING.md` -- the change process. Idea -> RFC -> review -> decision -> implementation -> review and merge -> reflection.
- `CODE_OF_CONDUCT.md` -- community standards. Adapted from the Contributor Covenant, version 1.4.
- `SECURITY.md` -- vulnerability disclosure policy.
- `discipline-rules/kanban-discipline.md` -- the per-channel kanban discipline rule, migrated from the monorepo.
- `ADR/0000-template.md` -- the ADR template.
- `ADR/0001-polyrepo-organisation.md` -- the first ADR, capturing the polyrepo organisation decision.
- `RFC/0000-template.md` -- the RFC template.
- `CHANGELOG.md` -- this file.

### Notes

- The per-channel kanban discipline rule was previously held in `Source-Engineering/project-governance/.github/discipline-rule.md`. It has been migrated here as the canonical home; the monorepo's copy is deprecated with a pointer.


## 2026-10-11 (RFC-0002 in review)

### Added

- `RFC/0002-recursive-evidence-chains.md` -- the third foundational RFC. Closes the case study's open question Q3.2 (recursive evidence chains: when the COTS's evidence is itself a model-generated artefact, the COTS does not increase trust). Proposes: a `source_type` field on every EvidenceItem, a `chain_termination` policy on every COTS, and a `bounded_recursion` default with a recommended bound of 1. Adds 4 canonical conformance test cases (TC-RC-01..04). Open for review until 2026-10-25.

### Notes

- RFC-0002 is interdependent with RFC-0001 (the v0.2 floor). RFC-0002 grounds RFC-0001's Layer 2 contract. The discipline's review may sequence the two acceptances.
