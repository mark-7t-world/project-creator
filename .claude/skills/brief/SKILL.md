---
name: brief
description: >
  Use at session start for quick orientation. Reads current companion state,
  synthesizes lifecycle phase and gaps, recommends what to work on next.
  Lightweight single skill — no agent dispatch needed.
disable-model-invocation: true
argument-hint: "[client/companion]"
---

# /brief — Session Orientation

Quick session start for Project Creator. Reads the current companion's state, synthesizes where it
is in the lifecycle, and recommends what to do this session.

**Usage:** `/brief` or `/brief [client/companion]`

---

## Step 1: Determine the Companion

1. If `$ARGUMENTS` contains a companion path, use that
2. Otherwise, read `tracking/current-companion.md` for the current companion
3. If no companion is set:
   ```
   No active companion. Run `/companion` to set one, or `/companion new [client/companion]` to create one.
   ```
   **Stop here.**

Resolve the companion path: `companions/{client}/{companion}/`

Declare:
```
Companion: {client}/{companion}
Path: {resolved path}
```

---

## Step 2: Read Current State

Read these files in order. Note which exist and which don't.

### Project Creator tracking (always read):
1. `tracking/projects-log.md` — find the row for this companion (status, last session date, notes)
2. `tracking/patterns-discovered.md` — recent learnings relevant to this companion type

### Companion state (read if they exist):
3. `companions/{client}/{companion}/HANDOFF.md` — where we left off last session
4. `companions/{client}/{companion}/context/questions.md` — open questions
5. `companions/{client}/{companion}/context/decisions.md` — decisions made (count and scan recent)
6. `companions/{client}/{companion}/context/requirements.md` — requirements captured (scan for completeness)
7. `companions/{client}/{companion}/context/constraints.md` — constraints identified

### Build state (read if companion is in cultivation or shaping):
8. `companions/{client}/{companion}/docs/plans/tickets.yaml` — ticket status
9. `companions/{client}/{companion}/docs/plans/build-progress.md` — build execution state

Declare:
```
Files read:
- [file]: [exists/missing] — [key finding]
- ...

Lifecycle phase: [seeding / cultivation / shaping / complete]
Last session: [date or "unknown"]
```

---

## Step 3: Synthesize

Produce a brief orientation based on the state files. Focus on three things:

### Where are we?
- Lifecycle phase (seeding, cultivation, shaping)
- Seeding completeness (rough % based on context file coverage)
- If cultivation: are tickets ready? Is there a spec?
- If shaping: how many tickets done vs remaining?

### What happened last?
- From HANDOFF.md or projects-log last session notes
- Any dangling threads or blockers

### What needs attention?
- Open questions that block progress (from questions.md)
- Missing context files (no constraints.md? no decisions.md?)
- Phase transition readiness (enough context for /plan? tickets ready for /build?)

---

## Step 4: Recommend and Ask

Present the brief in this format (stay under 15 lines):

```
## Session Brief — {client}/{companion}

**Phase:** {seeding/cultivation/shaping} — {one-line status}

**Last session:** {date} — {what happened}

**State:**
- {key state observation 1}
- {key state observation 2}
- {key state observation 3}

**Recommendation:** {What this session should focus on — 2-3 lines with reasoning. Reference specific commands.}

**Blockers:** {External dependencies or open questions that block progress. "None" if clear.}
```

Then:

```
What would you like to work on?
```

**Wait for the user to choose direction. Do not proceed autonomously.**

---

## What This Skill Does NOT Do

- It does not run `/gaps` (that's a full standards audit)
- It does not process inputs or do reverse prompting (that's `/intake`, `/process`)
- It does not create plans or build anything (that's `/plan`, `/build`)
- It orients. That's all. But orientation prevents wasted sessions.
