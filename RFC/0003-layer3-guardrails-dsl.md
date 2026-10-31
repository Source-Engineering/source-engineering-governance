# RFC 0003 -- v0.4 Floor: Layer 3 (Business Guardrails DSL)

> The third floor. Operationalises the guardrail DSL and the Layer 3 contract into a shippable layer: rule grammar, evaluation engine, conformance cases, tooling, and practitioner guidance.

## Status

In Review

## Champion

Emmanuel A. Otchere

## Date opened

2026-10-30

## Date closes

2026-11-13 (minimum 14 days from opening)

## Motivation

The discipline's four-layer stack has two floored layers. Layer 1 (typed model) makes the decision structurally valid; Layer 2 (runtime validation) flags the runtime signals. What neither layer does is apply **the domain's policy** to the decision. The 2026 reframing essay named this the **judgment gap**: a model can return a decision that fits every declared option and is still the wrong decision for the business.

The Layer 3 contract (`Source-Engineering/source-engineering-architecture/layer-contracts/layer-3-business-guardrails.md`) and the guardrail DSL shape (`Source-Engineering/source-engineering-practice/handbook/04-authoring-guardrails.md`) are already written in prose. The legal case study's Slice 3 findings provide the rule corpus. What is missing is the floor: a grammar the machine can parse, an evaluator the reference implementation ships, conformance cases the suite enforces, a CLI command reviewers run, and guidance domain experts read.

v0.4 is required because:

- **Layer 2 flags; only Layer 3 can reject.** The discipline's no-silent-rejection guarantee is hollow without a layer that actually stops a response and records why. Today a flagged decision flows nowhere; there is no floor to catch it.
- **Guardrails authored by domain experts, not engineers, is Slice 5's open question.** The question is answered by shipping a DSL a domain expert can write, not by restating that one is needed.
- **The case study produced rule-shaped findings** (jurisdiction constraints, recency windows, redaction) that have no home. The DSL is that home.

## Proposed change

v0.4 floors Layer 3 with the following, landed as one vertical slice:

- **Rule grammar (specifications).** The guardrail rule becomes a normative artefact: `rule <rule-id> { when <condition-on-cots> then <action> with rationale "<text>" }`, where `<action>` is `reject` (binding), `annotate` (advisory), or `escalate`. The condition references COTS schema fields (constraint scope/value, source temporal validity, decision-type, Layer 2 flags). Rules are carried in the decision-type registry alongside the question and answer schemas.
- **GuardrailDecision schema field (specifications).** COTS schema v1.3.0 adds the optional `decisions[*].guardrail` field: `{ decision: accepted | rejected | annotated | escalated, rule_id, rationale, binding: boolean }`. The `validation_status` update rule is normative: a rejected decision's status becomes `superseded`; an escalated decision's status becomes `appealed`.
- **Conformance test cases (specifications + conformance).** TC-L3-01..N added to the compatibility test suite and implemented in the conformance suite: guardrail record well-formedness, enum enforcement, binding/advisory distinction, rejection requires a binding rule, escalation surfaces, and the validation_status consistency rule.
- **Reference implementation.** A `Layer3` module: takes a post-Layer-2 COTS plus the domain's parsed rule set, evaluates every rule against every decision, attaches GuardrailDecision records, updates `validation_status`, and returns rejected/escalated decisions alongside the COTS. The parser accepts the DSL text; malformed rules return `guardrail-rules-malformed`; no rules returns `domain-rules-missing`.
- **Tools.** `se-trace check-guardrails <cots> [--rules <file>]` evaluates a COTS against a rule file and prints each decision's GuardrailDecision with the rule and rationale.
- **Practice.** Handbook chapter 11: reading a GuardrailDecision, authoring rules against the corpus from the case study, the binding/advisory decision, and what to do with a rejection.
- **Education.** Curriculum Unit 7: authoring and evaluating guardrails.

The change is **forward-compatible** at the COTS level: a v1.2.0 producer does not emit `decisions[*].guardrail`; consumers treat absence as "the producer has not floored Layer 3."

## Alternatives considered

- **Guardrails as code (TypeScript predicates).** Rejected for the main path: Slice 5's open question is precisely whether domain experts can author rules without engineers. Predicates remain the compilation target of the DSL, not the authoring surface.
- **Guardrails in configuration (JSON/YAML).** Rejected as the authoring surface: the DSL's rationale text and rule-block shape are the practitioner-facing contract already documented in the handbook; JSON loses the readability.
- **Deferring Layer 3 until Layer 4.** Rejected: the audit trail (Layer 4) records what happened; without Layer 3 there is nothing consequential to record. The stack ships top-down (L1, L2, L3, L4 floors in order).

## Costs and risks

- **Rule correctness is out of scope** (the contract says so explicitly); the discipline records which rule fired, not whether the rule was wise. The research agenda tracks rule-space coverage.
- **DSL grammar stability.** The grammar is small (one rule shape, one condition language referencing COTS fields); changes after v0.4 get a CR per the versioning policy.

## Related documents

- `layer-contracts/layer-3-business-guardrails.md` -- the normative contract this floor implements.
- `../source-engineering-practice/handbook/04-authoring-guardrails.md` -- the DSL shape this RFC normativises.
- `../source-engineering-specifications/cots/README.md` -- the COTS schema this floor extends to v1.3.0.
- `2026-legal-summarization` case study -- the rule corpus.
