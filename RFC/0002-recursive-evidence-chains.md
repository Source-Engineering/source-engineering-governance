# RFC 0002 -- Recursive Evidence Chains

> The discipline's second foundational RFC. Defines how a COTS handles evidence that is itself the output of another model.

## Status

Implemented. Landed as a vertical slice: COTS schema v1.2.0 fields and TC-RC test cases in `Source-Engineering/source-engineering-specifications`; `source_type`/`chain_termination` types and Layer 2 recursive-chain flagging in `Source-Engineering/source-engineering-reference-implementation`; TC-RC-01..05 and TC-SEM-01..03 validators in `Source-Engineering/source-engineering-conformance`; the `se-trace recursive` subcommand in `Source-Engineering/source-engineering-tools`; handbook chapter 10 in `Source-Engineering/source-engineering-practice`; curriculum Unit 6 in `Source-Engineering/source-engineering-education`. See the CHANGELOG in each repository.

## Champion

Emmanuel A. Otchere

## Date opened

2026-10-11

## Date closes

2026-10-25 (minimum 14 days from opening)

## Motivation

The discipline's first case study (Source-Engineering/source-engineering-case-studies/2026-legal-summarization/) confirmed the discipline's headline hypothesis (COTS-on reduces hesitation factor) but surfaced a second-order finding: **when the COTS's evidence is itself a model-generated artefact, the COTS does not increase trust.**

Concretely: a COTS-bearing system that summarises a legal document by citing its own intermediate reasoning (the model's own paragraph-level extraction, the model's own source-vs-claim classification, the model's own weighting) carries a COTS that looks structurally valid but is, in effect, citing itself. The hesitation factor does not close; the user is back to "take the summary on faith" because the evidence chain resolves to the model, not to a human-readable source.

This is the **recursive evidence chain** problem. It surfaced in the case study as open question Q3.2. The case study's own discussion:

> When the COTS did not help. When the COTS's evidence was itself a model-generated artefact (e.g. an LLM's intermediate reasoning), the COTS did not increase trust. The discipline's open question Q3.2 ("recursive evidence chains") surfaced here.

The discipline cannot ship v0.2 without addressing Q3.2. The Layer 2 contract (the v0.2 floor proposed in RFC-0001) assumes that evidence chains resolve to sources the user can audit; without a Q3.2 resolution, the Layer 2 contract is incomplete.

## Proposed change

The discipline adopts the following position on recursive evidence chains:

