# RFC 0005 -- Layer 4 Audit Trail Floor

> The discipline's fifth foundational RFC. Floors Layer 4 (Audit Trail) as a shipped vertical slice: an append-only store in the reference implementation, TC-L4 conformance cases, an `se-trace audit` replay command, and practitioner guidance.

## Status

In Review

## Champion

emmanuel-a-otchere

## Date opened

2026-11-02

## Date closes

2026-11-16 (minimum 14 days from opening)

## Motivation

The four-layer stack names Layer 4 (Audit Trail) as the layer that persists who saw what response, when, with what evidence, and what they did with it. The Layer 4 contract (`source-engineering-architecture/layer-contracts/layer-4-audit-trail.md`) already specifies the guarantees: append-only history, lifecycle integrity, domain-declared retention, re-runnability. But the reference implementation has `layer1`, `layer2`, and `layer3` directories and no `layer4`; the audit history on a DecisionRecord is a static array the producer fills once, and nothing in the repos can replay a decision's lifecycle or verify append-only-ness beyond time ordering. The Advancement Plan's Phase 4 names exactly this gap and lists "RFC-0004: audit-trail persistence contract" as its first item (numbering collision noted; this RFC takes 0005 to avoid clashing with the accepted context-graph RFC-0004).

The gap is not cosmetic. The discipline's terminology defines the audit trail as "the substrate the discipline uses to support appeals, re-runs, and cross-domain comparison." Appeals and re-runs are feedback surfaces the COTS already exposes; without a Layer 4 that actually records and replays them, those surfaces are promises, not mechanisms. This is the last layer without a shipped floor, and the Advancement Plan's acceptance criterion "all four layers floored" depends on it.

## Proposed change

Floor Layer 4 as one vertical slice, per the Layer 4 contract:

- **Reference implementation (`source-engineering-reference-implementation`).** New `src/layer4/` module:
  - `AuditStore` interface: `append(entry)`, `entriesFor(decisionId)`, `all()`. JSONL append-only file backend first (`JsonlAuditStore`), per the Advancement Plan; the interface allows Postgres or immutable ledgers to plug in later.
  - `Layer4` class implementing the contract's accept/produce: takes a post-Layer-3 DecisionRecord plus change events (appeal, rerun, correction, escalation, supersession, source update), appends an `AuditEntry` to the store, and mirrors it into the DecisionRecord's `audit_history`.
  - Guarantees enforced in code: append-only (the store rejects any operation that would rewrite or delete an entry; `AuditStoreError` with reason `audit-store-not-append-only`), lifecycle integrity (a source update that affects a decision appends a `corrected` entry), retention policy declared by the domain (`retention-policy-missing` error when absent), re-runnable (a rerun appends a `rerun` entry linked to the original decision id).
  - `replay(decisionId)`: reconstructs the decision's full lifecycle from the store alone -- the discipline's signature end-to-end test.
- **Conformance (`source-engineering-conformance`).** New `src/contracts/layer4-integrity.ts` implementing TC-L4-01..05, wired into the runner:
  - TC-L4-01: every decision's `audit_history` starts with `created` and every entry has id/action/performed_by/performed_at (extends TC-LI-01 at the Layer 4 level).
  - TC-L4-02: `audit_history` is append-only-verifiable: monotonic time order AND no duplicate entry ids.
  - TC-L4-03: every non-`created` action references a target decision that exists in the COTS (no dangling lifecycle events).
  - TC-L4-04: a `rerun` entry's notes carry the original decision id; a `superseded` decision is paired with the superseding decision's id.
  - TC-L4-05: legacy COTS with a minimal `created`-only history passes with warning `legacy-audit` (pre-RFC-0005 producers).
  - Plus fixtures: `l4-conformant.json`, `l4-non-append-only.json`, `l4-dangling-event.json`, `l4-rerun-unlinked.json`, `l4-legacy.json`.
- **Tools (`source-engineering-tools`).** `se-trace audit <file> [decision-id]` subcommand: prints the full audit trail of every decision (or one decision), marking append-only violations; exit 1 on TC-L4-02/03/04 violations.
- **Specifications (`source-engineering-specifications`).** `cots/README.md` Part 3 gains the Layer 4 note: the audit-history field semantics (append-only, lifecycle actions, rerun linkage) and the pointer to TC-L4. `compatibility-test-suite.md` gains TC-L4-01..05. No schema change; the fields exist, the semantics are now normative.
- **Architecture (`source-engineering-architecture`).** The Layer 4 contract gains a "Shipped as" note pointing at the ref-impl module and TC-L4 cases.
- **Practice (`source-engineering-practice`).** Handbook chapter 12: auditing a decision from its audit trail; the five audit questions against a replayed lifecycle.
- **Education (`source-engineering-education`).** Curriculum Unit 8 (audit trail replay) and glossary touch-up.
- **Research agenda.** Q-F.3-adjacent: the audit trail's role in verifying COTS faithfulness gets a protocol pointer.

