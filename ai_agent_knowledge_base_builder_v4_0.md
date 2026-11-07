# AI Agent Knowledge Base Builder (v4.0 — Pure Filesystem, Zero-Git, Bash-Only Antigravity & Gemini Edition)

> **DIRECT SYSTEM INSTRUCTION FOR THE AI AGENT (GEMINI / ANTIGRAVITY):**
>
> You are acting as a Principal Rust & Distributed Systems Architect, Knowledge-Base Engineer, and Coding-Agent Safety Designer.
>
> **This document is a self-contained, self-executing instruction package.** When a user provides or references this file in Antigravity, your task is to immediately begin equipping this repository from scratch with the `meta/` knowledge base, root and nested `AGENTS.md`/`GEMINI.md` files, `.agents/skills/`, and pure-Bash enforcement scripts defined below.
>
> **How to Execute This Document:**
>
> 1. Work strictly phase by phase from **Phase 0 to Phase 8** (Section 14). Do not skip phases.
>
> 2. **Strict No-Git & Bash-Only Mandate:** Do NOT invoke `git` commands anywhere in the tooling or discovery workflow. All file discovery, staleness tracking, and validation must use pure filesystem utilities (`find`, `grep`, `awk`, `sed`, `jq`, `yq`, `sha256sum`/`shasum`). Furthermore, **always use Bash (`*.sh`) over Python (`*.py`)**. Never create or run Python scripts.
>
> 3. Copy the Bash scripts in **Section 12** verbatim into `meta/bin/`, `meta/checks/`, and `meta/profiles/rust/`, make them executable (`chmod +x`), and adapt only `meta/config.sh` if needed.
>
> 4. Write down **only what the code cannot tell a reader in one or two tool calls**: architectural intent, type-system invariants, wire/compatibility contracts, hidden cross-service couplings, idiomatic patterns (pointing to Golden Exemplars), conventions, and past lessons.
>
> 5. Every fact must carry `source: verified | inferred | proposed`. Never mark your own inference as `verified`. When uncertain, set `source: proposed` and `requires_review: true`.
>
> 6. Metadata and relations live **only in Gold frontmatter (`meta/gold/**.md`)**. Never hand-edit `meta/generated/` or generated blocks inside `AGENTS.md`; always run `./meta/bin/build`.
>
> 7. Do not alter application runtime behavior. Outside `meta/`, you may only create/update `AGENTS.md`, `GEMINI.md`, `.ignore`, `.agents/skills/`, and `// INV-xxx:` doc-comment anchors in source code.
>
> 8. **Pause for Human Review** after **Phase 3 (Invariants, Contracts & Couplings)** unless the user explicitly tells you to run all phases unattended.

# 1. Core Architecture & Why v4 Works

Frontier models excel at local syntax generation but fail at **global architectural conservation** when modifying complex, multi-workspace systems. Under task pressure, an unguided agent takes the shortest path to green compilation—widening visibility (`pub`), bypassing typestate constructors, cloning single-use tokens, or weakening test assertions.

v4 solves this in any directory or workspace—even exported archives, air-gapped sandboxes, or multi-folder workspaces without `.git`—by combining **Single-Source-of-Truth Curated Gold Docs** with **Pure-Filesystem Bash Tooling (`find` + content hashes)** and **Automated Delivery Surfaces** that Antigravity and Gemini automatically load.

```
Sources: Cargo.toml, Rust crates, types/traits, tests, schemas, scripts, existing docs
   │  (Scanned via pure `find` + `grep` — zero git dependency)
   ▼
Bronze (raw evidence)          meta/_bronze/        excluded from agent grep via .ignore
   │  curated by the builder agent
   ▼
Gold (single source of truth)  meta/gold/**.md      one concept per file, strict YAML frontmatter
   │  ./meta/bin/build (deterministic Bash generator + content-hash stamper)
   ▼
Generated                      meta/generated/      index.json, registries/*.yaml, graph.json,
                                                    graph.graphml, test-baseline.tsv
   │
   ▼
Antigravity / Gemini Delivery Surfaces (what future coding agents actually consume)
   ├─ Root AGENTS.md + GEMINI.md          auto-loaded every session: commands, protocol, covenant, P0 list, routing
   ├─ <crate>/AGENTS.md (+ GEMINI.md)     auto-loaded when working inside that crate/component directory
   ├─ .agents/skills/<pack>/SKILL.md      loaded on demand by Antigravity when task matches description
   ├─ Code Anchors  // INV-001: ...       seen at the exact line of code; `./meta/bin/ctx show INV-001`
   ├─ ./meta/bin/ctx                      1-command deterministic router: impact, show, stamp, tests-for, search, stale
   └─ ./meta/bin/validate                 enforces DAG rules, forbidden regexes, Bash-only rule, budgets, test-weakening
```

# 2. Core Principles

**2.1 Deliver knowledge where Antigravity and Gemini already look.**
Agents read source code, root `AGENTS.md` / `GEMINI.md`, nested `<component>/AGENTS.md`, `.agents/skills/`, and CLI output. Put critical constraints in those exact places. `meta/gold/` is the single source of truth that generates those surfaces.

**2.2 Write down only what the code cannot tell.**
Before writing a sentence, ask whether an agent can derive it in one tool call. If yes, omit it. Spend context budget on *why* boundaries exist, typestate/visibility anchors, rejected alternatives, wire compatibility, concurrency/failure semantics, and hidden couplings.

**2.3 Enforce, then document (The Enforcement Ladder + Type Anchors).**
Prose invariants are advisory; compiler types, forbidden regex patterns, and tests are guarantees. Every invariant records its current enforcement level, its target level, its exact **Rust Type-System Anchor**, and any **Forbidden Code Regex** (`forbidden_patterns`) so `./meta/bin/validate` catches violations immediately.

**2.4 Show, don't describe (Patterns & Golden Exemplars).**
Models follow a concrete exemplar far better than abstract prose. Every recurring engineering task (adding a route, tool, policy check, consumer, or domain error) gets a `PAT-*` doc pointing to a real **Golden Exemplar** file in the workspace plus a 20–40 line idiomatic code skeleton.

**2.5 One fact, one place (Zero Manual Duplication).**
All metadata and relations live exclusively in `meta/gold/**.md` YAML frontmatter. Registries (`meta/generated/registries/*.yaml`), graphs (`graph.json`, `graph.graphml`), nested `AGENTS.md` blocks, and `.agents/skills/*/SKILL.md` are 100% generated by `./meta/bin/build`.

**2.6 Pure-Filesystem Provenance & Content-Hash Freshness (Zero Git Required).**
Classify every document as `verified`, `inferred`, or `proposed`. Instead of relying on git commits, `verified` documents anchored to code paths carry a deterministic `verified_hash` (computed by `find` + `sha256sum`/`shasum` over the anchored files via `./meta/bin/ctx stamp`). Whenever underlying source files are edited, `./meta/bin/ctx impact` and `./meta/bin/ctx stale` immediately detect the hash mismatch and flag the document as `STALE`.

**2.7 Small and sharp beats complete (Strict Context Budgets).**
Context is an attention budget. A 50-line nested `AGENTS.md` is obeyed; a 500-line manual is skimmed. `meta/checks/budgets.sh` enforces line and word limits across all generated and authored surfaces.

**2.8 Ceremony scales with risk (T1 / T2 / T3 Tiers).**
`./meta/bin/ctx impact <files>` computes the risk tier automatically:

* **T1 (Low risk):** Implement and run the component's fast checks.
* **T2 (Medium risk / P1 invariant / unmapped files):** Write a short plan and test-first verification before editing.
* **T3 (Critical risk / P0 invariant / Contract / Coupling / cross-component):** Complete pre-change impact analysis, preserve all type anchors, and run independent diff/validation review.

**2.9 Strict Bash-Only & Find-Only Tooling (Never Use Python or Git).**
All generators, routers, discovery scripts, and conformance checks MUST be portable Bash scripts (`*.sh`) using `find`, `grep`, `awk`, `sed`, `jq`, `yq` (v4), and `cargo`.
* **Never use `git`** (`git ls-files`, `git log`, `git diff`, `git rev-parse` are strictly forbidden; always use `find` and content hashes/baselines).
* **Always use Bash (`*.sh`) over Python (`*.py` / `*.ipynb`).** Creating or using Python scripts for automation, validation, or glue tooling is a hard validation error.

# 3. Target Repository Structure

When equipping a repository from scratch, create this clean structure:

```
AGENTS.md                          authored instructions + generated P0 & component routing block
GEMINI.md                          "@AGENTS.md" (auto-loaded by Gemini / Antigravity)
.ignore                            excludes meta/_bronze/ and meta/generated/ from agent grep
.agents/
  skills/<name>/SKILL.md           generated from ContextPacks (PACK-*) by ./meta/bin/build
<component-dir>/AGENTS.md          generated component constraints block (+ optional human header)
<component-dir>/GEMINI.md          "@AGENTS.md" (optional, controlled by NESTED_GEMINI_MD in config.sh)

meta/
  README.md                        human maintainer guide
  config.sh                        project settings for meta scripts (excludes, budgets, patterns)
  gold/
    overview.md                    VIEW-overview: system purpose, tier topology, non-goals (≤ 60 lines)
    glossary.md                    GLOSS-001: domain terms ↔ exact Rust/code types
    architecture/                  VIEW-* architecture views, ARCH-* dependency rules
    components/                    COMP-* (one per workspace crate or deployable service)
    invariants/                    INV-* (P0/P1/P2 rules with Type Anchors & forbidden_patterns)
    patterns/                      PAT-* (canonical recipes & Golden Exemplars)
    conventions/                   CONV-* (error handling, async/Tokio, observability, serde, Bash-over-Python)
    contracts/                     CTR-* (wire schemas, Ed25519 canonical bytes, public APIs, data entities)
    couplings/                     COUP-* (cross-service, DB column, env var, or metric couplings)
    rules/                         BR-* (business rules with concrete valid/invalid examples)
    flows/                         FLOW-* (critical multi-tier flows with Mermaid & failure semantics)
    adr/                           ADR-* (architectural decisions & rejected alternatives)
    test-contracts/                TC-* (what critical test suites protect & anti-tampering rules)
    lessons/                       LES-* (known architectural traps & past agent mistakes)
    packs/                         PACK-* (task context packs compiled into .agents/skills/)
  generated/                       DO NOT EDIT BY HAND: index.json, registries/*.yaml, graph.json,
                                   graph.graphml, test-baseline.tsv
  bin/                             lib.sh, build, ctx, validate, discover
  checks/                          frontmatter.sh, links.sh, anchors.sh, invariants.sh, budgets.sh,
                                   test-weakening.sh, stale.sh
  profiles/rust/                   check-crate-deps.sh, derive-components.sh
  playbooks/                       impact.md, review.md
  reports/                         discovery-mechanical.md, phase-0..8 reports, final-summary.md
  _bronze/                         raw notes, legacy doc extracts (hidden from agent search via .ignore)
```

# 4. Gold Document Schema & ID Prefixes

Every file in `meta/gold/**.md` has YAML frontmatter followed by a concise Markdown body. Relations and metadata live **only** in frontmatter.

## 4.1 Common Frontmatter Schema

```yaml
---
id: INV-003
type: Invariant
title: Canonical Grant Binding
summary: Capability grants must be cryptographically bound to RFC 8785 canonical payload bytes.
status: active                   # active | draft | deprecated
source: verified                 # verified | inferred | proposed
requires_review: false           # true if proposed or uncertain
verified_hash: 9f86d081884c      # auto-stamped by `./meta/bin/ctx stamp` over anchored files
anchors:
  paths: ["marketplace_trust_gateway/src/grant/**"]
  symbols: ["trust_gateway::grant::VerifiedGrant"]
relations:
  components: [COMP-marketplace-trust-gateway]
  invariants: []
  patterns: [PAT-002]
  rules: [BR-003]
  flows: [FLOW-001]
  contracts: [CTR-001]
  couplings: []
  adrs: [ADR-0002]
  lessons: []
  tests: ["marketplace_trust_gateway/tests/grant_verification.rs"]
agent:
  risk: critical                 # low | medium | high | critical (critical forces T3)
---
```

## 4.2 Document Types and ID Prefixes

