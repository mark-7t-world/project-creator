---
name: apply-capability
description: Apply a capability kit's standards to a target artifact — a file, directory, or companion component. Use when you want to align an existing artifact to best practice as defined in a capability's CAPABILITY.md and integration-guide.md. Reads the capability, inventories the target, produces a gap analysis, proposes a remediation plan, and executes with human approval at each step.
disable-model-invocation: true
argument-hint: "[capability-name] [target — file path, directory, or description]"
---

# /apply-capability — Apply a Capability Kit to a Target

Read a capability's standards (CAPABILITY.md + integration-guide.md), audit a target artifact against
those standards, and remediate gaps — with human approval at each step.

## Argument: $ARGUMENTS

Two parts:
1. **Capability name** — the capability to apply (kebab-case, matches a directory name in capabilities/)
2. **Target** — what to apply it to (a file path, directory path, or natural language description)

Examples:
```
/apply-capability companion-guide to this companion's guide
/apply-capability context-ecosystem to the context/ folder
/apply-capability claude-code-configuration to this companion's .claude/ setup
/apply-capability session-hygiene to the checkpoint and status skills
/apply-capability reverse-prompting to the explore skill
```

---

## Step 1: Parse Arguments and Resolve the Capability

Extract from `$ARGUMENTS`:
- `capability_name` = first token (kebab-case)
- `target_description` = everything after the capability name (strip leading "to" if present)

**Search for the capability.** Resolution order:

1. Public kits: `/Users/sonjayatandon/dev/project-creator/companion-kits/public-kits/capabilities/{capability_name}/`
2. Private kits: scan all directories in `/Users/sonjayatandon/dev/project-creator/companion-kits/private-kits/*/capabilities/{capability_name}/`

Use Glob to find: `**/capabilities/{capability_name}/CAPABILITY.md`

**If not found:**
```
Could not find capability "{capability_name}".

Available capabilities:
[list all capability directories found via Glob pattern **/capabilities/*/CAPABILITY.md]

Did you mean one of these?
```
**Stop here.**

**If found**, verify both files exist:
- `{capability_path}/CAPABILITY.md` (required)
- `{capability_path}/integration-guide.md` (optional but expected)

Declare:
```
Capability: {capability_name}
Path: {capability_path}
Has integration guide: [yes/no]
Target: {target_description}
```

---

## Step 2: Read the Capability

Read both files completely:
1. `{capability_path}/CAPABILITY.md` — the pattern definition, standards, anti-patterns
2. `{capability_path}/integration-guide.md` — the implementation templates, checklists, concrete examples

As you read, extract:
- **Standards** — what the capability says "good" looks like
- **Required elements** — things every implementation must have
- **Anti-patterns** — what to watch for
- **Templates** — structural patterns to follow
- **Checklists** — if the integration guide has checklists, note them (these become the audit rubric)

Do NOT summarize the capability back to the user yet. Hold the standards in context for the gap analysis.

---

## Step 3: Resolve and Inventory the Target

The target description from Step 1 may be:
- **A specific file** (e.g., "companion-guide.md", "tracking/hypothesis.md")
- **A directory** (e.g., "the context/ folder", ".claude/")
- **A conceptual target** (e.g., "this companion's guide", "the checkpoint and status skills")

**Resolve to concrete files:**

If a file path: read the file.
If a directory: use Glob to inventory all files, then read the key ones.
If conceptual: resolve to the most likely files in this companion project. For example:
- "this companion's guide" → `companion-guide.md`
- "the skills" → `.claude/skills/*/SKILL.md`
- "the hooks setup" → `.claude/settings.json` + `.claude/hooks/`

Read the target files. For directories with many files, read the most important ones and summarize the rest.

Declare:
```
Target files read:
- {file1} ({line count} lines)
- {file2} ({line count} lines)
- ...

Target files noted but not read:
- {file3} (will read if relevant during remediation)
```

---

## Step 4: Gap Analysis

Compare the target against the capability's standards. For each standard, required element, and checklist item from Step 2, assess:

- **Met** — the target satisfies this standard
- **Partial** — the target addresses this but incompletely or inconsistently
- **Missing** — the target does not address this
- **Anti-pattern present** — the target actively violates a stated anti-pattern

Present the analysis as a structured report:

```
## Gap Analysis: {capability_name} → {target_description}

### Standards Met
- [Standard]: [brief evidence from the target]

### Partial
- [Standard]: [what's there, what's missing]

### Missing
- [Standard]: [what needs to be added]

### Anti-Patterns Detected
- [Anti-pattern]: [where it appears in the target]

### Summary
[2-3 sentences: overall alignment assessment. How far is the target from the capability's standard?]
```

**Ask the user:**
```
Gap analysis complete. [N] standards met, [N] partial, [N] missing, [N] anti-patterns.

Want me to proceed with a remediation plan?
```

**Wait for approval before proceeding.**

---

## Step 5: Remediation Plan

Based on the gap analysis, produce a concrete plan to bring the target into alignment.

For each gap (partial, missing, or anti-pattern), specify:
- **What to change** — the specific file and section
- **How to change it** — add, restructure, rewrite, or remove
- **Template/example** — from the integration guide, if available
- **Priority** — [high] if it's a required element or anti-pattern, [medium] if it improves alignment, [low] if it's polish

Group changes by file. Order by priority within each file.

```
## Remediation Plan

### {file1}
1. [HIGH] {Change description}
   - Current: {what's there now}
   - Target: {what it should look like}
2. [MEDIUM] {Change description}
   - Current: {what's there now}
   - Target: {what it should look like}

### {file2}
1. [HIGH] {Change description}
   ...

### New files to create
1. {file path} — {what it contains and why}

### Estimated scope
[1-2 sentences: how much work this is, what gets touched]
```

**Ask the user:**
```
Remediation plan ready. [N] changes across [N] files.

Options:
1. Execute all changes (I'll show each change before writing)
2. Execute only HIGH priority changes
3. Modify the plan first
```

**Wait for the user's choice.**

---

## Step 6: Execute Remediation

Execute the approved changes. For each change:

1. **Show the proposed edit** — display the old content and new content side by side (or the new file content for new files)
2. **Wait for approval** — the user confirms each change before it's written
3. **Write the change** — use Edit for modifications, Write for new files
4. **Confirm** — report what was written

If the target is a single file, batch related changes and present them together rather than one-by-one.

If the target is a directory with new files to create, show the directory structure first, then create files in dependency order.

After all approved changes are written:

```
Remediation complete.

Changes made:
- {file1}: {summary of changes}
- {file2}: {summary of changes}
- {new file}: created

Changes skipped (user chose not to apply):
- {description}

Remaining gaps (if any):
- {gap that was deferred or requires more context}
```

---

## Step 7: Update Tracking

After remediation:

1. **Add to `tracking/insights-log.md`** — log the capability application and what was learned
2. **Add to `tracking/patterns-discovered.md`** — if the gap analysis revealed a pattern worth noting

Report:
```
Capability applied: {capability_name} → {target_description}
Changes: {count} applied, {count} skipped
Tracking: insights-log.md updated
```
