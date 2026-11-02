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


## 2026-10-11 (RFC-0001 in review)

### Added

- `RFC/0001-v0.2-floor-layer-2.md` -- the second foundational RFC. Proposes flooring Layer 2 (Runtime Validation) as the v0.2 floor. Open for review until 2026-10-25.

### Notes

- The v0.2 floor is the natural next step after v0.1 (Typed Model). The four-layer stack was defined in the 2026 reframing; v0.1 floored Layer 1 only.
- Reviewers are asked to comment on the motivation, the proposed change, the alternatives considered, the consequences, the migration path, and the open questions.

## [Unreleased]

### Changed

- RFC-0001 (v0.2 Floor: Layer 2) marked Implemented, with the landing evidence across the five repositories of the floor

### Changed

- RFC-0002 (recursive evidence chains) marked Implemented, with the landing evidence across the six repositories of the slice

### Added

- RFC-0003: the v0.4 floor proposal (Layer 3 Business Guardrails DSL): normative rule grammar, COTS schema v1.3.0 `decisions[*].guardrail` field, TC-L3 conformance cases, the Layer3 reference module, `se-trace check-guardrails`, handbook chapter 11, curriculum Unit 7

### Changed

- RFC-0003 (Layer 3 Business Guardrails DSL) marked Implemented, with the landing evidence across the six repositories of the floor