- **Definition.** A recursive evidence chain is a chain in which at least one evidence item is itself the output of a model (not a human-authored or human-curated source). The chain's terminal items are model outputs, not human-readable sources.
- **Disclosure.** A COTS MUST flag any evidence item that is itself a model output, in the `EvidenceItem.source_type` field (a new field; values: `human-authored`, `human-curated`, `model-generated`, `mixed`).
- **Chain termination.** A COTS MUST declare its chain termination policy in the `Cots.evidence.chain_termination` field (a new field; values: `terminal` (all chains resolve to human-authored sources), `bounded_recursion` (chains may include up to N levels of model outputs, declared per-COTS), `unbounded_recursion` (chains may include any depth of model outputs).
- **Default policy.** The discipline's default is `terminal`. A COTS that allows bounded recursion MUST declare its bound (max levels of model output in any chain) in the `bounded_recursion.max_levels` field. The bound is recommended to be 1; bounds greater than 1 are permitted but flagged for the user.
- **Conformance test cases.** A new set of canonical test cases (TC-RC-01..N) is added to the compatibility test suite and implemented in the conformance repo. The test cases cover:
  - TC-RC-01: a COTS with a recursive evidence chain MUST flag the recursive item.
  - TC-RC-02: a COTS with bounded recursion MUST declare the bound.
  - TC-RC-03: a COTS with unbounded recursion MUST be flagged in the conformance suite's warning set.
  - TC-RC-04: a COTS that violates its declared chain termination policy MUST fail validation.
- **Reference implementation.** The reference implementation in Source-Engineering/source-engineering-reference-implementation/ adds `chain_termination` and `source_type` schema fields to the COTS type; the `Layer2` module flags recursive chains.
- **Tools.** The `se-trace` CLI adds a `se-trace recursive <cots>` subcommand that walks the evidence chains and surfaces any recursive items.
- **Practice.** A new chapter in the practice handbook covers how a reviewer reads a COTS with recursive evidence chains.
- **Education.** A new section in the education curriculum covers recursive evidence chains.

The change is **forward-compatible** at the COTS level. A v0.1 COTS is a v0.2 COTS with the new fields absent; the conformance suite accepts v0.1 COTSs without the new fields but flags them as "legacy" in the warning set.

## Alternatives considered

- **Reject recursive evidence chains entirely.** Rejected. Some legitimate use cases (e.g. multi-document synthesis where intermediate summaries are useful artefacts) cannot easily avoid bounded recursion. A blanket ban would force a discipline that does not match real-world usage.
- **Allow unbounded recursion without disclosure.** Rejected. The case study's finding is that the user cannot tell whether the evidence resolves to a human source; the discipline cannot trust a COTS that hides this.
- **Require recursive chains to be annotated with the depth but not the type.** Rejected. The type (model-generated vs human-curated) is more informative than the depth; a chain of depth 1 with a model-generated item is qualitatively different from a chain of depth 3 with all-human items.
- **Defer the decision to a future RFC.** Rejected. The v0.2 floor (RFC-0001) assumes the chain terminates; without a Q3.2 resolution the v0.2 floor's assumptions are incomplete. The two RFCs are interdependent.

## Consequences

Easier:

- The case study's Q3.2 finding is closed; the discipline has a position on recursive evidence chains.
- The Layer 2 contract (RFC-0001) is now grounded in a complete model of evidence chains.
- The conformance suite grows by 4 canonical test cases (TC-RC-01..04).
- The reference implementation's `Layer2` module has a clearer job: flag recursive items, not just validate structural integrity.

Harder:

- The COTS schema gains 2 new fields (`evidence.source_type`, `evidence.chain_termination`). v0.2 implementations must support them.
- The reference implementation's tests grow from 15-20 to 20-25.
- The tools' CLI gains a new subcommand.

Commitments:

- The discipline commits to `terminal` being the default; bounded recursion is permitted but must be declared; unbounded recursion is permitted but is flagged in the warning set.
- The discipline commits to the source_type field having the four values above; new values require an RFC.

## Migration path

1. **Stage 1 (week 1).** Add the `source_type` and `chain_termination` fields to the COTS schema. Update `Source-Engineering/source-engineering-specifications/cots/README.md`.
2. **Stage 2 (week 2-3).** Author TC-RC-01..04. Implement them in the conformance suite.
3. **Stage 3 (week 4).** Update the reference implementation to flag recursive chains in the `Layer2` module.
4. **Stage 4 (week 5).** Add the `se-trace recursive` subcommand to the tools.
5. **Stage 5 (week 6).** Author the practice handbook chapter and the education curriculum section.
6. **Stage 6 (week 7).** Cut the v0.2.1 release of the COTS spec (additive change; backward-compatible at the wire format).

## Open questions

- **Is `terminal` the right default, or should it be `bounded_recursion` with a default bound of 1?** The current proposal is `terminal`; the discipline's review may revise.
- **Should unbounded recursion (be possible, or should the conformance suite reject it?** The current proposal is that the conformance suite warns rather than rejects; the discipline's review may require a hard rejection.
- **Does the `source_type` field have exactly four values, or should the discipline allow additional values (e.g. `derived-from-cots` for chains that resolve to another COTS)?** The current proposal is four values; the discipline's review may add more.
- **Does the `bounded_recursion.max_levels` bound apply per-chain or per-COTS?** The current proposal is per-COTS (a single bound for all chains in the COTS); the discipline's review may allow per-chain bounds.

## Notes

This RFC is the discipline's third foundational change, after the v0.1 floor (RFC-0000) and the v0.2 floor (RFC-0001, in review until 2026-10-25). RFC-0002 closes the case study's Q3.2 finding and grounds RFC-0001's Layer 2 contract.

The two RFCs (RFC-0001 and RFC-0002) are interdependent. The discipline may accept RFC-0001 only with RFC-0002 in flight; the discipline may accept RFC-0002 only with RFC-0001's Layer 2 contract in flight. The discipline's review will sequence the acceptance.