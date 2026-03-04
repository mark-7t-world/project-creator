# Capability: Companion Guide

Every companion must have a `companion-guide.md` at its project root. This is the operational playbook — what to do, when, and which commands to use. It is both a human reference and a workbench-rendered document.

---

## What It Is

The companion guide is the single document that answers: "How do I use this companion?" It bridges the gap between CLAUDE.md (which tells Claude how to behave) and the user's need to know what commands exist, when to use them, and how sessions flow.

In the Companion Workbench, the guide renders in the right pane alongside the working session. This means the **top of the document is what the user sees first** — and it should be the most useful content for mid-session reference.

**Without a companion guide:** Users discover commands through trial and error, forget parameters, and can't see the overall workflow. New sessions start with "what can I do?" instead of productive work.

**With a companion guide:** Commands and parameters are immediately visible in the workbench pane. Session flow is clear. The user knows which command to reach for in each situation.

---

## The Standard Structure

Every companion guide follows this section order. Required sections appear in every guide. Optional sections appear when the companion's domain warrants them.

### Required Sections

| # | Section | Purpose | Workbench Value |
|---|---------|---------|-----------------|
| 1 | **Title + Purpose** | One-line identification and operational framing | Confirms the user opened the right companion |
| 2 | **Command Quick Reference** | All commands with full parameter signatures, grouped by activity | **Primary workbench content** — visible in the right pane during work |
| 3 | **Session Lifecycle** | Mermaid flowchart showing the full session arc | Visual orientation for new and returning users |
| 4 | **What the loop looks like in practice** | 5-7 concrete session examples | Grounds the flowchart in real usage patterns |
| 5 | **Core Workflow** | How the companion organizes work (phases, activities, or modes) | Explains the "why" behind command groupings |
| 6 | **When to Use Which Command** | Situation-driven tables: "When X happens, use Y" | Decision support — the user looks up their situation, finds the command |
| 7 | **Agents** | Three-layer architecture diagram + agent table | Explains what happens behind the scenes when commands dispatch work |
| 8 | **Key Files** | File reference table with purpose and when-needed columns | Quick lookup for important project files |
| 9 | **Cross-Project Relationships** | How this companion connects to other companions/projects | Prevents confusion about scope and handoff points |
| 10 | **Key Principles** | 5-8 concise governing statements | The companion's philosophy in scannable form |
| 11 | **Last Updated** | Date stamp | Currency signal |

### Optional Sections

Include these when the companion's domain warrants them. Insert after the Core Workflow section and before Agents.

| Section | When to Include | Examples |
|---------|----------------|---------|
| **The [Companion] Voice / Named Behaviors** | Companion has a distinct personality or behavioral modes | PM with Skeptic/Connector/Narrower behaviors; Architect with peer-level co-design |
| **The Governing Principle** | One statement captures the companion's essence | "Context quality = convergence"; "Draw out, don't generate" |
| **Authority Boundaries** | Companion operates in a hierarchy with other companions | SE/Architect/PM boundaries; what this companion owns vs. doesn't |
| **Knowledge Domains** | Companion maintains a reference library organized by domain | Architect's four domains; PM's three pillars |
| **Development Workflows** | Companion orchestrates multi-phase builds | Floor cycles, PR group management, handoff ceremonies |
| **Blocking Protocol** | Companion has structured escalation when stuck | SE's five-field BLOCKED escalation |
| **Strategy Anchor / Prioritization Lens** | Companion's work is governed by a strategic artifact | PM's hypothesis file; success map as prioritization lens |
| **Technology Stack** | Companion is an implementation companion with specific tech constraints | Electron, TypeScript strict, node-pty |

---

## Why Command Quick Reference Goes First

The workbench renders companion-guide.md in a right pane alongside the working session. The top of the document is what the user sees without scrolling.

During active work, the most common need is: "What's the command for this? What are its parameters?" Not lifecycle diagrams, not principles — commands and parameters.

By placing the command quick reference immediately after the title, the user always has their command cheat sheet visible in the workbench pane. Everything else — context, diagrams, philosophy — lives below the fold for when the user wants to understand the system more deeply.

---

## Command Quick Reference Standards

The quick reference section has specific formatting requirements:

### Grouping

Commands are grouped by activity type, not alphabetically. Groups match the companion's workflow phases or activity categories.

```
### [Activity Group Name]
```

### Parameter Format

Every command variant gets its own line. Show the full parameter signature.

```
/command                                          Brief description
/command [required-param]                         Description with param
/command [required-param] [optional-param]        Full signature
/command --flag [param]                           Flag variant
```

### Alignment

