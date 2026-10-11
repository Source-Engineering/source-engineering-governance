# Source Engineering -- Per-Channel Kanban Discipline

This file is the per-channel discipline rule for the Source Engineering discipline. It mirrors the pattern of the existing ECM rule for #enterprise-concepts-model and the nuel-otchere-com rule for the personal site channel.

## Rule

Every actionable item that arises on the Source Engineering Discord channel (parent channel 1526858674822185010) **must produce a Kanboard card** on the Source Engineering Kanboard project (id 13 on `http://192.168.50.160:7000`), with column state matching the item's lifecycle. The rule runs autonomously without prompting.

## Channel binding

- **Guild:** 1496909480783249428
- **Parent channel:** 1526858674822185010
- **GitHub repo:** https://github.com/Source-Engineering/source-engineering-governance (this repo; the discipline's primary working repo is [project-governance](https://github.com/Source-Engineering/project-governance), but the rule itself is canonical here)
- **Kanboard project:** http://192.168.50.160:7000/board/13 (project id 13, columns 52 Backlog / 53 Ready / 54 WIP / 55 Done)
- **Skill:** source-engineering-kanban-discipline

## Scope lock

The Source Engineering Kanboard project does **not** pull cards from channels outside its own scope. If a card emerges from a different channel (e.g. #enterprise-concepts-model, #world-semantic-foundation, #dea-catalog-*, #i-simulator, #astroforge, #systemone_stockapp, #github-technelabs), it routes to that channel's board instead.

Cross-cutting references (ECF framework, OpenDEAM vocabulary, ECM canon) remain admissible as inputs to Source Engineering cards; they do not transfer ownership of the card.

## Column states

| Column      | When an item lands here                                    |
|-------------|------------------------------------------------------------|
| Backlog     | identified but not yet sized, no owner picked              |
| Ready       | sized and accepted, owner assigned, no in-flight work      |
| WIP         | an active worker is on it                                  |
| Done        | the deliverable is shipped and accepted                    |
| Blocked     | the worker is stuck and waiting for human input            |

Kanboard 4-column standard (Backlog/Ready/Work in progress/Done). Mapping to the in-skill terminology above is column-name-aware.

## Card content

Every card description carries:

- Origin Discord thread id (e.g. 1558169004336742480) and message id where applicable
- The repo path: https://github.com/Source-Engineering/source-engineering-governance/<path>
- Commit SHAs (7-char) for any commit the card references
- Adjacent card refs: the umbrella card id for the workstream
- The board URL: http://192.168.50.160:7000/board/13

## Tag set (project-scoped)

- discipline
- framework
- blueprint
- tool
- example
- pillar-1 ... pillar-5
- layer-1 ... layer-4
- blog-anchor

## Recurring threads

When this Discord channel hosts recurring check-ins for the discipline, each recurring thread produces one parent card and N sub-tasks on the Kanboard project, with parents= links to the parent id.

## KB API credentials (record only - never commit live values)

The Kanboard REST API at `http://192.168.50.160:7000` is reached via JSON-RPC. The profile-level credentials at `~/.profile/keys.env` hold `GITHUB_TOKEN`; KB auth is the standard admin/admin form credentials used across the LAN. **Never commit KB credentials to this file.**

## Audit and reflection

When a Source Engineering slice completes, a reflection lands in the snapshot doc:

- What shipped
- What fell short
- Which open question got answered
- Which new open question got opened

The discipline grows by closing questions, not by accumulating epics.
