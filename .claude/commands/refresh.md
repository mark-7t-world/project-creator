# /refresh — Re-Bake Kit Materials into Companion

Update a companion's baked-in reference materials when the source kits have evolved.

## Usage

```
/refresh                     # Use current companion
/refresh [client/companion]  # Override for specific companion
```

## Argument: $ARGUMENTS

---

## Instructions

### Step 1: Determine the Companion

1. If `$ARGUMENTS` contains a companion path, use that
2. Otherwise, read `tracking/current-companion.md` for the current companion
3. If no companion is set:
   ```
   No companion set. Use /companion to set or create one first.
   ```

Store the companion path and resolve the full directory path.

---

### Step 2: Identify the Kit

Read `tracking/current-companion.md` for the `kit_path` field. This handles both patterns:
- **Client-grouped** (e.g., `7t-world/website-update`) → kit at `companion-kits/private-kits/7t-world-companion-kit/`
- **Standalone** (e.g., `triangulation-framework`) → kit at `companion-kits/private-kits/triangulation-framework-companion-kit/`

Verify the kit directory exists on disk.

If no kit exists (kit_path is `none` or directory missing):
```
No companion kit found for [companion].
Expected: companion-kits/private-kits/[name]-companion-kit/

Nothing to refresh. The companion has no associated kit.
```
**Stop here.**

---

### Step 3: Read Current Baked Materials

Check what's currently in the companion's `reference/` folder:

```bash
ls -la companions/[client]/[companion]/reference/
```

For each file found, read and note its content fingerprint (first line, last modified, key sections).

If no `reference/` folder exists:
```
No reference/ folder found. This companion was built before kit-baking was implemented.

Would you like me to:
1. Run a full bake (equivalent to what /build Step 7 does)
2. Skip — this companion doesn't need kit materials

Choose (1/2):
```

If user chooses 1, execute the full bake from `/build` Step 7 (7a through 7f), then stop.

---

### Step 4: Identify What Changed in the Kit

Read the source kit materials and compare against what's baked:

**Persona check:**
- Read the persona source (from `context/decisions.md` → persona name → kit location)
- Compare against `reference/persona.md`
- Note: added sections, removed sections, changed content

**Capabilities check:**
- List capabilities in the kit: `companion-kits/private-kits/[client]-companion-kit/capabilities/`
- List capabilities in `reference/capabilities/`
- Compare each matching pair for changes
- Note: new capabilities in kit not yet baked, removed capabilities

**Library check:**
- List library subjects in kit: `companion-kits/private-kits/[client]-companion-kit/library/`
- List library refs in `reference/library/`
- Note: new library entries, updated entries (check metadata.yaml for status changes)

---

### Step 5: Present Change Report

```
## Refresh Report: [companion]

### Persona: [persona name]
- [CHANGED | UNCHANGED | NEW | MISSING]
- [If changed: specific sections that differ]

### Capabilities
| Capability | Status | Details |
|-----------|--------|---------|
| [name] | UNCHANGED | — |
| [name] | CHANGED | [what changed] |
| [name] | NEW (in kit, not baked) | [description] |
| [name] | ORPHANED (baked, not in kit) | [recommend: keep/remove] |

### Library
| Subject | Status | Details |
|---------|--------|---------|
| [name] | UNCHANGED | — |
| [name] | UPDATED | [what changed] |
| [name] | NEW | [description] |

### CLAUDE.md
- [NEEDS UPDATE | UP TO DATE]
- [If needs update: what sections are affected]

---

**Changes to apply:** [N items]

Apply these changes? (yes/no/selective)
```

If user says "selective", ask which changes to apply.

---

### Step 6: Apply Changes

For each approved change:

1. **Re-bake persona** — Distill updated persona source into `reference/persona.md`
2. **Re-bake capabilities** — Update `reference/capabilities/[name].md` for changed ones, create new ones, optionally remove orphaned ones
3. **Re-bake library** — Re-contextualize updated library entries for this companion
4. **Update CLAUDE.md** — Regenerate the companion's self-contained CLAUDE.md to reflect new reference materials

**For each file updated, preserve any companion-specific customizations:**
- If the baked file has been manually edited (comments, additions), merge changes rather than overwriting
- Flag any merge conflicts for user review

---

### Step 7: Confirm

```
## Refresh Complete: [companion]

**Updated:**
- [list each file updated]

**Added:**
- [list each new file]

**Removed:**
- [list each removed file, if any]

**CLAUDE.md:** [Updated | No change needed]

The companion's reference materials are now current with the kit.
```

---

## Principles

### Explicit Over Automatic
Kit changes never propagate automatically. The user runs `/refresh` when they want updates. This prevents surprise behavior changes in working companions.

### Merge, Don't Overwrite
Baked materials may have been customized after the initial bake. Always check for companion-specific additions before overwriting.

### Report Before Acting
Always show what will change before changing it. The user may not want all kit updates applied to every companion.

### Selective Application
Not all kit changes belong in all companions. A new capability added to the kit might not be relevant to an existing companion. Let the user choose.