| Type | Prefix | Folder | Purpose |
| ----- | ----- | ----- | ----- |
| `View` | `VIEW-` | `gold/`, `gold/architecture/` | System overview and macro architecture views |
| `Glossary` | `GLOSS-` | `gold/glossary.md` | Domain terms $\leftrightarrow$ exact Rust types & disambiguation |
| `Component` | `COMP-` | `gold/components/` | Crate or service ownership, boundaries, concurrency rules, fast checks |
| `ArchitectureRule` | `ARCH-` | `gold/architecture/` | Workspace DAG rules & `forbidden_dependencies` |
| `Invariant` | `INV-` | `gold/invariants/` | Non-negotiable rule with enforcement ladder, type anchors & forbidden regexes |
| `Pattern` | `PAT-` | `gold/patterns/` | Canonical implementation recipe, Golden Exemplar & code skeleton |
| `Convention` | `CONV-` | `gold/conventions/` | Cross-cutting coding standards (errors, async, tracing, serde, Bash-only) |
| `Contract` | `CTR-` | `gold/contracts/` | Data entities, wire formats, canonical serialization & schema evolution |
| `Coupling` | `COUP-` | `gold/couplings/` | Hidden dependency that breaks at a distance (ports, schemas, env vars) |
| `BusinessRule` | `BR-` | `gold/rules/` | Domain/policy logic with concrete valid and invalid examples |
| `Flow` | `FLOW-` | `gold/flows/` | Multi-tier sequence diagram, timeouts, idempotency & failure modes |
| `ADR` | `ADR-` | `gold/adr/` | Architectural decision, trade-offs & rejected alternatives |
| `TestContract` | `TC-` | `gold/test-contracts/` | Protected conformance test suite & anti-tampering rules |
| `Lesson` | `LES-` | `gold/lessons/` | Architectural traps & past failure modes |
| `ContextPack` | `PACK-` | `gold/packs/` | Task bundle automatically compiled into `.agents/skills/<name>/SKILL.md` |

# 5. Gold Document Templates

Keep every Gold document concise (target $\le 60$ lines, hard budget $\le 200$ lines). Omit empty sections. After creating or updating Gold documents, run `./meta/bin/ctx stamp` to compute and populate `verified_hash` automatically for all verified documents.

## 5.1 Overview (`meta/gold/overview.md`)

```markdown
---
id: VIEW-overview
type: View
title: System Overview
summary: One-sentence description of the platform, its tiers, and its core security/domain guarantee.
status: active
source: verified
---
# Purpose
Two or three sentences explaining what the system does and the problem it solves.

# Explicit Non-Goals
- What the system (or specific tiers) must never do directly.

# Tier & Component Topology
One line per component (`COMP-*`), grouped by architectural tier.

# Critical End-to-End Flows
The 3–5 flows (`FLOW-*`) where a bug compromises security, correctness, or money.

# Workspace Build, Lint & Test Commands
Exact commands (fastest per-crate commands first, full workspace commands second).
```

## 5.2 Component (`meta/gold/components/comp-*.md`)

```markdown
---
id: COMP-triage-agent
type: Component
title: Triage Agent
summary: Evaluates operation risk and routes ambiguous/high-risk tool calls to human quorum.
status: active
source: verified
verified_hash: <auto-filled-by-ctx-stamp>
anchors: { paths: ["triage_agent/**"] }
agents_md: triage_agent/AGENTS.md
forbidden:
  - Unilaterally issuing an ALLOW grant for high-risk or unknown schemas (INV-005)
  - Direct execution of downstream B2B or shop tools
checks:
  - cargo check --manifest-path triage_agent/Cargo.toml --all-targets
  - cargo clippy --manifest-path triage_agent/Cargo.toml --all-targets -- -D warnings
  - cargo test --manifest-path triage_agent/Cargo.toml
relations:
  contracts: [CTR-001]
  tests: ["triage_agent/tests/**"]
agent: { risk: high }
---
# Purpose & Boundaries
What enters and leaves this component (ports, RPC routes, types).

# Concurrency, Executor & Resource Rules
Async runtime assumptions, lock discipline, timeouts, and backpressure.

# Traps (Look Like Simplifications, But Break the System)
- Trap 1 and why it must not be changed.
```

## 5.3 Invariant (`meta/gold/invariants/inv-*.md`) — Type & Regex Anchored

```markdown
---
id: INV-003
type: Invariant
title: Canonical Grant Binding
summary: Capability grants must be Ed25519-verified against canonical payload bytes before execution.
severity: P0                          # P0 = security/trust/money/isolation; P1 = correctness; P2 = hygiene
status: active
source: verified
verified_hash: <auto-filled-by-ctx-stamp>
anchors:
  paths: ["marketplace_trust_gateway/src/**"]
  symbols: ["VerifiedGrant"]
enforcement:
  level: type                         # type | compile_fail_test | lint | dependency_rule | property_test | test | ci_check | runtime_assertion | review_only
  mechanism: "VerifiedGrant fields are private; only Verifier::verify_signed_grant constructs it"
  command: "cargo test --manifest-path marketplace_trust_gateway/Cargo.toml grant"
  target_level: type
  forbidden_patterns:                 # Scanned across anchors.paths (excluding tests) by checks/invariants.sh!
    - pattern: "UnverifiedGrant::into_inner_unchecked"
      reason: "Bypasses Ed25519 signature and nonce verification"
forbidden:
  - Widening VerifiedGrant fields to pub or adding #[derive(Deserialize, Clone)]
  - Executing a downstream tool using raw serde_json::Value instead of VerifiedGrant
relations:
  components: [COMP-marketplace-trust-gateway]
  contracts: [CTR-001]
  tests: ["marketplace_trust_gateway/tests/conformance.rs"]
agent: { risk: critical }
---
# Invariant Statement
One precise sentence a reviewer can verify against any code change.

# Why This Matters (Blast Radius)
The security, isolation, or financial failure mode if violated.

# Type-System & Compile-Time Anchors
- **Enforcing Types / Typestates:** `VerifiedGrant<T>`
- **Visibility & Trait Constraints:** Fields are private to `grant` module; `Clone` and `Deserialize` are intentionally omitted so tokens cannot be forged or reused.

# Allowed Pattern
- Construct exclusively via `Verifier::verify()` (see `PAT-001`).

# Evidence
Exact file paths and test functions proving this invariant.
```

## 5.4 Pattern (`meta/gold/patterns/pat-*.md`) — With Golden Exemplar

````markdown
---
id: PAT-001
type: Pattern
title: Adding a Governed Tool Endpoint
summary: Idiomatic handler signature, VerifiedGrant extraction, audit logging, and conformance test wiring.
status: active
source: verified
verified_hash: <auto-filled-by-ctx-stamp>
anchors: { paths: ["marketplace_trust_gateway/src/handlers/**"] }
relations:
  components: [COMP-marketplace-trust-gateway]
  invariants: [INV-001, INV-003, INV-009]
---
# Golden Exemplar in Repository
`marketplace_trust_gateway/src/handlers/execute_tool.rs` — copy its structure, error mapping, and tracing span attributes.

# Idiomatic Code Skeleton
```rust
#[tracing::instrument(skip(state, grant, req), fields(tenant_id = %grant.tenant_id()))]
pub async fn handle_governed_action(
    state: &AppState,
    grant: VerifiedGrant<ActionScope>,
    req: ValidatedActionRequest,
) -> Result<ActionReceipt, GatewayError> {
    // 1. Consume single-use nonce (BR-003)
    // 2. Execute isolated action within timeout budget
    // 3. Persist durable audit event (INV-009) before returning
    todo!()
}
```

# Files Always Touched Together
1. `src/handlers/<name>.rs` — handler implementation
2. `src/routes.rs` — route registration under zero-trust middleware
3. `src/error.rs` — typed error variant (never `anyhow` in domain/handler signatures)
4. `tests/conformance_<name>.rs` — positive + unauthenticated rejection test

# Local Anti-Patterns (Looks Right, But Wrong Here)
- Using `.unwrap()` on payload deserialization instead of returning `GatewayError::MalformedPayload`.
- Spawning a detached `tokio::spawn` for the audit write (violates `INV-009` audit durability).
````

## 5.5 Contract / Data Entity (`meta/gold/contracts/ctr-*.md`)

```markdown
---
id: CTR-001
type: Contract
title: ExecutionGrant Wire & Cryptographic Contract
summary: Ed25519-signed capability token exchanged between Trust Gateway, Triage, and Worker Agents.
status: active
source: verified
verified_hash: <auto-filled-by-ctx-stamp>
anchors:
  paths: ["marketplace_trust_gateway/src/grant.rs", "commercial_b2b_agent/src/auth.rs"]
  symbols: ["ExecutionGrant", "VerifiedGrant"]
relations:
  components: [COMP-marketplace-trust-gateway, COMP-commercial-b2b-agent]
  invariants: [INV-003, INV-008]
agent: { risk: critical }
---
# Canonical Type & Smart Constructor
- **Type Path:** `trust_gateway::grant::ExecutionGrant`
- **Construction Rule:** Never construct via struct literal in downstream consumers; parse only through `VerifiedGrant::try_from_wire(bytes, &verifying_key)`.

# Wire Format, Canonicalization & Schema Evolution
- **Serialization:** Deterministic canonical JSON byte ordering for signature payload; `#[serde(deny_unknown_fields)]`.
- **Evolution Rule:** Additive optional fields only; changing any signed field requires a coordinated protocol version bump.
```

## 5.6 Coupling (`meta/gold/couplings/coup-*.md`)

```markdown
---
id: COUP-001
type: Coupling
title: Port Topology and Route Table Shared Across Conformance Suite
summary: Changing service listener ports or health routes breaks end-to-end mesh conformance checks.
status: active
source: verified
verified_hash: <auto-filled-by-ctx-stamp>
anchors: { paths: ["*/src/main.rs", "scripts/**"] }
relations: { components: [COMP-swarm-gateway, COMP-marketplace-trust-gateway] }
agent: { risk: high }
---
# What Breaks at a Distance
List the exact consumer script, service, external repo, DB query, or test that breaks if this value/field is altered.