The change is **additive**: no COTS schema field changes; existing COTSs remain valid, with TC-L4-05's `legacy-audit` warning marking pre-RFC-0005 producers.

## Alternatives considered

- **Store the audit trail only inside the COTS (audit_history array), no separate store.** Rejected: the contract requires a persistent store independent of any single COTS (cross-decision queries, re-runs producing new DecisionRecords linked via the trail). The array stays as the per-decision mirror; the store is the substrate.
- **Require tamper-evidence (hash chaining) at this floor.** Rejected for v0.x: the contract explicitly does not require it; the domain can add it. The JSONL backend writes a running SHA-256 chain per file so a tamper-evident backend can build on it, but conformance does not demand it.
- **Defer Layer 4 until Phase 5 (evidence and adoption).** Rejected: the Advancement Plan sequences Phase 4 before Phase 5, and "all four layers floored" is an acceptance criterion. Appeals and re-runs are already promised in the COTS's feedback surfaces; shipping those promises is the point.
- **Use a database backend first.** Rejected: JSONL-first keeps the reference implementation dependency-free and diff-able; the `AuditStore` interface is the seam for Postgres/ledgers.

## Consequences

Easier:

- All four layers have a shipped floor; the discipline matches its own name and the Advancement Plan's acceptance criterion.
- Appeals, re-runs, and corrections become mechanisms, not promises.
- The signature end-to-end test (reconstruct any decision from COTS + audit trail) becomes runnable.
- Conformance can verify append-only-ness structurally, not just by time ordering.

Harder:

- The ref-impl gains a module with an I/O seam; its tests grow from ~3 files to 4.
- The conformance suite gains 5 test cases and 5 fixtures.
- The tools CLI gains a subcommand; the per-decision mirror vs. store distinction must be documented so producers do not double-append.

Commitments:

- The discipline commits to append-only as a hard guarantee (store-level rejection, not just convention).
- The discipline commits to JSONL-first with a storage interface; no default retention policy is imposed (domain-declared, per the contract).
- The discipline commits to TC-L4 as the normative check that a COTS's audit trail is lifecycle-complete, not merely present.

## Migration path

Additive; no migration for existing COTSs.

1. **Stage 1.** Land this RFC; add the Layer 4 note to `cots/README.md` and TC-L4-01..05 to the test-suite doc (specifications).
2. **Stage 2.** Implement `src/layer4/` in the reference implementation with tests (append-only rejection, rerun linkage, replay).
3. **Stage 3.** Implement TC-L4 validators + fixtures in conformance; wire into the runner.
4. **Stage 4.** Add `se-trace audit` to tools with tests.
5. **Stage 5.** Architecture "Shipped as" note; handbook chapter 12; curriculum Unit 8; research-agenda pointer.
6. **Stage 6.** Cut the v0.4 release per the Advancement Plan's tagging rule (Layer 4 completes the stack).

## Open questions

- **Should the store live in the same process as the producer, or be a separate service?** Current proposal: same process for the reference implementation, interface-separated for real deployments.
- **Does `corrected` require the source-update linkage, or is a notes field enough at v0.x?** Current proposal: notes must carry the affecting source id; TC-L4-03 checks resolvability.
- **Should TC-L4-02 detect reordering (time-monotonic but reordered) via the running hash chain, or is id-uniqueness + time order enough?** Current proposal: id-uniqueness + monotonic time; hash chain is a backend property, not a conformance requirement.
- **RFC-0005 in the Advancement Plan's Phase 4 refers to privacy/redaction (GDPR).** Current proposal: that remains a separate future RFC; this RFC is the persistence floor only.

## Notes

This RFC floors the last unfloored layer of the four-layer stack. It follows the RFC-0001/0002/0003 pattern (RFC first, vertical slice across spec, ref-impl, conformance, tools, practice, education) and the Advancement Plan's Phase 4. The numbering note: the Advancement Plan's Phase 4 text says "RFC-0004: audit-trail persistence contract"; that number was taken by the context-graph RFC, so this RFC is 0005 and the epic text should be corrected in the same change.
