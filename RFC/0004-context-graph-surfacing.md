# RFC 0004 -- Context Graph Surfacing

> The discipline's fourth foundational RFC. Defines the graph-structured, user-facing rendering of a COTS's Context and Evidence parts, and the contract that governs how the context-engineering knobs surface to the user.

## Status

In Review

## Champion

emmanuel-a-otchere

## Date opened

2026-11-02

## Date closes

2026-11-16 (minimum 14 days from opening)

## Motivation

Two findings converge on the same gap.

First, the manifesto's second principle says "context is a runtime signal, a public interface, not a hidden knob," and Pillar 2 (Contextual Signature Mapping) requires Layer 2 to surface every runtime knob back to the user with rationale. But in practice the knobs (temperature, top-p, prompt cache, tool budget, retrieval depth, thinkingBudget) are exposed only as developer settings. The COTS carries them in `context.parameters`, yet nothing in the discipline specifies how the user *sees* them or how they relate to the rest of the response. Research question Q2.2 names this exactly: the contract between the context-engineering layer (which sets the parameters) and the COTS layer (which surfaces them to the user) is not specified.

Second, research question Q4.2 asks what the user-facing visualisation of a COTS is; the discipline currently answers only partially, with the Reasoning Roadmap in `source-engineering-tools` (`se-trace visualize`), which renders Sources -> Claims -> Decision but leaves the Context part unrendered. The education glossary already maps "graph" to the Reasoning Roadmap, and Pillar 3 names "structured summaries or knowledge graphs" as the chain-of-evidence shape, so the graph vocabulary is already canonical; what is missing is a graph that includes the context nodes and typed edges between context, sources, claims, and decisions.

The problem is real and evidenced: a reviewer holding a COTS today can read `context.parameters` as raw JSON, but cannot see *why* the parameters were set this way, which constraints they interact with, or how they connect to the claims and evidence in the rest of the response. That is the hesitation factor persisting at the context layer even when the evidence layer is well-formed.

## Proposed change

The discipline adopts a **Context Graph** as a first-class, user-facing surface of the COTS, alongside the existing Reasoning Roadmap:

