# Generic Rust Workspace Knowledge Generation Script (v4.0)

> An executable cognitive control plane and structured knowledge generator for AI coding agents working on existing, multi-crate Rust workspaces.

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Bash Only](https://img.shields.io/badge/Tooling-Pure%20Bash-green.svg)](#)
[![Zero Git](https://img.shields.io/badge/Dependency-Zero%20Git-orange.svg)](#)

---

## ⚡ The Problem

When coding agents (Claude, Gemini, OpenAI) work on mature Rust workspaces, they often take the shortest path to a green compilation:
- Widening visibility (`pub(crate)` $\rightarrow$ `pub`)
- Bypassing domain typestates and parsing raw `serde_json::Value`
- Rewriting existing idiomatic patterns into ad-hoc shortcuts
- Weakening test assertions when edge cases break

**Human documentation doesn't solve this.** Agents read under task pressure and need negative constraints, type-system anchors, and deterministic routing rather than narrative tutorials.

---

## 🛠️ The Solution: Medallion Architecture + Open Knowledge Format

This single instruction package transforms your repository into an agent-ready environment without touching application runtime code:

```text
Existing Crates & Manifests
   │  (Scanned via pure `find` — zero git dependency)
   ▼
Bronze (meta/_bronze/)      --> Raw notes & legacy evidence (isolated via .ignore)
   │
   ▼
Gold (meta/gold/**.md)      --> Single source of truth: Invariants, Patterns, Contracts
   │  ./meta/bin/build
   ▼
Delivery Surfaces (Auto-loaded by agents)
   ├─ Root AGENTS.md & GEMINI.md         (Protocol + Anti-Simplification Covenant)
   ├─ <crate>/AGENTS.md                  (Local boundaries + fast cargo commands)
   ├─ .agents/skills/<name>/SKILL.md     (Antigravity on-demand skills)
   ├─ Code Anchors // INV-001: ...       (Direct inline reminders at enforcement points)
   └─ ./meta/bin/ctx impact <file>       (1-command deterministic context router)
```

### Key Pillars:
1. **Invariants at the Center (`INV-*`):** Every invariant binds a non-negotiable rule to exact Rust types, private constructors, forbidden regexes, and test commands.
2. **Golden Exemplars (`PAT-*`):** Points to the cleanest existing files in your repository so agents mimic your established conventions instead of guessing.
3. **Deterministic Context Router (`./meta/bin/ctx impact`):** Resolves the exact context, risk tier (`T1`/`T2`/`T3`), and `cargo` commands in sub-second time.
4. **Pure Filesystem (Zero Git):** Works in air-gapped sandboxes, CI, or raw folder exports using `find` and content hashes (`verified_hash`).
5. **Strictly Bash-Only:** All tooling is written in portable `*.sh`. No Python required or permitted.

---

## 🚀 Quick Start

1. Copy [`ai_agent_knowledge_base_builder_v4_0.md`](./ai_agent_knowledge_base_builder_v4_0.md) into the root of your existing Rust workspace.
2. Prompt your coding agent (Antigravity, Gemini CLI, Claude Code, Cursor):

```text
Read ai_agent_knowledge_base_builder_v4_0.md and execute the methodology phase by phase on this workspace. Pause at Phase 3 for human review.
```

3. Review the extracted invariants in `meta/reports/phase-3-invariants.md`.
4. Let the agent complete the remaining phases.

Once built, future coding sessions simply run:
```bash
./meta/bin/ctx impact path/to/changed_file.rs
```

---

## 🎯 Target Scope & What's Next

- **Current Scope:** Designed specifically for **already-existing, mature Rust repositories** to preserve architectural integrity, coherence, and stability.
- **Next Step:** Greenfield initialization—allowing organizations to bootstrap brand-new repositories with mandatory architectural blueprints and seed invariants before code is written.

---

## 💬 Community & Feedback

Read the full background story on [dev.to](https://dev.to) *(link your article here)*.

Issues, suggestions, and feedback on invariant extraction and context routing are welcome!