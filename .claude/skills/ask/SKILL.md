---
name: ask
description: Compose a structured message from this companion to another companion in the ecosystem. Use when the current conversation has produced insights, decisions, or requests that another companion needs to act on. Outputs the message in conversation for copy-paste (v1). Future: direct routing with session support.
disable-model-invocation: true
argument-hint: "companion-name [optional: topic or question]"
---

# /ask — Cross-Companion Communication

Compose a structured message from this companion to another companion. The message captures
this companion's perspective, expertise, and analysis for the target companion to consume.

**Current:** Outputs in conversation for copy-paste.
**Future:** `/ask [companion] [session #]` routes directly via communication fabric.

## Argument: $ARGUMENTS

The target companion name and optional topic/question.

Examples:
```
/ask client-tracker success map framing for Neycher
/ask software-architect-companion workbench Floor 1.2 requirements
/ask se-companion-workbench bug in session state detection
```

---

## Step 1: Resolve the Target Companion

Search for the companion by name. Resolution order:

1. **Scan all companions** — use the Glob tool to find all companion CLAUDE.md files:
   Pattern: `**/CLAUDE.md` with path `/Users/sonjayatandon/dev/project-creator/companions`
   The parent directory of each match is a companion.
2. **Exact path** — if the argument looks like a path, use it directly.

Match `$ARGUMENTS[0]` against directory names. Partial matches are fine (`client-tracker` matches `client-tracker`, `tracker` matches `client-tracker`).

**If no match found:**
```
Could not resolve companion "$ARGUMENTS[0]".

Available companions in this ecosystem:
[list from resolution step 1]

All known companions:
[list from resolution step 2]
```
**Stop here.**

**If match found**, read the target companion's CLAUDE.md (first 50 lines — identity and role section).

Declare:
```
Target: [companion name]
Path: [resolved path]
Role: [one-line summary from their CLAUDE.md]
```

---

## Step 2: Understand What to Communicate

The message content comes from the **current conversation context**. The skill does NOT
read files or do research — the thinking already happened in this session.

Scan the current conversation for:
1. **What was discussed** — the topic, client, decision, or analysis
2. **What the target companion needs to do** — generate a deliverable, update a file, process information
3. **What perspective this companion is adding** — strategic framing, constraints, language guidance, signals

If the user provided a topic in `$ARGUMENTS` (everything after the companion name), use that as the focal point.

If the conversation context is ambiguous about what to communicate, ask ONE question:

```
What should [target companion] do with this? For example:
- Generate a success map for [client]
- Update the client profile with these insights
- Create tickets for these changes
- [other]
```

**Do not proceed until the intent is clear.**

---

## Step 3: Gather Supporting Context

Read files that give the target companion what it needs to act. Read ONLY what's relevant — not everything.

**Always read:**
- This companion's `tracking/hypothesis.md` (decision lens)

**Read if relevant to the message:**
- Client profile at `/Users/sonjayatandon/ConsortiumTeam_ClientFiles/ActiveClients/[client]/CLIENT_PROFILE.md`
- This companion's `tracking/opportunity-tree.md` (if the message connects to an opportunity)
- This companion's `tracking/insights-log.md` (if referencing specific insights)
- The target companion's relevant templates (if the message requests a specific deliverable)

**Do NOT read:**
- The full conversation history (you already have it)
- Files the target companion will read on its own (e.g., Granola transcripts — reference them by ID, don't paste them)

---

## Step 4: Compose the Message

Output the message directly in the conversation using this structure:

```
# Handoff: [This Companion Name] → [Target Companion Name]
# Re: [Topic — one line]

**Date:** [today's date]
**From:** [this companion's name and role]
**To:** [target companion's name and role]
**Source:** [what produced this — conversation topic, Granola meeting ID, session reference]

---

## What This Is

[1-2 sentences: what the target companion should do with this message.
Be specific — "generate a success map" not "process this information."]

---

## The Core Read

[The insight, analysis, or framing that this companion produced.
This is the substance — the Problem A→B, the strategic reframe,
the decision, the constraint. This section can be long if the
thinking was substantial. Use the structure that fits the content:
narrative for strategic framing, tables for signals, lists for
action items.]

---

## What the Target Companion Needs to Focus On

[Specific guidance for the target companion's work.
What to emphasize, what to avoid, what constraints to honor.
This is where THIS companion's expertise shapes the OTHER
companion's output.]

---

## Language and Tone Guidance

[If the output will be client-facing or externally visible:
what will land, what won't, communication style notes.
OMIT this section if the message is purely internal.]

---

## Key Signals

[Table or list of specific moments, quotes, or data points
that should inform the target companion's work.
OMIT if there are no specific signals to flag.]

---

## Context the Target Companion Should Read

[Explicit file paths and Granola meeting IDs that the target
companion should read when processing this message.
Don't paste content — point to sources.]

---

## Next Steps

[What happens after the target companion processes this.
Who reviews? What's the deadline? What does the founder
need to do?]
```

**Omit any section that has nothing to say.** A short message with 3 sections is better than a long one with empty sections. The header, "What This Is," and "The Core Read" are the only mandatory sections.

---

## Step 5: Confirm

After outputting the message, ask:

```
Message composed for [target companion]. Ready to copy-paste.

Anything to add or change before you take it over?
```

Do not write the message to disk. Do not attempt to route it. The founder is the communication fabric (for now).