# Safe Change Procedure
Step-by-step coordinated update sequence.
```

## 5.7 Convention, Glossary, BusinessRule, Flow, ADR, TestContract, Lesson

* **Convention (`CONV-*`):** Cross-cutting rule (e.g., `CONV-errors`, `CONV-async`, `CONV-tracing`, `CONV-serde`, `CONV-bash-only`). Always include `CONV-bash-only` stating that all repository automation, validation, and helper scripts must be written in portable Bash (`*.sh`), never Python (`*.py`).
* **Glossary (`GLOSS-001` in `meta/gold/glossary.md`):** Markdown table with columns `| Term | Meaning | Exact Code Type / Path | Not to be confused with |`.
* **BusinessRule (`BR-*`):** Domain rule with `# Concrete Examples` showing at least one valid input $\rightarrow$ outcome and one invalid input $\rightarrow$ exact rejection error.
* **Flow (`FLOW-*`):** Mermaid sequence/flowchart diagram + `# Concurrency, Timeouts & Failure Semantics` (retries, idempotency key, fail-closed vs fail-open per hop, crash recovery).
* **ADR (`ADR-*`):** Context, decision, trade-offs, rejected alternatives (so agents don't re-propose them), and signals for revisiting.
* **TestContract (`TC-*`):** Protected test path, exact run command, what a failure diagnoses, and **Anti-Tampering Rules** (never weaken assertions, never mock the subject under test).
* **Lesson (`LES-*`):** Known architectural trap or past agent mistake, why it looked plausible, and the check/invariant that prevents it.

## 5.8 Context Pack (`meta/gold/packs/pack-*.md` $\rightarrow$ `.agents/skills/*/SKILL.md`)

```markdown
---
id: PACK-trust-firewall
type: ContextPack
title: Trust Execution Firewall & Capability Grants
summary: Load before modifying grant verification, zero-trust routing, nonce consumption, or audit durability.
status: active
source: verified
skill:
  name: trust-execution-firewall
  description: "Use when modifying capability grants, Ed25519 signature verification, zero-trust middleware, or audit logging in marketplace_trust_gateway."
required_reading: [COMP-marketplace-trust-gateway, INV-001, INV-003, INV-009, PAT-001, CTR-001, TC-001]
checks:
  - cargo test --manifest-path marketplace_trust_gateway/Cargo.toml
  - cargo clippy --manifest-path marketplace_trust_gateway/Cargo.toml --all-targets -- -D warnings
---
# Mental Model
Five to ten lines explaining how the execution firewall separates cognitive planning from side-effecting tool execution.

# Typical Tasks & Canonical Recipes
- Add a new governed route: follow `PAT-001`.
- Update grant payload fields: follow `CTR-001` and run `TC-004`.

# Forbidden Shortcuts
- Never bypass `VerifiedGrant` or widen private struct fields to `pub`.
- Never weaken assertions in `TC-001` or `TC-004`.
```

# 6. The Enforcement Ladder & Stack Profiles

## 6.1 The Enforcement Ladder

Every `INV-*` document records `enforcement.level` and `enforcement.target_level`:

| Level | When the Agent Hears About a Violation | Typical Mechanism |
| ----- | ----- | ----- |
| `type` | Immediately during `cargo check` | Newtype with private fields, typestate (`Grant<Verified>`), sealed trait |
| `compile_fail_test` | During test run | `trybuild` compile-fail test |
| `lint` | Seconds after edit | `clippy.toml` `disallowed-methods`/`disallowed-types`, `forbidden_patterns` regex in `INV-*` |
| `dependency_rule` | On `./meta/bin/validate` | `ARCH-*` `forbidden_dependencies` checked by `check-crate-deps.sh` |
| `property_test` | On test run | `proptest` (idempotency, fuel budget bounds, canonical serialization round-trip) |
| `test` | On test run | Integration or conformance test (`TC-*`) |
| `ci_check` | On `./meta/bin/validate` | Schema check, `stale.sh` hash verification, `test-weakening.sh` baseline comparison |
| `runtime_assertion` | At runtime | Fail-closed circuit breaker, fuel counter check |
| `review_only` | Only if a human notices | Prose only — must include a `## Proposed Enforcement` upgrade in Phase 3 report |

## 6.2 Rust & Distributed Agentic Systems Profile

During **Phase 2 and Phase 3**, explicitly sweep the workspace (using `find` and `grep`) for these failure modes:

1. **Thinking vs. Acting Separation:** Cognitive/LLM ingress crates must never execute side-effecting business tools or hold standing DB/API credentials directly.
2. **Typestate & Smart Constructors:** Identify domain types whose fields are private or validated on construction (`ExecutionGrant`, `TenantId`, IR nodes). Document them so agents never widen visibility or derive raw `Deserialize` on verified types.
3. **Deterministic Serialization & Cryptography:** Where signatures (Ed25519) or hashes are computed, check for `HashMap` non-determinism vs `BTreeMap`/JCS canonical bytes.
4. **Execution Budgets & Sandboxing:** Subprocess isolation (`std::process::Command` forbidden outside sandbox runner), Wasm/IR fuel budgets, and non-Turing-complete DSL guarantees.
5. **Async & Lock Discipline:** No `std::sync::MutexGuard` held across `.await`; CPU-heavy crypto/compilation offloaded or bounded; bounded channels (`mpsc::channel`) instead of unbounded queues on request paths.
6. **Idempotency, Quorum & Fail-Closed Semantics:** Single-use capability nonces, two-human quorum graduation rules, and fail-closed circuit breaking on downstream timeout or schema ambiguity.
7. **Error & Scripting Discipline:** `thiserror` domain enums in library crates, no `.unwrap()` / `.expect()` / `todo!()` on untrusted runtime inputs, and **Bash (`*.sh`) only** for all automation/control scripts (zero Python).

# 7. Code Anchors

Place concise one-line comments in source code at the exact enforcement point of every `P0` invariant, critical contract, or hidden coupling:

```rust
// INV-003: Grant must be Ed25519-verified against canonical bytes before constructing VerifiedGrant.
// CTR-001: Wire changes to ExecutionGrant are additive-only; see meta/gold/contracts/ctr-001.md.
```

* `meta/checks/anchors.sh` scans source files using `find` + `grep -Eo` to verify that every code anchor references a valid Gold ID and warns if any active `P0` invariant has zero anchors in the codebase.

# 8. Delivery Surfaces for Antigravity & Gemini

## 8.1 Root `AGENTS.md` (and `GEMINI.md`)

`./meta/bin/build` automatically maintains the generated P0 and component routing table at the bottom of root `AGENTS.md` and creates `GEMINI.md` (`@AGENTS.md`) at the workspace root.

Author the top of root `AGENTS.md` using this exact template (adapting the build/test commands to the workspace):

```markdown
# Agent Operating Instructions & Cognitive Control Plane

<One sentence describing this workspace.> Full overview: `meta/gold/overview.md`.

## Fast Workspace Commands
- Check one crate: `cargo check --manifest-path <crate>/Cargo.toml --all-targets`
- Lint one crate: `cargo clippy --manifest-path <crate>/Cargo.toml --all-targets -- -D warnings`
- Test one crate: `cargo test --manifest-path <crate>/Cargo.toml`
- Resolve context for files: `./meta/bin/ctx impact <file1> [file2 ...]`
- Inspect any knowledge ID: `./meta/bin/ctx show <ID>`
- Re-stamp verified hashes after doc updates: `./meta/bin/ctx stamp [ID]`
- Validate knowledge & code conformance: `./meta/bin/validate`

## Tooling & Scripting Rules (No Git / Bash Only)
- **Use `find`, NOT `git`:** Do not rely on `git` commands for file discovery, search, or status checks. Use `find`, `grep`, and `./meta/bin/ctx`.
- **Always Use Bash (`*.sh`) Over Python (`*.py`):** Never write or generate Python scripts for automation, testing, or validation. All scripts must be portable Bash (`*.sh`).

## Mandatory Pre-Change Protocol
1. **Resolve Terminology:** If any domain term in the request is ambiguous, run `./meta/bin/ctx search <term>`.
2. **Deterministic Context Routing:** Before editing any code, run:
   `./meta/bin/ctx impact <files-you-plan-to-touch>`
3. **Read Targeted Context Only:** Read the matched `INV-*`, `PAT-*` (and inspect its **Golden Exemplar** source file), `CTR-*`, `COUP-*`, and `TC-*` items output by `ctx impact`. Do NOT read `meta/` wholesale.
4. **Follow Risk Tier Rules:**
   - **T1:** Implement following matched `PAT-*` and run the listed verification commands.
   - **T2:** State a brief plan and identify/write a failing test before editing code.
   - **T3 (P0 Invariant / Contract / Coupling / Cross-Crate):** Produce a pre-change impact check (`meta/playbooks/impact.md`), preserve all compile-time type anchors, test first, and run `./meta/bin/validate` before finishing.

## The Anti-Simplification & Compiler-Integrity Covenant
When resolving compiler errors, borrow-checker friction, trait bounds, or failing tests:
1. **No Type Downgrading:** NEVER replace a domain type (`VerifiedGrant`, `TenantId`, etc.) with `String`, `Vec<u8>`, or `serde_json::Value` to bypass validation.
2. **No Visibility Widening:** NEVER change private/`pub(crate)` fields or constructors to `pub` on invariant-governed types.
3. **No Illegal Trait Derives:** NEVER add `#[derive(Clone, Deserialize, Default)]` to single-use tokens or verified typestate structs.
4. **No Silent Logic Deletion:** NEVER delete an exhaustive `match` arm, remove a fuel/timeout/quorum check, or swallow errors with `let _ = ...` to force compilation.
5. **No Unchecked Runtime Panics:** NEVER introduce `.unwrap()`, `.expect()`, or `todo!()` in non-test runtime paths.
6. **No Test Contract Tampering:** NEVER weaken assertions, add `#[ignore]`, or mock out the core behavior protected by a `TC-*` test contract.

## Stop Conditions (Ask the Human Immediately)
- Fulfilling the request requires violating a `P0` invariant, `CTR-*` contract, or `ADR-*`.
- Metadata in `meta/gold/` contradicts actual code or tests.
- Fixing a failure seems to require weakening an existing test assertion.

## After-Change Verification
1. Run the scoped test and clippy commands output by `./meta/bin/ctx impact`.
2. If behavior, boundaries, or anchored code legitimately changed, verify/update the affected `meta/gold/**.md` file and refresh its content hash with `./meta/bin/ctx stamp <ID>`, then run `./meta/bin/build`.
3. Run `./meta/bin/validate` until it passes with 0 errors.

<!--Generated block is appended below by meta/bin/build-->
```

# 9. Playbooks (`meta/playbooks/`)

Create these two concise playbooks:

**`meta/playbooks/impact.md`**

```markdown
# Pre-Change Impact Analysis (Required for Tier T3)
1. **Restated Request & Acceptance Criteria:**
2. **Output of `./meta/bin/ctx impact <target-files>`:**
3. **Golden Exemplar & Pattern (`PAT-*`) Inspected:**
4. **P0/P1 Invariants & Type Anchors Preserved (No visibility widening / no type downgrading):**
5. **Wire Contracts (`CTR-*`), Couplings (`COUP-*`) & Failure Semantics Impact:**
6. **Protecting Test Contracts (`TC-*`) & Exact Verification Commands:**
7. **Decision:** Proceed (zero P0 conflicts) / Stop & Ask Human.
```

**`meta/playbooks/review.md`**

```markdown
# Independent Review Checklist
1. Run `./meta/bin/ctx impact <modified-files>`. Verify each matched `INV-*`, `CTR-*`, and `COUP-*` is preserved in the modified files.
2. Run `./meta/bin/validate`. Confirm zero test assertions were reduced compared to `meta/generated/test-baseline.tsv` and zero forbidden patterns were introduced.
3. Check modified files against the Anti-Simplification Covenant (no widened `pub` fields, no `.unwrap()` in runtime paths, no dropped timeout/fuel checks, no Python scripts created).
4. Report: BLOCKING issues, NON-BLOCKING issues, and Gold docs requiring `./meta/bin/ctx stamp`.
```

# 10. Writing Rules for Agent-Facing Docs

* **Pair every rule with its reason.** Explaining *why* (e.g., "because retries with a new key double-charge the customer") lets Gemini generalize accurately to edge cases.
* **Always pair `forbidden` with an allowed `PAT-*` or type.** Tell the agent the exact right constructor or trait to call instead.
* **Be concrete.** Use real crate names, file paths, Rust type paths, and runnable `cargo` commands.
* **Stay within budgets.** Root `AGENTS.md` $\le 150$ lines; Nested `<crate>/AGENTS.md` $\le 80$ lines; Skills $\le 1,100$ words; Gold docs $\le 200$ lines (target $\le 60$).

# 11. `meta/bin/ctx` — Deterministic Context Retrieval (Zero-Git)

Future agents do not manually browse directories or YAML registries. They run one command:

```bash
./meta/bin/ctx impact <file1> [file2 ...]
```

If no files are passed, `ctx impact` automatically uses `find` to locate files modified more recently than `meta/generated/index.json`. It resolves all matching `COMP-*`, `INV-*`, `PAT-*`, `CTR-*`, `COUP-*`, `BR-*`, `FLOW-*`, `TC-*`, `LES-*`, and `PACK-*` items, checks `verified_hash` against the live filesystem to flag `STALE` documents, computes the Risk Tier (`T1`, `T2`, `T3`), and prints the exact verification commands to execute.

Additional subcommands:

* `./meta/bin/ctx show <ID>` — prints the full Gold document and verifies its `verified_hash` against current anchored files.
* `./meta/bin/ctx stamp [ID]` — computes the current content hash of anchored files using `find` + `sha256sum`/`shasum` and writes `verified_hash` into the Gold document(s).
* `./meta/bin/ctx tests-for <ID>` — prints protecting test files, commands, and `TC-*` contracts.
* `./meta/bin/ctx search <term>` — searches IDs, titles, summaries, and `glossary.md`.
* `./meta/bin/ctx list [Type]` — lists all indexed Gold artifacts.
* `./meta/bin/ctx stale` — lists all Gold artifacts whose anchored files no longer match `verified_hash`.

# 12. Tooling Reference (Copy Verbatim — Pure Bash + `find`, Zero Git, Zero Python)

Create each script below verbatim and run `chmod +x meta/bin/* meta/checks/*.sh meta/profiles/rust/*.sh`. Adapt only `meta/config.sh` if your workspace uses non-standard directories.

## 12.1 Configuration (`meta/config.sh`)

```bash
# shellcheck shell=bash
# meta/config.sh — project-specific settings for the meta tooling (Pure Filesystem / Zero-Git).

# Directories excluded from `find` scans across the workspace.
SRC_EXCLUDES=(meta .git target node_modules dist build vendor .agents .claude .idea .vscode)

# Globs identifying test files (bash pattern match; '*' crosses '/').
TEST_PATTERNS=('tests/*' '*/tests/*' '*_test.*' '*.test.*' 'benches/*' '*.snap')

# IDs recognized as code anchors in source comments.
ANCHOR_REGEX='(INV|BR|COUP|CTR|PAT|ADR|FLOW|LES|TC)-[0-9]{3,4}'

# Write GEMINI.md ("@AGENTS.md") at root and optionally next to nested AGENTS.md files.
ROOT_GEMINI_MD=1
NESTED_GEMINI_MD=0

# Where Antigravity skills are generated from ContextPacks (PACK-*).
SKILLS_DIR=".agents/skills"

# Enforce Bash-only scripts across the entire workspace (1 = error on any *.py/*.ipynb outside vendor, 0 = only check meta/).
ENFORCE_REPO_NO_PYTHON=1

# Context budgets (enforced by meta/checks/budgets.sh).
BUDGET_ROOT_AGENTS_LINES=150
BUDGET_NESTED_AGENTS_LINES=80
BUDGET_SKILL_WORDS=1100
BUDGET_GOLD_DOC_LINES=200
```

## 12.2 Core Scripts (`meta/bin/`)

**`meta/bin/lib.sh`**

```bash
#!/usr/bin/env bash
# meta/bin/lib.sh — shared helpers sourced by all meta scripts (Pure Bash + find, Zero Git).
set -euo pipefail

META_DIR="$(cd "$(dirname "${BASH_SOURCE[0]}")/.." && pwd)"
ROOT="$(cd "$META_DIR/.." && pwd)"
GOLD="$META_DIR/gold"
GEN="$META_DIR/generated"
INDEX="$GEN/index.json"
TEST_BASELINE="$GEN/test-baseline.tsv"
# shellcheck source=/dev/null
source "$META_DIR/config.sh"

die()  { echo "FATAL: $*" >&2; exit 2; }
need() { for t in "$@"; do command -v "$t" >/dev/null 2>&1 || die "required tool '$t' not found (install find, jq, and mikefarah yq v4)"; done; }
need find jq yq awk sed grep

# Portable SHA-256 helper (works on Linux sha256sum and macOS shasum)
hash_stream() {
  if command -v sha256sum >/dev/null 2>&1; then
    sha256sum | awk '{print substr($1,1,12)}'
  elif command -v shasum >/dev/null 2>&1; then
    shasum -a 256 | awk '{print substr($1,1,12)}'
  else
    cksum | awk '{print $1}'
  fi
}

hash_file() {
  hash_stream < "$1"
}

ERRORS=0; WARNINGS=0
err()  { echo "ERROR: $*";   ERRORS=$((ERRORS + 1)); }
warn() { echo "WARNING: $*"; WARNINGS=$((WARNINGS + 1)); }
info() { echo "INFO: $*"; }
finish() { echo "-- $(basename "$0"): $ERRORS error(s), $WARNINGS warning(s)"; [ "$ERRORS" -eq 0 ]; }

gold_files() {
  [ -d "$GOLD" ] && find "$GOLD" -type f -name '*.md' | sort || true
}

frontmatter_json() {
  local f="$1"
  if [ ! -f "$f" ] || [ "$(head -n 1 "$f")" != "---" ]; then echo '{}'; return; fi
  yq --front-matter=extract -o=json -I=0 '.' "$f" 2>/dev/null || echo '{}'
}

# Bash glob match where '*' also crosses '/'
path_matches() { local p="$1" pat="$2"; [[ "$p" == $pat ]]; }

is_test_file() {
  local f="$1" pat
  for pat in "${TEST_PATTERNS[@]}"; do path_matches "$f" "$pat" && return 0; done
  return 1
}

# Pure `find` replacement for listing all workspace files (excluding SRC_EXCLUDES except meta when needed)
repo_files() {
  local prune_args=() d
  for d in "${SRC_EXCLUDES[@]}"; do
    [ "$d" = "meta" ] && continue
    prune_args+=(-path "$ROOT/$d" -o -path "$ROOT/$d/*" -o)
  done
  prune_args+=(-path "$ROOT/meta/generated" -o -path "$ROOT/meta/generated/*" -o -path "$ROOT/meta/_bronze" -o -path "$ROOT/meta/_bronze/*")
  find "$ROOT" \( "${prune_args[@]}" \) -prune -o -type f -print \
    | sed "s|^$ROOT/||" | sort -u
}

# Pure `find` listing of source/project files excluding all SRC_EXCLUDES (including meta/)
source_files() {
  local prune_args=() d
  for d in "${SRC_EXCLUDES[@]}"; do
    prune_args+=(-path "$ROOT/$d" -o -path "$ROOT/$d/*" -o)
  done
  unset 'prune_args[${#prune_args[@]}-1]'
  find "$ROOT" \( "${prune_args[@]}" \) -prune -o -type f -print \
    | sed "s|^$ROOT/||" \
    | grep -vE '(^|/)(AGENTS|GEMINI|CLAUDE)\.md$' | sort -u || true
}

# Files modified more recently than the generated index (pure `find -newer`, zero git)
changed_files() {
  [ -f "$INDEX" ] || return 0
  local prune_args=() d
  for d in "${SRC_EXCLUDES[@]}"; do
    prune_args+=(-path "$ROOT/$d" -o -path "$ROOT/$d/*" -o)
  done
  unset 'prune_args[${#prune_args[@]}-1]'
  find "$ROOT" \( "${prune_args[@]}" \) -prune -o -type f -newer "$INDEX" -print \
    | sed "s|^$ROOT/||" \
    | grep -vE '(^|/)(AGENTS|GEMINI|CLAUDE)\.md$' | sort -u || true
}

# Compute deterministic 12-char content hash over all source files matching a list of anchor globs
compute_anchor_hash() {
  local pat f matched=()
  local all_src
  mapfile -t all_src < <(source_files)
  for pat in "$@"; do
    [ -z "$pat" ] && continue
    for f in "${all_src[@]}"; do
      if path_matches "$f" "$pat" && [ -f "$ROOT/$f" ]; then
        matched+=("$f")
      fi
    done
  done
  [ "${#matched[@]}" -eq 0 ] && { echo ""; return 0; }
  local sorted
  mapfile -t sorted < <(printf '%s\n' "${matched[@]}" | sort -u)
  {
    for f in "${sorted[@]}"; do
      printf '%s:%s\n' "$f" "$(hash_file "$ROOT/$f")"
    done
  } | hash_stream
}

ensure_index() { [ -f "$INDEX" ] || "$META_DIR/bin/build" >/dev/null; }

write_block() {
  local file="$1" name="$2" content="$3" header="${4:-}"
  local begin="<!-- BEGIN GENERATED:$name (meta/bin/build, do not edit inside) -->"
  local end="<!-- END GENERATED:$name -->"
  mkdir -p "$(dirname "$file")"
  if [ ! -f "$file" ]; then
    { [ -n "$header" ] && printf '%s\n\n' "$header"; echo "$begin"; cat "$content"; echo "$end"; } > "$file"
    return
  fi
  if grep -qF "$begin" "$file"; then
    awk -v b="$begin" -v e="$end" -v c="$content" '
      $0 == b { print; while ((getline line < c) > 0) print line; skip = 1; next }
      $0 == e { skip = 0 }
      !skip   { print }' "$file" > "$file.tmp" && mv "$file.tmp" "$file"
  else
    { echo; echo "$begin"; cat "$content"; echo "$end"; } >> "$file"
  fi
}
```

**`meta/bin/build`**

```bash
#!/usr/bin/env bash
# meta/bin/build — deterministic generator from meta/gold/**.md frontmatter (Zero Git).
# Generates:
#   - meta/generated/index.json, registries/*.yaml, graph.json, graph.graphml
#   - meta/generated/test-baseline.tsv (for git-free test-weakening detection)
#   - Nested <component>/AGENTS.md blocks (and optional GEMINI.md)
#   - Antigravity skills under .agents/skills/<name>/SKILL.md
#   - Root AGENTS.md generated block (and root GEMINI.md)
source "$(dirname "${BASH_SOURCE[0]}")/lib.sh"

mkdir -p "$GEN/registries"
tmp="$(mktemp -d)"; trap 'rm -rf "$tmp"' EXIT

# 1. Build Master JSON Index
: > "$tmp/docs.jsonl"
while IFS= read -r f; do
  [ -z "$f" ] && continue
  rel="${f#"$ROOT"/}"
  frontmatter_json "$f" | jq -c --arg file "$rel" '. + {_file: $file}' >> "$tmp/docs.jsonl"
done < <(gold_files)
jq -s 'map(select(.id != null)) | sort_by(.id)' "$tmp/docs.jsonl" > "$INDEX"
count="$(jq length "$INDEX")"

# 2. Generate YAML Registries per Type
rm -f "$GEN/registries/"*.yaml
for t in $(jq -r '.[].type // empty' "$INDEX" | sort -u); do
  jq --arg t "$t" '[.[] | select(.type == $t)]' "$INDEX" \
    | yq -P '.' > "$GEN/registries/$(echo "$t" | tr '[:upper:]' '[:lower:]').yaml"
done

# 3. Generate Knowledge Graph (JSON + GraphML)
jq '
  def kind($k): {components:"RELATES_TO_COMPONENT", invariants:"GOVERNED_BY", rules:"IMPLEMENTS_RULE",
                 flows:"PART_OF_FLOW", adrs:"DECIDED_BY", patterns:"FOLLOWS_PATTERN",
                 couplings:"COUPLED_VIA", contracts:"BOUND_BY_CONTRACT", tests:"PROTECTED_BY_TEST",
                 lessons:"LEARNED_FROM"}[$k] // ("RELATES_" + ($k|ascii_upcase));
  . as $docs
  | ([ $docs[] | {id, type, title, severity, source, file: ._file} ]) as $nodes
  | ([ $docs[] | .id as $from
       | ( (.relations // {}) | to_entries[] | .key as $k | (.value // [])[]
           | {from: $from, to: ., kind: kind($k), target_kind: (if $k == "tests" then "Path" else "Doc" end)} ),
         ( (.anchors.paths // [])[] | {from: $from, to: ., kind: "ANCHORED_AT", target_kind: "Path"} ),
         ( (.required_reading // [])[] | {from: $from, to: ., kind: "REQUIRES_CONTEXT", target_kind: "Doc"} )
     ]) as $edges
  | {nodes: ($nodes + ([ $edges[] | select(.target_kind == "Path") | {id: .to, type: "Path"} ] | unique_by(.id))),
     edges: ($edges | map(del(.target_kind)))}
' "$INDEX" > "$GEN/graph.json"

jq -r '
  def esc: tostring | gsub("&";"&amp;") | gsub("<";"&lt;") | gsub(">";"&gt;") | gsub("\"";"&quot;");
  "<?xml version=\"1.0\" encoding=\"UTF-8\"?>",
  "<graphml xmlns=\"http://graphml.graphdrawing.org/xmlns\">",
  "  <key id=\"type\" for=\"node\" attr.name=\"type\" attr.type=\"string\"/>",
  "  <key id=\"title\" for=\"node\" attr.name=\"title\" attr.type=\"string\"/>",
  "  <key id=\"kind\" for=\"edge\" attr.name=\"kind\" attr.type=\"string\"/>",
  "  <graph id=\"project\" edgedefault=\"directed\">",
  (.nodes[] | "    <node id=\"\(.id|esc)\"><data key=\"type\">\(.type|esc)</data><data key=\"title\">\(.title // .id | esc)</data></node>"),
  (.edges | to_entries[] | "    <edge id=\"e\(.key)\" source=\"\(.value.from|esc)\" target=\"\(.value.to|esc)\"><data key=\"kind\">\(.value.kind|esc)</data></edge>"),
  "  </graph>",
  "</graphml>"
' "$GEN/graph.json" > "$GEN/graph.graphml"

LINKED='def linked($c; $t; $key):
  [ .[] | select(.type == $t)
        | select(((.relations.components // []) | index($c.id)) or (($c.relations[$key] // []) | index(.id))) ];'

# 4. Generate Nested AGENTS.md per Component
jq -c '.[] | select(.type == "Component" and .agents_md != null)' "$INDEX" | while IFS= read -r comp; do
  target="$ROOT/$(jq -r '.agents_md' <<<"$comp")"
  jq -r --argjson c "$comp" "$LINKED"'
    . as $all
    | "## \($c.title) (`\($c.id)`)", "", ($c.summary // ""), "",
      "Inspect any ID with `./meta/bin/ctx show <ID>`. Before editing, run `./meta/bin/ctx impact <files>`.", "",
      ( linked($c; "Invariant"; "invariants") | if length > 0 then
          "### Governing Invariants (must remain true)", (sort_by(.severity, .id)[]
          | "- `\(.id)` [\(.severity // "?"), enforced by \(.enforcement.level // "?")]: \(.title)"), "" else empty end ),
      ( $c.forbidden // [] | if length > 0 then "### Forbidden Responsibilities & Shortcuts", (.[] | "- \(.)"), "" else empty end ),
      ( linked($c; "Pattern"; "patterns") | if length > 0 then
          "### Canonical Implementation Patterns (Golden Exemplars)", (.[] | "- `\(.id)`: \(.title)"), "" else empty end ),
      ( linked($c; "Contract"; "contracts") | if length > 0 then
          "### Data & Wire Contracts", (.[] | "- `\(.id)`: \(.title)"), "" else empty end ),
      ( linked($c; "Coupling"; "couplings") | if length > 0 then
          "### Hidden Couplings (breaks at a distance)", (.[] | "- `\(.id)`: \(.title)"), "" else empty end ),
      ( linked($c; "Lesson"; "lessons") | if length > 0 then
          "### Past Agent Lessons", (.[] | "- `\(.id)`: \(.title)"), "" else empty end ),
      ( $c.checks // [] | if length > 0 then "### Fast Verification Commands", (.[] | "- `\(.)`") else empty end )
  ' "$INDEX" > "$tmp/nested.md"
  write_block "$target" "component" "$tmp/nested.md" "# Component Agent Instructions"
  if [ "${NESTED_GEMINI_MD:-0}" = "1" ] && [ ! -f "$(dirname "$target")/GEMINI.md" ]; then
    echo "@AGENTS.md" > "$(dirname "$target")/GEMINI.md"
  fi
done

# 5. Generate Antigravity Skills from ContextPacks
jq -c '.[] | select(.type == "ContextPack" and .skill.name != null)' "$INDEX" | while IFS= read -r pack; do
  name="$(jq -r '.skill.name' <<<"$pack")"
  src="$ROOT/$(jq -r '._file' <<<"$pack")"
  out="$ROOT/$SKILLS_DIR/$name/SKILL.md"
  mkdir -p "$(dirname "$out")"
  {
    echo "---"
    jq -r '"name: \(.skill.name)", "description: \(.skill.description | @json)"' <<<"$pack"
    echo "---"
    echo "<!-- Generated from ${src#"$ROOT"/} by meta/bin/build. Edit the Gold source file, not this skill. -->"
    echo
    awk 'NR==1 && $0=="---" {fm=1; next} fm && $0=="---" {fm=0; next} !fm' "$src"
    echo
    jq -r --argjson p "$pack" '
      . as $all
      | ($p.required_reading // []) as $ids
      | if ($ids | length) > 0 then "## Required Reading (`./meta/bin/ctx show <ID>`)",
          ($ids[] as $id | ($all[] | select(.id == $id) | "- `\(.id)` [\(.type)\(if .severity then ", " + .severity else "" end)]: \(.title)")) else empty end,
        "",
        ( $p.checks // [] | if length > 0 then "## Verification Commands", (.[] | "- `\(.)`") else empty end )
    ' "$INDEX"
  } > "$out"
done

# 6. Update Root AGENTS.md Generated Block & Root GEMINI.md
jq -r '
  "### P0 Invariants (Never Violate — Inspect with `./meta/bin/ctx show <ID>`)",
  ( [ .[] | select(.type == "Invariant" and .severity == "P0" and (.status // "active") == "active") ] | sort_by(.id)[]
    | "- `\(.id)` [enforced by \(.enforcement.level // "?")]: \(.title)" ),
  "",
  "### Component Routing Table",
  "| Component | Owned Code Paths | Nested Instructions |", "|---|---|---|",
  ( [ .[] | select(.type == "Component") ] | sort_by(.id)[]
    | "| `\(.id)` \(.title) | \((.anchors.paths // []) | map("`" + . + "`") | join(", ")) | `\(.agents_md // "-")` |" )
' "$INDEX" > "$tmp/root.md"
write_block "$ROOT/AGENTS.md" "root" "$tmp/root.md" "# Agent Operating Instructions & Cognitive Control Plane"
if [ "${ROOT_GEMINI_MD:-1}" = "1" ] && [ ! -f "$ROOT/GEMINI.md" ]; then
  echo "@AGENTS.md" > "$ROOT/GEMINI.md"
fi

# 7. Snapshot Test Assertion Baseline (if not yet created, or if --refresh-tests passed)
if [ ! -f "$TEST_BASELINE" ] || [ "${1:-}" = "--refresh-tests" ]; then
  : > "$TEST_BASELINE"
  while IFS= read -r f; do
    [ -z "$f" ] && continue
    if is_test_file "$f" && [ -f "$ROOT/$f" ]; then
      acount="$(grep -cE '(assert|expect|should|verify|check_)' "$ROOT/$f" 2>/dev/null || true)"
      icount="$(grep -cE '(#\[ignore|\.skip\(|@pytest\.mark\.skip|\bxit\(|\bxdescribe\(|t\.Skip\()' "$ROOT/$f" 2>/dev/null || true)"
      printf '%s\t%s\t%s\n' "$f" "$acount" "$icount" >> "$TEST_BASELINE"
    fi
  done < <(source_files)
fi

echo "build: $count Gold documents indexed; registries, graph, test baseline, nested AGENTS.md, skills ($SKILLS_DIR) and root routing updated."
```

**`meta/bin/ctx`**

```bash
#!/usr/bin/env bash
# meta/bin/ctx — deterministic context router for AI coding agents (Pure Filesystem / Zero Git).
#   ctx impact [--brief] [files...]   what to read & run before modifying files
#   ctx show <ID>                     display one Gold document + content-hash freshness check
#   ctx stamp [ID]                    compute & write verified_hash for anchored Gold doc(s)
#   ctx tests-for <ID>                tests and commands protecting an ID
#   ctx list [Type]                   list indexed Gold items
#   ctx search <text>                 search IDs, titles, summaries, and glossary
#   ctx stale                         list items whose anchored files no longer match verified_hash
source "$(dirname "${BASH_SOURCE[0]}")/lib.sh"
ensure_index

stale_status() {
  local doc="$1" recorded current paths=()
  recorded="$(jq -r '.verified_hash // empty' <<<"$doc")"
  mapfile -t paths < <(jq -r '(.anchors.paths // [])[]' <<<"$doc")
  [ "${#paths[@]}" -eq 0 ] && return 0
  if [ -z "$recorded" ]; then echo "UNSTAMPED (run ./meta/bin/ctx stamp)"; return 0; fi
  current="$(compute_anchor_hash "${paths[@]}")"
  if [ -z "$current" ]; then echo "NO-MATCHING-FILES"; return 0; fi
  if [ "$recorded" != "$current" ]; then
    echo "STALE (anchored files changed: recorded $recorded != current $current)"
  fi
  return 0
}

table() { awk -F'\t' '{ printf "%-22s %-16s", $1, $2; for (i = 3; i <= NF; i++) printf " %s", $i; print "" }'; }

cmd_stamp() {
  local target_id="${1:-}" stamped=0
  while IFS= read -r f; do
    [ -z "$f" ] && continue
    local fm id src paths=()
    fm="$(frontmatter_json "$f")"
    id="$(jq -r '.id // empty' <<<"$fm")"
    [ -z "$id" ] && continue
    if [ -n "$target_id" ] && [ "$id" != "$target_id" ]; then continue; fi
    src="$(jq -r '.source // empty' <<<"$fm")"
    mapfile -t paths < <(jq -r '(.anchors.paths // [])[]' <<<"$fm")
    [ "${#paths[@]}" -eq 0 ] && continue
    if [ "$src" = "verified" ] || [ -n "$target_id" ]; then
      local h
      h="$(compute_anchor_hash "${paths[@]}")"
      if [ -n "$h" ]; then
        yq -i --front-matter=process ".verified_hash = \"$h\"" "$f"
        stamped=$((stamped + 1))
        echo "stamped $id ($h) -> ${f#"$ROOT"/}"
      fi
    fi
  done < <(gold_files)
  "$META_DIR/bin/build" >/dev/null
  echo "stamp: updated $stamped document(s) and rebuilt index."
}

cmd_impact() {
  local brief=0 files=()
  [ "${1:-}" = "--brief" ] && { brief=1; shift; }
  if [ "$#" -gt 0 ]; then
    local f; for f in "$@"; do f="${f#"$ROOT"/}"; files+=("${f#./}"); done
  else
    mapfile -t files < <(changed_files)
  fi
  [ "${#files[@]}" -eq 0 ] && { echo "No recently modified files detected. Pass target files explicitly: ./meta/bin/ctx impact <files>"; return 0; }

  local pairs direct=() unmapped=() f id pat hit
  pairs="$(jq -r '.[] | .id as $id | (.anchors.paths // [])[] | "\($id)\t\(.)"' "$INDEX")"
  for f in "${files[@]}"; do
    [[ "$f" == meta/* ]] && continue
    hit=0
    while IFS=$'\t' read -r id pat; do
      [ -z "$id" ] && continue
      if path_matches "$f" "$pat"; then direct+=("$id"); hit=1; fi
    done <<<"$pairs"
    if [ "$hit" -eq 0 ]; then unmapped+=("$f"); fi
  done
  local direct_json unmapped_json files_json
  direct_json="$(printf '%s\n' "${direct[@]:-}" | jq -R . | jq -s 'map(select(. != "")) | unique')"
  unmapped_json="$(printf '%s\n' "${unmapped[@]:-}" | jq -R . | jq -s 'map(select(. != ""))')"
  files_json="$(printf '%s\n' "${files[@]}" | jq -R . | jq -s .)"

  local result
  result="$(jq --argjson d "$direct_json" '
    . as $all
    | [ $all[] | select(.id as $i | $d | index($i)) ] as $dd
    | [ $dd[] | (.relations // {}) | to_entries[] | select(.key != "tests") | .value[]? ] as $fwd
    | [ $all[] | select(any((.relations.components // [])[]; . as $x | $d | index($x))) | .id ] as $back
    | ($fwd + $back | unique) as $hop
    | [ $all[] | select(.id as $i | ($d | index($i)) or (($hop | index($i)) and ((.anchors.paths // []) | length) == 0)) ] as $sel
    | [ $sel[] | .id ] as $ids
    | [ $all[] | select(.id as $i | ($hop | index($i)) and ($ids | index($i) | not)) ] as $near
    | [ $all[] | select(.type == "ContextPack")
        | select((.id as $i | $ids | index($i)) or any((.required_reading // [])[]; . as $x | $ids | index($x))) ] as $packs
    | {
        docs: ($sel | sort_by(.type, .id)),
        packs: ($packs | unique_by(.id)),
        tests: ([ $sel[] | (.relations.tests // [])[] ] | unique),
        near: ($near | sort_by(.type, .id)),
        checks: ([ $sel[] | (.checks // [])[], (.enforcement.command // empty) ]
                 | reduce .[] as $x ([]; if index($x) then . else . + [$x] end)),
        p0: [ $sel[] | select(.type == "Invariant" and .severity == "P0") | .id ],
        p1: [ $sel[] | select(.type == "Invariant" and .severity == "P1") | .id ],
        ncomp: ([ $sel[] | select(.type == "Component") ] | length),
        risky: [ $sel[] | select(.type == "Contract" or .type == "Coupling" or .agent.risk == "critical") | .id ],
        high: [ $sel[] | select(.agent.risk == "high") | .id ]
      }' "$INDEX")"

  local tier reason
  if [ "$(jq '(.p0|length) + (.risky|length)' <<<"$result")" -gt 0 ] || [ "$(jq .ncomp <<<"$result")" -gt 1 ]; then
    tier="T3"; reason="P0 invariant, contract, coupling, critical item, or cross-component change"
  elif [ "$(jq '(.p1|length) + (.high|length)' <<<"$result")" -gt 0 ] || [ "$(jq length <<<"$unmapped_json")" -gt 0 ]; then
    tier="T2"; reason="P1 invariant, high-risk item, or unmapped files"
  else
    tier="T1"; reason="no high-severity metadata matched"
  fi

  if [ "$brief" -eq 1 ]; then
    echo "Risk tier $tier: $reason"
    jq -r '.docs[] | select(.type == "Invariant" or .type == "Coupling" or .type == "Contract" or .type == "Lesson")
           | "- \(.id) [\(.type)\(if .severity then " " + .severity else "" end)]: \(.title)"' <<<"$result"
    jq -r '.packs[] | "- skill/pack: \(.skill.name // .id)"' <<<"$result"
    return 0
  fi

  echo "# Impact Analysis for: $(jq -r 'join(", ")' <<<"$files_json")"
  echo
  echo "Risk Tier: **$tier** ($reason). Follow the $tier protocol in root AGENTS.md."
  echo
  local t label
  for t in Component Invariant Pattern Contract Coupling BusinessRule Flow Convention ADR TestContract Lesson; do
    case "$t" in
      Component) label="Components";;
      Invariant) label="Invariants (MUST REMAIN TRUE)";;
      Pattern) label="Canonical Implementation Patterns (Inspect Golden Exemplar)";;
      Contract) label="Data & Wire Contracts";;
      Coupling) label="Hidden Couplings";;
      BusinessRule) label="Business Rules";;
      Flow) label="Process Flows";;
      Convention) label="Coding Conventions";;
      ADR) label="Architecture Decisions (ADRs)";;
      TestContract) label="Protected Test Contracts";;
      Lesson) label="Past Agent Lessons";;
    esac
    local rows
    rows="$(jq -c --arg t "$t" '.docs[] | select(.type == $t)' <<<"$result")"
    [ -z "$rows" ] && continue
    echo "## $label"
    while IFS= read -r doc; do
      local line st
      line="$(jq -r '"- `\(.id)`\(if .severity then " [" + .severity + ", enforced by " + (.enforcement.level // "?") + "]" else "" end): \(.title)"' <<<"$doc")"
      st="$(stale_status "$doc")"
      if [ -n "$st" ]; then line="$line  ⚠ $st"; fi
      echo "$line"
    done <<<"$rows"
    echo
  done
  if [ "$(jq '.near | length' <<<"$result")" -gt 0 ]; then
    echo "## Elsewhere in the Same Component (Read if your change reaches them)"
    jq -r '.near[] | "- `\(.id)` [\(.type)\(if .severity then " " + .severity else "" end)]: \(.title)"' <<<"$result"; echo
  fi
  if [ "$(jq '.packs | length' <<<"$result")" -gt 0 ]; then
    echo "## Context Packs / Antigravity Skills"
    jq -r '.packs[] | "- `\(.id)` (skill `\(.skill.name // "-")`): \(.title)"' <<<"$result"; echo
  fi
  if [ "$(jq '.tests | length' <<<"$result")" -gt 0 ]; then
    echo "## Protecting Tests"
    jq -r '.tests[] | "- `\(.)`"' <<<"$result"; echo
  fi
  if [ "$(jq '.checks | length' <<<"$result")" -gt 0 ]; then
    echo "## Verification Commands (Run in order before finishing)"
    jq -r '.checks[] | "- `\(.)`"' <<<"$result"; echo
  fi
  if [ "$(jq length <<<"$unmapped_json")" -gt 0 ]; then
    echo "## Unmapped Files (No Metadata Found)"
    jq -r '.[] | "- `\(.)`"' <<<"$unmapped_json"
    echo "Treat as unknown risk (T2): inspect callers and tests before editing."
  fi
}

cmd_show() {
  local id="${1:?usage: ctx show <ID>}" file
  file="$(jq -r --arg id "$id" '.[] | select(.id == $id) | ._file' "$INDEX")"
  [ -z "$file" ] && die "unknown ID: $id (try: ./meta/bin/ctx search $id)"
  cat "$ROOT/$file"
  local st; st="$(stale_status "$(jq -c --arg id "$id" '.[] | select(.id == $id)' "$INDEX")")"
  [ -n "$st" ] && echo && echo "⚠ $id is $st: confirm against source code and run './meta/bin/ctx stamp $id'."
  return 0
}

cmd_tests_for() {
  local id="${1:?usage: ctx tests-for <ID>}"
  jq -r --arg id "$id" '
    [ .[] | select(.id == $id) ] as $self
    | [ .[] | select(.type == "TestContract" and any((.relations // {}) | to_entries[] | .value[]?; . == $id)) ] as $tcs
    | "Tests:", ([ ($self + $tcs)[] | (.relations.tests // [])[] ] | unique[] | "- \(.)"),
      "Commands:", ([ $self[] | (.enforcement.command // empty), (.checks // [])[] ] | unique[] | "- \(.)"),
      "Test Contracts:", ($tcs[] | "- \(.id): \(.title)")
  ' "$INDEX"
}

cmd_list() {
  jq -r --arg t "${1:-}" '.[] | select($t == "" or .type == $t)
    | "\(.id)\t\(.type)\t\(.severity // "")\t\(.title)"' "$INDEX" | table
}

cmd_search() {
  local q="${1:?usage: ctx search <text>}"
  jq -r --arg q "$q" '.[] | select([.id, .title, .summary] | map(. // "") | join(" ") | test($q; "i"))
    | "\(.id)\t\(.type)\t\(.title)"' "$INDEX" | table
  if compgen -G "$GOLD/glossary*.md" >/dev/null; then
    grep -iH -- "$q" "$GOLD"/glossary*.md | sed "s|$ROOT/||" || true
  fi
}

cmd_stale() {
  local doc st found=0
  while IFS= read -r doc; do
    st="$(stale_status "$doc")"
    if [ -n "$st" ]; then found=1; echo "$(jq -r '"\(.id)\t\(.type)\t\(.severity // "")"' <<<"$doc")	$st"; fi
  done < <(jq -c '.[]' "$INDEX")
  [ "$found" -eq 0 ] && echo "No stale items."
  return 0
}

case "${1:-}" in
  impact) shift; cmd_impact "$@";;
  show) shift; cmd_show "$@";;
  stamp) shift; cmd_stamp "$@";;
  tests-for) shift; cmd_tests_for "$@";;
  list) shift; cmd_list "$@";;
  search) shift; cmd_search "$@";;
  stale) shift; cmd_stale "$@";;
  *) sed -n '2,10p' "$0"; exit 1;;
esac
```

**`meta/bin/validate`**

```bash
#!/usr/bin/env bash
# meta/bin/validate — rebuilds generated files and runs all metadata + codebase conformance checks (Zero Git, Zero Python).
#   validate          full validation (frontmatter, links, anchors, invariants, budgets, test-weakening, stale, profiles)
#   validate --quick  fast check (frontmatter, links, anchors, invariants)
source "$(dirname "${BASH_SOURCE[0]}")/lib.sh"
mode="${1:-full}"
"$META_DIR/bin/build" >/dev/null
checks=(frontmatter links anchors invariants)
[ "$mode" != "--quick" ] && checks+=(budgets test-weakening stale)
for p in "$META_DIR"/profiles/*/check-*.sh; do [ -x "$p" ] && [ "$mode" != "--quick" ] && checks+=("$p"); done

failed=0
for c in "${checks[@]}"; do
  script="$c"; [[ "$c" != /* ]] && script="$META_DIR/checks/$c.sh"
  echo "== ${script#"$META_DIR"/}"
  "$script" || failed=$((failed + 1))
done

echo "== bash-only enforcement (no Python scripts)"
if find "$META_DIR" -type f \( -name '*.py' -o -name '*.ipynb' \) | grep -q .; then
  echo "ERROR: Python files are strictly forbidden under meta/ (use Bash *.sh only)"
  failed=$((failed + 1))
fi
if [ "${ENFORCE_REPO_NO_PYTHON:-1}" = "1" ]; then
  py_files="$(source_files | grep -E '\.(py|ipynb)$' || true)"
  if [ -n "$py_files" ]; then
    echo "ERROR: Python scripts found in repository (always use Bash *.sh over Python):"
    echo "$py_files" | sed 's/^/  /'
    failed=$((failed + 1))
  fi
fi

echo; [ "$failed" -eq 0 ] && echo "validate: OK" || echo "validate: $failed check suite(s) failed"
[ "$failed" -eq 0 ]
```

**`meta/bin/discover`**

````bash
#!/usr/bin/env bash
# meta/bin/discover — extracts deterministic Phase 0 repository facts using pure `find` (Zero Git).
# Writes meta/reports/discovery-mechanical.md.
source "$(dirname "${BASH_SOURCE[0]}")/lib.sh"
out="$META_DIR/reports/discovery-mechanical.md"; mkdir -p "$(dirname "$out")"

mapfile -t ALL_SRC < <(source_files)

{
echo "# Mechanical Discovery — Pure Filesystem ($(date -u +%Y-%m-%d))"
echo
echo "## File Extensions (via find)"
echo '```'
printf '%s\n' "${ALL_SRC[@]}" | sed -n 's/.*\.\([A-Za-z0-9]*\)$/\1/p' | sort | uniq -c | sort -rn | head -15 || true
echo '```'

echo
echo "## Build, Workspace, CI & Agent Config Files"
echo '```'
printf '%s\n' "${ALL_SRC[@]}" | grep -E '(^|/)(Cargo\.toml|package\.json|go\.mod|Makefile|justfile|Dockerfile|docker-compose[^/]*|\.github/workflows/[^/]+|clippy\.toml|deny\.toml|rust-toolchain(\.toml)?|AGENTS\.md|GEMINI\.md)$' | head -80 || true
echo '```'

echo
echo "## Schema, Wire & Migration Files"
echo '```'
printf '%s\n' "${ALL_SRC[@]}" | grep -E '\.(proto|avsc|graphql|sql|yaml|json)$|openapi|asyncapi|migrations/' | head -50 || true
echo '```'

echo
echo "## Test Directories"
echo '```'
for f in "${ALL_SRC[@]}"; do
  if is_test_file "$f"; then dirname "$f"; fi
done | sort | uniq -c | sort -rn | head -25 || true
echo '```'

echo
echo "## Largest Source Files by Line Count (Complexity Hotspots)"
echo "Invest context packs, invariants, and patterns here first."
echo '```'
for f in "${ALL_SRC[@]}"; do
  [[ "$f" =~ \.(lock|json|yaml|md|snap)$ ]] && continue
  [ -f "$ROOT/$f" ] && wc -l "$ROOT/$f"
done | sed "s|$ROOT/||" | sort -rn | head -25 || true
echo '```'

echo
echo "## Recently Modified Source Files (by Filesystem mtime)"
echo '```'
for f in "${ALL_SRC[@]}"; do
  [ -f "$ROOT/$f" ] || continue
  if stat -c '%Y %n' "$ROOT/$f" >/dev/null 2>&1; then
    stat -c '%Y %n' "$ROOT/$f"
  else
    stat -f '%m %N' "$ROOT/$f"
  fi
done | sort -rn | head -25 | awk '{ $1=""; sub(/^ /,""); print }' | sed "s|$ROOT/||" || true
echo '```'

echo
echo "## Shared Port Numbers, Env Vars & Cross-Crate References (Candidate Couplings)"
echo '```'
for f in "${ALL_SRC[@]}"; do
  [[ "$f" =~ \.(rs|sh|toml|yaml)$ ]] || continue
  grep -HnoE '(127\.0\.0\.1:[0-9]{4,5}|0\.0\.0\.0:[0-9]{4,5}|std::env::var\("[^"]+"\))' "$ROOT/$f" 2>/dev/null || true
done | sed "s|$ROOT/||" | head -40 || true
echo '```'

echo
echo "## Existing Code Anchors"
echo '```'
for f in "${ALL_SRC[@]}"; do
  grep -HoE "$ANCHOR_REGEX" "$ROOT/$f" 2>/dev/null || true
done | awk -F: '{print $2}' | sort | uniq -c | sort -rn | head -20 || true
echo '```'
} > "$out"
echo "discover: wrote ${out#"$ROOT"/}"
````

## 12.3 Checks (`meta/checks/`)

**`meta/checks/frontmatter.sh`**

```bash
#!/usr/bin/env bash
# Validates YAML frontmatter, required fields, enums, unique IDs, and prefix-to-type matching.
source "$(dirname "${BASH_SOURCE[0]}")/../bin/lib.sh"

declare -A PREFIX=( [Component]=COMP [Invariant]=INV [BusinessRule]=BR [Flow]=FLOW [ADR]=ADR
  [TestContract]=TC [Pattern]=PAT [Convention]=CONV [Coupling]=COUP [Contract]=CTR [Lesson]=LES
  [ContextPack]=PACK [ArchitectureRule]=ARCH [View]=VIEW [Glossary]=GLOSS )
declare -A SEEN=()

while IFS= read -r f; do
  [ -z "$f" ] && continue
  rel="${f#"$ROOT"/}"
  if [ "$(head -n 1 "$f")" != "---" ]; then err "$rel: missing YAML frontmatter"; continue; fi
  fm="$(frontmatter_json "$f")"
  [ "$fm" = "{}" ] && { err "$rel: frontmatter is empty or invalid YAML"; continue; }
  for k in id type title summary status source; do
    [ "$(jq -r --arg k "$k" '.[$k] // empty' <<<"$fm")" ] || err "$rel: missing '$k'"
  done
  id="$(jq -r '.id // empty' <<<"$fm")"; type="$(jq -r '.type // empty' <<<"$fm")"
  src="$(jq -r '.source // empty' <<<"$fm")"; status="$(jq -r '.status // empty' <<<"$fm")"
  if [[ -n "$src" && ! "$src" =~ ^(verified|inferred|proposed)$ ]]; then err "$rel: source '$src' must be verified|inferred|proposed"; fi
  if [[ -n "$status" && ! "$status" =~ ^(active|draft|deprecated)$ ]]; then err "$rel: status '$status' must be active|draft|deprecated"; fi
  if [ -n "$type" ]; then
    p="${PREFIX[$type]:-}"
    if [ -z "$p" ]; then err "$rel: unknown type '$type'"
    elif [[ "$id" != "$p-"* ]]; then err "$rel: id '$id' must start with '$p-' for type $type"; fi
  fi
  if [ -n "$id" ]; then
    if [ -n "${SEEN[$id]:-}" ]; then err "$rel: duplicate id '$id' (also in ${SEEN[$id]})"; fi
    SEEN[$id]="$rel"
  fi
  if [ "$src" = "verified" ] && [ "$(jq -r '(.anchors.paths // []) | length' <<<"$fm")" -gt 0 ] \
     && [ -z "$(jq -r '.verified_hash // empty' <<<"$fm")" ]; then
    err "$rel: source 'verified' with code anchors requires 'verified_hash' (run ./meta/bin/ctx stamp)"
  fi
  if [ "$src" = "proposed" ] && [ "$(jq -r '.requires_review // false' <<<"$fm")" != "true" ]; then
    warn "$rel: proposed items should set requires_review: true"
  fi
done < <(gold_files)
finish
```

**`meta/checks/links.sh`**

```bash
#!/usr/bin/env bash
# Validates that all relation IDs exist and all anchor/test path globs match real files via `find`.
source "$(dirname "${BASH_SOURCE[0]}")/../bin/lib.sh"
ensure_index
mapfile -t FILES < <(repo_files)

matches_any_file() {
  local pat="$1" f
  for f in "${FILES[@]}"; do path_matches "$f" "$pat" && return 0; done
  return 1
}

while IFS=$'\t' read -r from key to; do
  [ -z "$from" ] && continue
  jq -e --arg id "$to" 'any(.[]; .id == $id)' "$INDEX" >/dev/null || err "$from: $key -> '$to' does not exist"
done < <(jq -r '.[] | .id as $f
  | ((.relations // {}) | to_entries[] | select(.key != "tests") | .key as $k | (.value // [])[] | "\($f)\t\($k)\t\(.)"),
    ((.required_reading // [])[] | "\($f)\trequired_reading\t\(.)")' "$INDEX")

while IFS=$'\t' read -r id kind pat; do
  [ -z "$id" ] && continue
  matches_any_file "$pat" || err "$id: $kind '$pat' matches no file in repository"
done < <(jq -r '.[] | .id as $id
  | ((.anchors.paths // [])[] | "\($id)\tanchor\t\(.)"),
    ((.relations.tests // [])[] | "\($id)\ttest\t\(.)")' "$INDEX")

while IFS=$'\t' read -r id p; do
  [ -z "$id" ] && continue
  [ -d "$ROOT/$(dirname "$p")" ] || err "$id: agents_md '$p' directory does not exist"
done < <(jq -r '.[] | select(.agents_md) | "\(.id)\t\(.agents_md)"' "$INDEX")
finish
```

**`meta/checks/anchors.sh`**

```bash
#!/usr/bin/env bash
# Scans source files via `find` + `grep` to validate code anchor comments (e.g. // INV-001).
source "$(dirname "${BASH_SOURCE[0]}")/../bin/lib.sh"
ensure_index

mapfile -t SRC < <(source_files)
found=""
for f in "${SRC[@]}"; do
  [ -f "$ROOT/$f" ] || continue
  matches="$(grep -IoE "$ANCHOR_REGEX" "$ROOT/$f" 2>/dev/null || true)"
  [ -n "$matches" ] && found+="$matches"$'\n'
done
found="$(sort -u <<<"$found" | grep . || true)"

for id in $found; do
  jq -e --arg id "$id" 'any(.[]; .id == $id)' "$INDEX" >/dev/null || err "code references unknown ID '$id'"
done
for id in $(jq -r '.[] | select(.type == "Invariant" and .severity == "P0" and (.status // "active") == "active") | .id' "$INDEX"); do
  grep -qx "$id" <<<"$found" || warn "$id (P0) has no code anchor comment: add \`// $id: <summary>\` at its enforcement point"
done
finish
```

**`meta/checks/invariants.sh`**

```bash
#!/usr/bin/env bash
# Validates Invariant fields AND scans anchored non-test code (via find) for forbidden_patterns regex violations.
source "$(dirname "${BASH_SOURCE[0]}")/../bin/lib.sh"
ensure_index
LEVELS='^(type|compile_fail_test|lint|dependency_rule|property_test|test|ci_check|runtime_assertion|review_only)$'
mapfile -t ALL_SRC < <(source_files)

while IFS= read -r inv; do
  [ -z "$inv" ] && continue
  id="$(jq -r .id <<<"$inv")"; sev="$(jq -r '.severity // empty' <<<"$inv")"
  lvl="$(jq -r '.enforcement.level // empty' <<<"$inv")"; cmd="$(jq -r '.enforcement.command // empty' <<<"$inv")"
  [[ "$sev" =~ ^P[012]$ ]] || err "$id: severity must be P0, P1 or P2"
  if [ -z "$lvl" ]; then err "$id: missing enforcement.level"
  elif [[ ! "$lvl" =~ $LEVELS ]]; then err "$id: invalid enforcement.level '$lvl'"; fi
  if [ -n "$lvl" ] && [ "$lvl" != "review_only" ] && [ "$lvl" != "type" ] && [ -z "$cmd" ]; then
    err "$id: enforcement.level '$lvl' requires enforcement.command"
  fi
  if [ "$lvl" = "review_only" ] && [ "$sev" = "P0" ]; then
    warn "$id: P0 invariant is review_only; set target_level and propose a compile/lint/test mechanism"
  fi
  if [ "$(jq -r '(.forbidden // []) | length' <<<"$inv")" -eq 0 ]; then
    warn "$id: missing 'forbidden' shortcuts list"
  fi

  # Active regex enforcement across anchored non-test source files
  while IFS=$'\t' read -r pat reason; do
    [ -z "$pat" ] && continue
    while IFS= read -r apath; do
      [ -z "$apath" ] && continue
      for f in "${ALL_SRC[@]}"; do
        is_test_file "$f" && continue
        if path_matches "$f" "$apath" && [ -f "$ROOT/$f" ]; then
          if grep -nE "$pat" "$ROOT/$f" >/dev/null 2>&1; then
            err "$id: forbidden pattern '$pat' found in $f${reason:+ ($reason)}"
          fi
        fi
      done
    done < <(jq -r '(.anchors.paths // [])[]' <<<"$inv")
  done < <(jq -r '(.enforcement.forbidden_patterns // [])[] | "\(.pattern)\t\(.reason // "")"' <<<"$inv")
done < <(jq -c '.[] | select(.type == "Invariant")' "$INDEX")
finish
```

**`meta/checks/budgets.sh`**

```bash
#!/usr/bin/env bash
# Enforces line and word budgets on AGENTS.md, skills, and Gold documents using `find`.
source "$(dirname "${BASH_SOURCE[0]}")/../bin/lib.sh"
lines() { wc -l < "$1" | tr -d ' '; }
words() { wc -w < "$1" | tr -d ' '; }

if [ -f "$ROOT/AGENTS.md" ] && [ "$(lines "$ROOT/AGENTS.md")" -gt "$BUDGET_ROOT_AGENTS_LINES" ]; then
  warn "AGENTS.md: $(lines "$ROOT/AGENTS.md") lines > $BUDGET_ROOT_AGENTS_LINES"
fi
while IFS= read -r f; do
  [ -z "$f" ] || [ ! -f "$f" ] && continue
  [ "$f" = "$ROOT/AGENTS.md" ] && continue
  if [ "$(lines "$f")" -gt "$BUDGET_NESTED_AGENTS_LINES" ]; then warn "${f#"$ROOT"/}: $(lines "$f") lines > $BUDGET_NESTED_AGENTS_LINES"; fi
done < <(find "$ROOT" -name 'AGENTS.md' -type f)
if [ -d "$ROOT/$SKILLS_DIR" ]; then
  while IFS= read -r f; do
    if [ "$(words "$f")" -gt "$BUDGET_SKILL_WORDS" ]; then warn "${f#"$ROOT"/}: $(words "$f") words > $BUDGET_SKILL_WORDS"; fi
  done < <(find "$ROOT/$SKILLS_DIR" -name SKILL.md -type f)
fi
while IFS= read -r f; do
  [ -z "$f" ] && continue
  if [ "$(lines "$f")" -gt "$BUDGET_GOLD_DOC_LINES" ]; then warn "${f#"$ROOT"/}: $(lines "$f") lines > $BUDGET_GOLD_DOC_LINES"; fi
done < <(gold_files)
finish
```

**`meta/checks/test-weakening.sh`**

```bash
#!/usr/bin/env bash
# Pure-filesystem test-weakening guard (Zero Git).
# Compares current test files against meta/generated/test-baseline.tsv to detect deleted test files,
# reduced assertion counts, or newly added ignore/skip attributes.
source "$(dirname "${BASH_SOURCE[0]}")/../bin/lib.sh"
ensure_index

if [ ! -f "$TEST_BASELINE" ]; then
  info "no test baseline found; generating initial baseline via build"
  "$META_DIR/bin/build" --refresh-tests >/dev/null
  finish; exit $?
fi

while IFS=$'\t' read -r path base_assert base_ignore; do
  [ -z "$path" ] && continue
  if [ ! -f "$ROOT/$path" ]; then
    err "$path: protected test file was deleted (compared to test-baseline.tsv)"
    continue
  fi
  cur_assert="$(grep -cE '(assert|expect|should|verify|check_)' "$ROOT/$path" 2>/dev/null || true)"
  cur_ignore="$(grep -cE '(#\[ignore|\.skip\(|@pytest\.mark\.skip|\bxit\(|\bxdescribe\(|t\.Skip\()' "$ROOT/$path" 2>/dev/null || true)"
  if [ "$cur_assert" -lt "$base_assert" ]; then
    err "$path: assertion count dropped from $base_assert to $cur_assert (do not weaken tests)"
  fi
  if [ "$cur_ignore" -gt "$base_ignore" ]; then
    err "$path: skipped/ignored test count increased from $base_ignore to $cur_ignore"
  fi
done < "$TEST_BASELINE"
finish
```

**`meta/checks/stale.sh`**

```bash
#!/usr/bin/env bash
# Pure-filesystem staleness gate (Zero Git).
# Verifies that all active P0/P1 Invariants, Contracts, and Couplings match their recorded verified_hash.
source "$(dirname "${BASH_SOURCE[0]}")/../bin/lib.sh"
ensure_index

while IFS= read -r doc; do
  [ -z "$doc" ] && continue
  id="$(jq -r .id <<<"$doc")"
  recorded="$(jq -r '.verified_hash // empty' <<<"$doc")"
  mapfile -t paths < <(jq -r '(.anchors.paths // [])[]' <<<"$doc")
  [ "${#paths[@]}" -eq 0 ] && continue
  if [ -z "$recorded" ]; then
    err "$id: missing verified_hash (run ./meta/bin/ctx stamp $id)"
    continue
  fi
  current="$(compute_anchor_hash "${paths[@]}")"
  if [ "$recorded" != "$current" ]; then
    err "$id: anchored code modified since last verification ($recorded != $current). Re-verify doc and run './meta/bin/ctx stamp $id'"
  fi
done < <(jq -c '.[] | select((.type == "Invariant" and (.severity == "P0" or .severity == "P1")) or .type == "Contract" or .type == "Coupling")' "$INDEX")
finish
```

## 12.4 Rust Profile (`meta/profiles/rust/`)

Uses `find` to locate all `Cargo.toml` manifests across single- or multi-workspace repositories.

**`meta/profiles/rust/check-crate-deps.sh`**

```bash
#!/usr/bin/env bash
# Enforces ArchitectureRule.forbidden_dependencies across all Cargo manifests found via `find`.
source "$(dirname "${BASH_SOURCE[0]}")/../../bin/lib.sh"
ensure_index
need cargo

mapfile -t manifests < <(source_files | grep -E '(^|/)Cargo\.toml$' | sort -u)
[ "${#manifests[@]}" -eq 0 ] && { info "no Cargo.toml found, skipping"; finish; exit $?; }

edges=""
for m in "${manifests[@]}"; do
  md="$(cargo metadata --manifest-path "$ROOT/$m" --format-version 1 --no-deps 2>/dev/null || true)"
  [ -z "$md" ] && continue
  edges+="$(jq -r '.packages[] | .name as $n | .dependencies[] | "\($n)\t\(.name)\t\(.kind // "normal")"' <<<"$md")"$'\n'
done
edges="$(sort -u <<<"$edges")"

while IFS=$'\t' read -r rule from to reason; do
  [ -z "$rule" ] && continue
  while IFS=$'\t' read -r crate dep kind; do
    [ -z "$crate" ] && continue
    [ "$kind" = "dev" ] && continue
    if [[ "$crate" == $from && "$dep" == $to ]]; then
      err "$rule: crate '$crate' depends on forbidden crate '$dep' ($kind)${reason:+: $reason}"
    fi
  done <<<"$edges"
done < <(jq -r '.[] | select(.type == "ArchitectureRule") | .id as $id
  | (.forbidden_dependencies // [])[] | "\($id)\t\(.from)\t\(.to)\t\(.reason // "")"' "$INDEX")
finish
```

**`meta/profiles/rust/derive-components.sh`**

```bash
#!/usr/bin/env bash
# Prints Component Gold doc scaffolds for all Cargo crates in the repository found via `find`.
source "$(dirname "${BASH_SOURCE[0]}")/../../bin/lib.sh"
ensure_index
need cargo

mapfile -t manifests < <(source_files | grep -E '(^|/)Cargo\.toml$' | sort -u)
seen_pkgs=""
for m in "${manifests[@]}"; do
  md="$(cargo metadata --manifest-path "$ROOT/$m" --format-version 1 --no-deps 2>/dev/null || true)"
  [ -z "$md" ] && continue
  jq -r --arg root "$ROOT/" '.packages[] | "\(.name)\t\(.manifest_path | sub("^" + $root; "") | sub("/?Cargo\\.toml$"; ""))"' <<<"$md" |
  while IFS=$'\t' read -r name dir; do
    [ -z "$name" ] && continue
    grep -qx "$name" <<<"$seen_pkgs" && continue
    seen_pkgs+="$name"$'\n'
    [ -z "$dir" ] && dir="."
    covered="$(jq -r --arg d "$dir/" '[.[] | select(.type == "Component") | (.anchors.paths // [])[] | select(startswith($d))] | length' "$INDEX")"
    [ "$covered" -gt 0 ] && continue
    deps="$(jq -r --arg n "$name" '.packages[] | select(.name == $n) | [.dependencies[] | select(.kind == null) | .name] | join(", ")' <<<"$md")"
    h="$(compute_anchor_hash "$dir/**")"
    cat <<SCAFFOLD
---- meta/gold/components/comp-$name.md
---
id: COMP-$name
type: Component
title: $name
summary: TODO one-sentence responsibility of $name
status: active
source: inferred
requires_review: true
verified_hash: ${h:-unstamped}
anchors:
  paths: ["$dir/**"]
agents_md: $dir/AGENTS.md
checks:
  - cargo check --manifest-path $dir/Cargo.toml --all-targets
  - cargo clippy --manifest-path $dir/Cargo.toml --all-targets -- -D warnings
  - cargo test --manifest-path $dir/Cargo.toml
forbidden: []
relations: { invariants: [], patterns: [], couplings: [], contracts: [], flows: [], tests: ["$dir/tests/**"] }
agent: { risk: medium }
---
# Purpose & Boundaries
TODO

# Direct Dependencies (Verified from Cargo.toml)
$deps
SCAFFOLD
  done
done
```

# 13. Context Budgets & Minimum Target Scope

| Artifact | Budget / Target Scope |
| ----- | ----- |
| Root `AGENTS.md` | $\le 150$ lines (auto-loaded every session) |
| Nested `<component>/AGENTS.md` | $\le 80$ lines (auto-loaded when inside that component) |
| Antigravity Skill (`.agents/skills/*/SKILL.md`) | $\le 1,100$ words |
| Any Gold document (`meta/gold/**.md`) | $\le 200$ lines (aim for $\le 60$ lines) |
| `VIEW-overview` + `GLOSS-001` | 1 each (`meta/gold/overview.md`, `meta/gold/glossary.md`) |
| Components (`COMP-*`) | All workspace crates / services (typically 5–15) |
| Invariants (`INV-*`) | 10–20 P0/P1 invariants with **Type-System Anchors** & `forbidden_patterns` |
| Patterns (`PAT-*`) | 4–8 canonical implementation blueprints with **Golden Exemplar** pointers |
| Conventions (`CONV-*`) | 4–6 (errors, async, tracing, serialization, testing, Bash-over-Python) |
| Contracts (`CTR-*`) & Couplings (`COUP-*`) | 4–10 core wire entities/grants + all cross-service port/schema couplings |
| Business Rules (`BR-*`), Flows (`FLOW-*`), ADRs (`ADR-*`), Test Contracts (`TC-*`) | Critical ones (4–8 flows, 5–15 rules, 5–15 test contracts, 3–6 ADRs) |
| Context Packs (`PACK-*` $\rightarrow$ Skills) | 4–8 high-value domain/tier packs |

# 14. Phase-by-Phase Execution Plan (Zero-Git, Pure Bash)

Execute these phases sequentially from scratch. Write a concise report in `meta/reports/phase-<N>-<name>.md` at the end of each phase.

## Phase 0 — Pure-Filesystem Tooling Setup & Mechanical Discovery

1. Create the directory hierarchy in Section 3 (`meta/gold/...`, `meta/bin`, `meta/checks`, `meta/profiles/rust`, `meta/playbooks`, `meta/reports`, `meta/_bronze`).
2. Write all scripts from Section 12 verbatim and `chmod +x` them.
3. Run `./meta/bin/discover` (which uses pure `find`) to generate `meta/reports/discovery-mechanical.md`.
4. Inspect existing workspace manifests (`Cargo.toml`, `Makefile`, `scripts/*.sh`, docs) and draft `meta/gold/overview.md` (`VIEW-overview`) and `meta/gold/glossary.md` (`GLOSS-001`).
5. Write `meta/reports/phase-0-discovery.md`.

## Phase 1 — Antigravity & Gemini Delivery Surfaces

1. Create `.ignore` at root (`meta/_bronze/` and `meta/generated/`).
2. Create root `AGENTS.md` (Section 8.1) and `GEMINI.md` (`@AGENTS.md`).
3. Create `meta/playbooks/impact.md` and `meta/playbooks/review.md` (Section 9).
4. Run `./meta/bin/build` and `./meta/bin/validate` to verify the pure-Bash pipeline works end-to-end.
5. Write `meta/reports/phase-1-delivery-surfaces.md`.

## Phase 2 — Components, Architecture DAG Rules & Conventions

1. Run `./meta/profiles/rust/derive-components.sh` to discover all Cargo crates via `find`.
2. Create `meta/gold/components/comp-*.md` for each service/crate with accurate `anchors.paths`, `agents_md`, `forbidden` responsibilities, concurrency rules, Traps, and fast `checks`.
3. Create `meta/gold/architecture/arch-*.md` (including `forbidden_dependencies`) and key architecture views.
4. Create `meta/gold/conventions/conv-*.md` (error handling, async/Tokio, observability, serialization, and `conv-bash-only.md` mandating Bash over Python).
5. Run `./meta/bin/ctx stamp`, `./meta/bin/build`, and `./meta/profiles/rust/check-crate-deps.sh`.
6. Write `meta/reports/phase-2-components-architecture.md`.

## Phase 3 — Type-Anchored Invariants, Wire Contracts & Couplings (HUMAN REVIEW GATE)

1. Sweep the codebase using `find` and `grep` following the Rust & Distributed Agentic Systems Profile (Section 6.2).
2. Create `meta/gold/invariants/inv-*.md` for all P0 and P1 invariants. Every invariant **must** include its `enforcement` ladder block, `# Type-System & Compile-Time Anchors`, `# Forbidden Shortcuts`, and (where applicable) `forbidden_patterns` regexes.
3. Create `meta/gold/contracts/ctr-*.md` for core domain types, signed capability grants, and wire schemas.
4. Create `meta/gold/couplings/coup-*.md` for port topologies, cross-service route tables, and shared schemas.
5. Add concise `// INV-xxx: ...` code-anchor comments at the enforcement points in the Rust source files for all P0 invariants.
6. Run `./meta/bin/ctx stamp` and `./meta/bin/validate`. Write `meta/reports/phase-3-invariants.md` (including concrete enforcement upgrade proposals for any `review_only` invariant).
7. **Pause and ask the user to review Phase 3** (unless instructed to run unattended).

## Phase 4 — Canonical Implementation Patterns & Lessons

1. Identify the 4–8 most important ways engineers extend this codebase (e.g., adding a governed tool route, implementing an agent policy evaluator, compiling mapping IR, emitting SIEM audit events, defining crate error types).
2. Create `meta/gold/patterns/pat-*.md` for each, pointing to the single cleanest **Golden Exemplar** file in the codebase and providing a 20–40 line idiomatic Rust skeleton.
3. Create `meta/gold/lessons/les-*.md` documenting critical architectural traps and how they are prevented.
4. Complete `meta/gold/glossary.md` (`GLOSS-001`) mapping domain terms to exact Rust struct/enum paths.
5. Run `./meta/bin/ctx stamp` and write `meta/reports/phase-4-patterns-lessons.md`.

## Phase 5 — Business Rules, Critical Flows, ADRs & Test Contracts

1. Create `meta/gold/rules/br-*.md` with concrete valid and invalid examples.
2. Create `meta/gold/flows/flow-*.md` for the critical multi-tier flows, including Mermaid sequence diagrams and `# Concurrency, Timeouts & Failure Semantics`.
3. Create `meta/gold/adr/adr-*.md` for core architectural decisions and rejected alternatives.
4. Create `meta/gold/test-contracts/tc-*.md` for the conformance and security test suites, specifying exact run commands and anti-tampering rules.
5. Run `./meta/bin/ctx stamp` and write `meta/reports/phase-5-rules-flows-tests.md`.

## Phase 6 — Context Packs & Antigravity Skills Generation

1. Create `meta/gold/packs/pack-*.md` for the 4–8 main functional/security domains, each with a sharp `skill.name` and `skill.description` ("Use when modifying...").
2. Run `./meta/bin/build` to generate `.agents/skills/<name>/SKILL.md` and update all nested `<component>/AGENTS.md` files.
3. Write `meta/reports/phase-6-packs-skills.md`.

## Phase 7 — Full Validation & Context Router Dry Runs

1. Run `./meta/bin/ctx stamp` and `./meta/bin/build --refresh-tests`.
2. Run `./meta/bin/validate` and fix any frontmatter, broken link, missing code anchor, forbidden pattern, crate dependency, staleness, or budget issues until `0 error(s)` is achieved.
3. Run `./meta/bin/ctx impact` across at least 6 representative source files from different crates to verify that every file resolves to its Component, Invariants, Pattern, Contracts, and Test Commands with zero unmapped gaps.
4. Write `meta/reports/phase-7-validation.md`.

## Phase 8 — Final Maintainer Guide & Synthesis

1. Write `meta/README.md` explaining how humans and Antigravity/Gemini agents use and maintain the knowledge base (`./meta/bin/ctx`, `./meta/bin/ctx stamp`, `./meta/bin/build`, `./meta/bin/validate`).
2. Write `meta/reports/final-summary.md` summarizing all generated artifacts, enforcement ladder coverage, and any `requires_review: true` items.