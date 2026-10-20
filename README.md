# System Design Skill

An agent skill that makes an AI coding agent work through system design the way a senior engineer works with a client: it clarifies the requirements, gets them confirmed, then produces a complete design with scale math, failure modes, trade-offs, and a verification plan.

It works with Claude Code and any agent that reads `SKILL.md` skills.

## What it does

**Design mode**: for "how would you build X".
1. **Clarify**: reads the repo first, so it asks only what the code can't answer. It asks the 2 or 3 questions that reshape the architecture, then one at a time, with a recommended default for each and 7 questions at most.
2. **Confirm**: one summary of what it understood, its labeled assumptions, and any conflicts. Design starts only when you say go.
3. **Design**: requirements → scale estimate → interfaces → high-level design → deep dives → failure modes → trade-offs → verification.
4. **Self-review**: runs a checklist and fixes every gap before showing you the design.

**Review mode**: for an existing repo, design doc, or diagram. It rebuilds the current design from the code, checks scale, ownership, failures, and trade-offs, and reports the gaps ranked by impact, each with a fix and its cost.

It sizes the work to the task: a single feature gets the five steps it needs, and a whole system gets all eight.

Every diagram is a drawn picture embedded in the doc: an architecture diagram, a class diagram of the data model, and a sequence diagram for each hard part. They're SVG files, so they show up in GitHub, VS Code, and any Markdown preview, with no diagram code to paste anywhere.

## Install

### Claude Code plugin
```
/plugin marketplace add huongstienstra/system-design-skill
/plugin install system-design@system-design-skill
```

### Copy the skill
```bash
git clone https://github.com/huongstienstra/system-design-skill.git
cp -r system-design-skill/skills/system-design ~/.claude/skills/
```

For one project only, copy it into that project's `.claude/skills/` instead.

## Use

Ask in plain words. The skill loads on its own:

- "Design a group chat app for 50k users."
- "How would you build an offline-first notes app?"
- "Review the architecture of this repo."
- "Design the favorites feature for our feed screen."

To skip the questions, say "just design it". The agent lists its assumptions at the top and continues.

## What's inside

```
skills/system-design/
├── SKILL.md                 the process: modes, phases, steps
├── domains/                 one guide per system type
│   ├── backend.md
│   ├── mobile-client.md
│   ├── data-pipeline.md
│   ├── realtime.md
│   └── ai-feature.md
├── checklists/review.md     self-review before presenting
├── templates/
│   ├── design-doc.md        the output shape
│   └── diagrams.md          how to draw the SVG diagrams
└── examples/
    ├── newsflow.md          a finished design
    └── diagrams/            its architecture, class, and sequence diagrams
```

Each domain guide has three parts: ranked **questions** with defaults, the **scale dimensions** that matter for that kind of system, and its **usual failures**. The agent loads only the guides that match the task.

## Example

[`examples/newsflow.md`](skills/system-design/examples/newsflow.md) is the full design for [NewsFlow](https://github.com/huongstienstra/newsflow-system-design), an offline-first Android news reader. It shows what the skill produces: drawn architecture, class, and sequence diagrams, the scale math that finds the real hard part (a shared third-party API quota, not traffic), the cache rules that keep favorites safe across refreshes, a failure table, trade-offs with their costs, and tests mapped to each risk.

## Add a domain

Copy any file in `domains/`, keep its three sections, and add a row for it to the table in `SKILL.md`.

## License

MIT
