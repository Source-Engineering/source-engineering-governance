# Source Engineering -- Governance Charter

> The discipline's governance. Maintainer roles, succession, decision authority, conflict resolution.

This charter is the constitution of the Source Engineering discipline. It defines who has authority to make which decisions, how the discipline grows, and how disagreements are resolved.

## 1. Maintainer roles

### Founding author

Emmanuel A. Otchere is the founding author of the discipline. As of Phase 1, the founding author holds final decision authority on all matters that are not delegated below. The founding author's role is documented in the discipline's history; succession is in section 4.

### Domain maintainers

Each aspect repository has a **domain maintainer** who is the named owner of that artefact. Domain maintainers approve PRs that affect their artefact. The domain maintainer is the first reviewer and the binding sign-off for changes within their scope.

Domain maintainers are listed in the README of each aspect repo. The list evolves as the discipline grows.

### Steering committee

When the discipline has external contributors, a **steering committee** of three to five members is established. The founding author chairs the committee initially; subsequent chairs are elected by the committee. The committee:

- Approves changes to the foundations repo (any change to a pillar, a principle, or a COTS component)
- Approves changes to the governance repo (any change to the change process itself)
- Resolves disputes that domain maintainers cannot resolve
- Approves the discipline's release policy

Until the steering committee is established, the founding author holds the committee's authority.

## 2. Decision authority by artefact

| Artefact class | Authority | Approval required |
|---|---|---|
| Pillar, principle, COTS component | Foundations repo domain maintainer + founding author | RFC + ADR; steering committee once established |
| Specification, model, architecture, blueprint | Aspect repo domain maintainer | PR review by domain maintainer + one additional reviewer |
| Reference implementation, conformance, tools | Aspect repo domain maintainer | PR review by domain maintainer + one additional reviewer |
| Case studies, practice, education | Aspect repo domain maintainer | PR review by domain maintainer |
| Research, experimental protocols | Research repo domain maintainer | PR review by domain maintainer |
| Governance (this repo) | Founding author | Founding author + one domain maintainer as second reviewer |
| Per-channel discipline rules | Domain maintainer for the bound channel | PR review |

## 3. The change process

The change process is described in detail in `CONTRIBUTING.md`. The summary:

1. **Idea.** A change starts as a discussion (in an issue, on the kanban board, or in a Discord thread). The proposer is the **champion**.
2. **RFC.** For foundational changes, the champion files an RFC under `RFC/`. The RFC describes the motivation, the proposed change, the alternatives considered, and the consequences.
3. **Review.** The RFC is open for review for a minimum of 14 days. The community and the domain maintainers comment. The champion updates the RFC in response.
4. **Decision.** The accepting authority approves or rejects the RFC. The decision is recorded as an ADR under `ADR/`.
5. **Implementation.** The accepted change is implemented as a PR. The PR references the ADR.
6. **Review and merge.** The PR is reviewed by the domain maintainer and merged.
7. **Reflection.** A reflection lands in the discipline's snapshot doc: what shipped, what fell short, which open question got answered.

Routine changes (typos, dependency bumps, formatting) skip the RFC step and follow the standard PR flow.

## 4. Succession

The founding author holds the authorities above until they delegate. Delegation is one-way: once delegated, an authority is held by the delegate. The founding author may withdraw delegation, but only with a public note in `CHANGELOG.md` and a 30-day notice.

When the founding author steps down, the steering committee takes over. The transition is documented as an ADR.

## 5. Conflict resolution

When two domain maintainers disagree and cannot resolve between themselves:

1. The disagreement is documented in an issue or a thread.
2. The steering committee (or the founding author until the committee is established) reviews.
3. The committee's decision is recorded as an ADR.
4. The decision is binding for the matter at hand but does not set precedent unless explicitly stated.

## 6. Release policy

The discipline does not yet have a release policy. It will be drafted as part of Phase 3 (Demonstrate), when the reference implementation and conformance suite make versioned releases meaningful. Until then, changes are continuous on `main` of each aspect repo.

## 7. Cardinal rules (binding on all contributors)

- **No decisions leading to creating an org or repo are logged on GitHub.** Repo descriptions state the artefact the repo owns, not the decision to create the repo.
- **No reference or tagging to the coding agent or another agent in any GitHub surface.** Commit messages and PR titles use the contributor's GitHub identity only.
- **Activity/identity refers to the user's GitHub id or name only.** All commits are authored by the contributor's own GitHub identity.

These rules are recorded per the discipline's cardinal-rule redaction (see `Source-Engineering/project-governance` commit history for the redaction pattern).

## 8. Amendment

This charter is amended via the change process (section 3) with the additional requirement that any amendment is approved by the founding author (until the steering committee is established) or by a two-thirds majority of the steering committee.