- **Definition.** A Context Graph is the graph-structured rendering of a COTS's `context` and `evidence` parts: nodes are context parameters, constraints, source artifacts, evidence items, claims, and decisions; edges are the typed relationships between them. It is the graph form of the COTS's Context + Evidence parts, not a separate artifact.
- **Typed edge vocabulary.** Edges carry a relationship type from the minimum vocabulary that Q3.1 asks for: at minimum `supports`, `contradicts`, `contextualises`, and `constrains`. New relationship types require a CR. (This RFC answers Q3.1's vocabulary at the minimum level and leaves extension to review.)
- **Context nodes and edges.** Every entry in `context.parameters` becomes a node of type `parameter`, linked from a `context` node by a `set-by` edge. Every entry in `context.constraints` becomes a node of type `constraint`, linked by a `constrains` edge to each node it constrains. Parameters and constraints link to decisions by `contextualises` edges: the decision was made under this context.
- **Evidence edges.** The existing Reasoning Roadmap edges (source `-. supports .->` claim, source `-. contradicts .->` claim, claim `--> ` decision) are retained unchanged, now expressed in the same typed-edge vocabulary.
- **Chain termination in the graph.** When RFC-0002 fields are present, model-generated evidence nodes are rendered with a distinct style (the existing `contradiction`-style red class or a new `model-generated` class), so the graph makes recursive evidence chains visible at a glance rather than only via `se-trace recursive`.
- **Surfacing contract (closes Q2.2).** A COTS's Context part is considered *surfaced* when a user-facing artifact (the Context Graph rendering, or an equivalent machine-readable export) is producible from the COTS alone, showing every parameter with its value, every constraint, and their edges to the rest of the COTS. "Surfaced" means reproducible by the user without asking the operator; it does not mean the user must be able to change the parameters at runtime (that is a separate, larger question).
- **Tooling.** `se-trace` gains a `context-graph <file>` subcommand in `Source-Engineering/source-engineering-tools` that emits the Context Graph as Mermaid plus a self-contained HTML page (same pattern as `se-trace visualize`), writing `<base>.context-graph.mermaid` and `<base>.context-graph.html`. The renderer lives in a new `src/context-graph.ts` next to `src/visualize.ts`.
- **Schema.** No change to the COTS schema is required: the Context Graph is derived entirely from existing `context`, `evidence`, and `inputs` fields. The `Context` interface in `source-engineering-tools/src/cots.ts` already models `parameters` and `constraints`.
- **Specifications.** `source-engineering-specifications` gains a short normative note (in `cots/`) stating the surfacing contract above, so conformance can test "context is surfaced" rather than "context exists."

The change is **additive and backward-compatible**: existing COTSs render a Context Graph without any schema change, and `se-trace visualize` is unchanged.

## Alternatives considered

- **Extend `se-trace visualize` to include context nodes instead of adding a subcommand.** Rejected for now: the Reasoning Roadmap has a stable, taught shape (education curriculum Unit on the Reasoning Roadmap); changing its node set would invalidate existing teaching material. A separate `context-graph` subcommand keeps both surfaces stable. A future RFC may merge them.
- **Make the Context Graph a COTS schema field (the graph serialized inside the COTS).** Rejected: the graph is derivable from existing fields; serializing it would duplicate data and create a consistency burden. Derivation keeps one source of truth.
- **Require runtime user-tunable knobs in this RFC.** Rejected as scope creep: surfacing (visibility) is the v0.x contract; tunability is the Pillar 2 "tunable per call" ambition and belongs to a later slice with latency and safety implications.
- **Use undifferentiated edges.** Rejected: Q3.1 exists precisely because untyped "related to" edges make the graph uninspectable. Typed edges are the point.

## Consequences

Easier:

- Q2.2 (context/COTS surfacing contract) and Q4.2 (user-facing COTS visualisation) get a concrete answer.
- The minimum evidence-relationship vocabulary (Q3.1) is specified at the floor level.
- Reviewers can see, in one graph, the parameters, constraints, sources, claims, and decision, and how they connect.
- Recursive evidence chains (RFC-0002) become visible in the graph, not only in CLI output.

Harder:

- The tools repo grows a new module and subcommand; its test suite grows accordingly.
- Conformance gains a notion of "context surfaced" that must be tested.
- Maintaining two renderings (Reasoning Roadmap, Context Graph) until a possible future merge.

Commitments:

- The discipline commits to the typed edge vocabulary (`supports`, `contradicts`, `contextualises`, `constrains`, `set-by`) as the floor; extension by CR only.
- The discipline commits to the Context Graph being derivable from the COTS alone, with no schema change.
- The discipline commits to "surfaced" meaning user-reproducible visibility, not runtime tunability.

## Migration path

Additive; no migration required.

1. **Stage 1.** Land this RFC; add the surfacing-contract note to `source-engineering-specifications/cots/`.
2. **Stage 2.** Implement `src/context-graph.ts` and `se-trace context-graph` in `source-engineering-tools`, with tests against the existing `legal-summarization.cots.json` fixture.
3. **Stage 3.** Add conformance test cases (TC-CG-01..N) that a Context Graph is producible from a COTS and that every parameter and constraint appears as a node.
4. **Stage 4.** Update the education glossary and curriculum to name the Context Graph alongside the Reasoning Roadmap; update the practice handbook with a reviewer chapter.
5. **Stage 5.** Update the research agenda: mark Q2.2, Q4.2, and the Q3.1 vocabulary floor as closed (with this RFC as the protocol), moving them to the closed-questions section.

## Open questions

- Should `se-trace visualize` and `se-trace context-graph` merge into one rendering in a future version? Deferred to a later RFC.
- Should `contextualises` edges from parameters to decisions be per-decision or per-COTS? Current proposal: per-decision (each decision node carries its contextualising edges), since a COTS may hold several decisions made under the same context.
- Should the surfacing contract require a machine-readable export (e.g. JSON graph) in addition to Mermaid/HTML? Current proposal: Mermaid + HTML suffice for v0.x; JSON-LD export is a candidate for a later slice.
- Does `thinkingBudget` belong in `context.parameters` for providers that expose it? Current proposal: yes, when present; the `Context.parameters` interface is extended additively when a provider field is named.

## Notes

This RFC builds on RFC-0001 (Layer 2 floor, which is the home of context engineering) and RFC-0002 (recursive evidence chains, whose model-generated nodes the Context Graph renders distinctly). It answers research questions Q2.2, Q4.2, and the floor of Q3.1, and it operationalises manifesto principle 2 ("context is a runtime signal") and Pillar 2's user-side surfacing requirement.