Descriptions are right-aligned to a consistent column. Use spaces (not tabs) for alignment. The column position should be consistent within each group.

### What Counts as a Variant

A variant is any distinct combination of parameters that changes behavior:
- Different parameter counts: `/gaps` vs `/gaps [client/companion]`
- Different parameter types: `/intake` vs `/intake [persona]` vs `/intake [persona] [client/companion]`
- Flag modes: `/read-book [url]` vs `/read-book --library [org] [url]`

Do NOT list:
- Internal aliases or shortcuts
- Parameters that are always the same (e.g., if every command accepts `[client/companion]`, you can note this once at the top of the section instead of repeating for every command)

---

## Session Lifecycle Standards

Every companion guide includes a Mermaid flowchart showing the session arc.

### Required Elements

- **Entry point**: How the session starts (open Claude Code, run orientation command)
- **Decision nodes**: What kind of work needs to happen (diamond shapes)
- **Command nodes**: Which command handles each situation (with brief description)
- **Loop structure**: How the session cycles through multiple commands
- **Exit point**: How the session ends (checkpoint/capture command)

### Style Conventions

- Use `flowchart TD` (top-down) for session lifecycles
- Use `stateDiagram-v2` for phase/state transitions
- Command names in node labels: `/brief\nSession orientation`
- Edge labels describe the trigger: `"Architecture decision needed"`
- Keep to one primary flowchart; add secondary diagrams for specific workflows

---

## "What the Loop Looks Like in Practice" Standards

This section grounds the flowchart in concrete reality. Include 5-7 examples showing actual session patterns.

### Format

Each example is a single bullet showing a command sequence with brief context:

```
- `/brief` then `/spec companion-workbench` to ingest a new PM spec, then `/checkpoint`
- `/brief` then `/design` a technology choice, then `/checkpoint`
```

### What Makes Good Examples

- **Varied**: Cover different paths through the flowchart
- **Concrete**: Name actual commands, not abstract activities
- **Brief**: One line per example — the detail lives in the command reference
- **Realistic**: Show the 2-4 command sessions that actually happen, not theoretical 10-command marathons

---

## When to Use

Every companion. No exceptions. A companion without a guide is a companion that gets underused.

| Companion Type | Guide Complexity | Notes |
|----------------|-----------------|-------|
| PM / Strategic | Heavy | Many commands, multiple workflow paths, named behaviors, strategy anchors |
| Architect | Heavy | Build flows, code review flows, knowledge refresh, cross-companion integration |
| SE / Developer | Medium | Ticket lifecycle, blocking protocol, authority boundaries |
| Writer / Creative | Medium | Session flow, capture/synthesis cycles, mentor framework |
| Tracker / Operational | Medium | Daily/weekly rhythms, email command variants, success map lifecycle |

---

## Relationship to Other Configuration

| Artifact | Audience | Relationship to Companion Guide |
|----------|----------|-------------------------------|
| **CLAUDE.md** | Claude (the model) | Tells Claude how to behave. The guide tells the user how to interact. They share vocabulary but serve different readers. |
| **companion-guide.md** | The user (human) | The operational playbook. Rendered in the workbench. Primary reference during sessions. |
| **Skill SKILL.md files** | Claude (when invoked) | Detailed procedure for each command. The guide summarizes what each command does; the skill defines how. |
| **README.md** | External humans | If present, covers setup/installation. The guide covers usage. Most companions don't need a README. |

---

## Anti-Patterns

| Anti-Pattern | Problem | Better Approach |
|--------------|---------|-----------------|
| No companion guide | User doesn't know what commands exist or when to use them | Every companion gets a guide — it's a required artifact |
| Commands without parameters | User can't tell how to invoke commands correctly | Show every parameter variant with its own line |
| Alphabetical command listing | Doesn't help the user who thinks in situations ("I just had a meeting, now what?") | Group by activity type; add situation-driven tables |
| Lifecycle diagram without practice examples | Abstract flowcharts don't teach usage patterns | Always pair the diagram with "what the loop looks like in practice" |
| Quick reference buried below diagrams | User has to scroll past context they don't need mid-session | Quick reference goes immediately after the title |
| Describing commands without linking to workflow | User knows what a command does but not when to use it | Both the quick reference AND the situation tables are needed |
| Copy-pasting CLAUDE.md into the guide | Different audiences, different purposes — CLAUDE.md tells Claude how to behave; the guide tells the user how to interact | Write the guide for the human; reference CLAUDE.md concepts but don't duplicate |
| Missing Last Updated date | No currency signal; user doesn't know if the guide reflects current capabilities | Always include the date; update it when commands change |
