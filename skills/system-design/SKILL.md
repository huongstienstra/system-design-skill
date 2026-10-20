---
name: system-design
description: System design for a new system, app, service, feature, or data pipeline ("how would you build X", high-level design, architecture), or a review of an existing architecture, codebase, or design doc.
---

# System Design

Work as a senior engineer with a client. The client confirms the requirements before the design starts.

## Pick the mode and the size

Mode:
- **Design**: something new. Run Phase 1, then Phase 2.
- **Review**: something that exists (a repo, a design doc, a diagram). Run [Review](#review).

Size:
- **Feature** (one screen, endpoint, job, or flow inside an existing system): Steps 1, 3, 4, 6, 7.
- **System** (a new product, service, or pipeline): all eight steps.

Domain guides live in `domains/`. Load every guide that matches; an end-to-end system loads its client guide and `backend.md`.

| System type | Guide |
|---|---|
| API, service, database, storage | `domains/backend.md` |
| Mobile or web app | `domains/mobile-client.md` (web: skip OS and store items) |
| ETL, analytics, reporting, events | `domains/data-pipeline.md` |
| Chat, live feed, presence, notifications | `domains/realtime.md` |
| LLM, model, or AI feature | `domains/ai-feature.md` |

Each guide has three parts: **Questions** (Phase 1), **Scale dimensions** (Step 2), and **Usual failures** (Step 6).

## Phase 1: Clarify

### A. Discover
Read before asking. In a repo, read the README, build and dependency files, config, infrastructure files, and existing docs. Note the stack, cloud, patterns already in use, and hard constraints.

Done when you can list which guide questions the code already answers. Those questions are never asked.

### B. Ask
1. If the system type is unclear, ask it first.
2. Re-rank the guide's questions for this prompt. Anything that reshapes the whole architecture goes first: money, compliance, safety, data loss.
3. First round: the top 2 or 3 architecture-shaping questions together, each with a recommended default.
4. Then one question at a time. After each answer, say in one line what it changes, and drop the questions it made irrelevant.
5. Stop at 7 questions in total. Defaults cover the rest.

When an ask tool is available, use it, with the recommended default as the first option.

### C. Confirm
Send one message:
- **What I understood**: short bullets.
- **Assumptions**: each labeled "Assumption".
- **Conflicts**: each flagged ⚠️.
- Close with: "Is this correct? Anything to add or change: features, users, deadlines, budget, or tech you must use or avoid?"

A correction or new information updates the summary, and you confirm again. Phase 2 starts when the client says go.

**Unattended** (running as a subagent, no way to reach the client, or the client said "just design it"): skip B and C, list every assumption at the top of the design, and continue.

## Phase 2: Design

Run the steps in order without further questions. When something is unknown, assume it, label it "Assumption", and continue.

Write the result in the shape of `templates/design-doc.md`. In a repo, save it as `docs/design/<name>.md`, with its diagrams in `docs/design/diagrams/`; without a repo, use `design/<name>.md` and `design/diagrams/` in the working folder. `examples/newsflow.md` is a finished example; read it when unsure of depth or format.

Every diagram is a drawn picture: an SVG file embedded with `![<what it shows>](diagrams/<name>-<kind>.svg)`, so the reader sees it right in the doc. Draw each one by following `templates/diagrams.md`.

### Step 1: Requirements
- Functional: what users can do.
- Non-functional: latency, availability, consistency, security, compliance, cost, deadline.
- Out of scope.

### Step 2: Scale estimate
- Use the guide's scale dimensions. Show the math with units, average and peak.
- End with: "The hard part of this system is ___."

### Step 3: Interfaces
- APIs and contracts between parts.
- The data model as a class diagram: each entity with its fields, keys, and relations.
- Mark every call that must be retry-safe, and how (idempotency key, upsert, natural key).
- End-to-end: the client-server contract: pagination, versioning, who retries, what is cached where and for how long.

### Step 4: High-level design
- An architecture diagram.
- Every piece of state has exactly one owner; name it.
- Every component has a stated reason to exist.

### Step 5: Deep dives
- The 2 or 3 hard parts named in Step 2, each with a sequence diagram and the rule that keeps it correct.

### Step 6: Failure modes
- A table: component or dependency | slow | down | wrong data | what the user sees. Start from the guide's usual failures.
- Security and privacy risks, each with its mitigation.

### Step 7: Trade-offs
- Each major choice: options, decision, reason, cost.
- Build vs buy, always.

### Step 8: Verification
- Tests, metrics, and drills. Every hard part from Step 2 maps to at least one.

## Review

1. Discover (Phase 1 A), then rebuild the current design: components, state owners, data flow, external dependencies. Draw its architecture and class diagrams.
2. Infer the requirements from the product, each labeled "Assumption". Confirm them with the client (Phase 1 C) unless unattended.
3. Run Steps 2, 4, 6, and 7 against what exists. The guide's usual failures are the starting list.
4. Run `checklists/review.md` against the existing design.
5. Report what is sound and why, then the gaps ranked by impact, each with a concrete fix and its cost. Cite code as `path:line`.

## Before presenting

Run `checklists/review.md` and fix every "no". End with the open risks and what would make you change the design.

## Rules
- The simplest design that meets the requirements wins.
- Every scale claim carries numbers.
- Client constraints (cloud, stack, deadline, budget) beat personal preference.
