# Setup Check

**If this file still contains the literal text `[YOUR_NAME]`, the system is not yet configured.**
Read and follow `_setup/wizard.md` immediately. Do not proceed with normal KB operations until setup is complete.

**If the user says "reconfigure", "change settings", "add tools", "add projects", or "add areas":**
Read `_setup/wizard.md` and run only the relevant phase (Phase 2 for tools, Phase 3 for adding items, Phase 4 for automations, or the full wizard for a complete reconfigure).

---

# Knowledge Base

This is [YOUR_NAME]'s PARA-structured knowledge base. It contains projects, areas of responsibility, activity logs, strategies, and meeting notes across work (GGT client, GGT internal) and personal life.

Claude should treat this KB as the single source of truth for all project and area context.

> **Vault root:** `[VAULT_ROOT]`
> All relative paths in this file are relative to this root. Always prepend this root when constructing absolute paths.

---

## Auto-Read: Context Loading

**ALWAYS** do this when a project or area is mentioned by name or abbreviation:

1. Read `_templates/project-mapping.md` to find the item's Category and Folder.
2. Based on Category:
   - **Area**: Read `profile.md` — gives you context, contacts, scope, status. If planning or strategy is discussed, also read `strategy.md`. If recent history is relevant, read `activity-log.md`.
   - **Project**: Read `brief.md` — gives you goal, scope, deadline, deliverables. If recent history is relevant, read `activity-log.md`. If the project has a Parent Area, also read that area's `profile.md` for relationship context.

Do this silently. Do not tell [YOUR_NAME] you're reading files — just have the context ready.

### Path Resolution

To resolve any name to an absolute file path:

1. Read `_templates/project-mapping.md` — the single source of truth for names, categories, folder paths, and aliases.
2. Match the name (or alias) to a row in the mapping table.
3. Get the Category (Project or Area) and Folder path.
4. Prepend the vault root to the folder path.
5. Append the target file based on category:
   - **Area files**: `profile.md`, `strategy.md`, `activity-log.md`, `meetings/`
   - **Project files**: `brief.md`, `activity-log.md`, `meetings/`

**Example:** "Acme Corp" → mapping gives Category=Area, Folder=`Areas/acme-corp/` → `[VAULT_ROOT]/Areas/acme-corp/profile.md`
**Example:** "Acme Website Redesign" → mapping gives Category=Project, Folder=`Projects/acme-website-redesign/` → `[VAULT_ROOT]/Projects/acme-website-redesign/brief.md`

---

## Auto-Retrieve: Knowledge Before Work

Before starting non-trivial work, **silently** search for relevant past knowledge. Do this the same way Auto-Read loads context — have it ready without announcing it.

1. **Search `_solutions/`** — grep frontmatter and headings for matching problem types, tags, or area names relevant to the current task
2. **Scan memory files** — check `MEMORY.md` index for relevant learnings (feedback, references, patterns)
3. **Check recent activity** — if working on a project or area, scan their last 3-5 `activity-log.md` entries

**When to trigger:** Tasks that involve debugging, building/modifying automations, processing data from external tools, or complex work.

**When NOT to trigger:** Simple lookups, file reads, quick edits, or straightforward tasks where past context won't change the approach.

If relevant solutions or learnings are found, use them to inform the approach. If a past solution directly applies, reference it rather than re-discovering the fix.

---

## Auto-Log: Activity Logging After Work

After completing meaningful work for a project or area during a session, **automatically** append a dated entry to their `activity-log.md`. Do not ask for permission.

**Format:**

```markdown
## YYYY-MM-DD
**[Short title of work done]**

[2-4 sentences: what was done, why, and what's next. Include deliverable names, key decisions, and any pending items.]
```

**Newest entries go at the top** (below the frontmatter/header).

**What counts as meaningful work:**
- Creating deliverables (documents, reports, presentations, analyses)
- Completing strategic decisions or planning
- Processing meeting notes or transcripts
- Significant communication or analysis

**Do NOT log:** File formatting, quick lookups, reading files, minor edits.

Write **rich** entries — not just "did X" but "did X because Y, result was Z, next step is W." These feed into weekly reviews.

---

## Auto-Update: Strategy Changes

When strategic decisions are made during a session (new goals, changed priorities, updated metrics, scope changes), **automatically** update the relevant sections in the area's `strategy.md`. Keep existing content and update only the relevant sections — don't overwrite the whole file.

This applies to **Areas only**. For projects, capture scope or goal changes in `brief.md` instead.

---

## Auto-Compound: Solution Documentation After Problem-Solving

After solving a non-trivial problem during a session, **offer** to document it as a solution. This captures the investigation and fix while context is fresh.

**When to offer:**
- After debugging an automation failure or workaround
- After figuring out a tool integration or formatting issue
- After resolving a data or workflow problem
- After any "that was hard to figure out" moment

**How:** Ask lightly — "This seems worth documenting as a solution. Should I save it to `_solutions/`?" If [YOUR_NAME] says no, move on. If yes, create a file at `_solutions/YYYY-MM-DD-{slug}.md` using the template at `_templates/solution.md`.

**Problem types** (use in frontmatter, extensible):
`automation-failure`, `tool-integration`, `formatting`, `workflow`, `data-issue`

Do NOT document routine work, simple fixes, or things that are obvious from the code. Solutions should capture knowledge that would otherwise be lost.

---

## Daily Journal

At session **START**:

1. Check if `_daily/YYYY-MM-DD.md` exists.
   - **If it exists:** read the whole file — including any personal notes [YOUR_NAME] has already written. Do not modify it.
   - **If it does not exist:** create it from `_templates/daily-note.md` with `{{date}}` replaced by today's date (YYYY-MM-DD). Only do this when starting a real working session, not for quick lookups.
2. Also read yesterday's note (`_daily/YYYY-MM-DD.md` for yesterday) if it exists.

At session **END** (if meaningful work was done), append only to the **Session Log** section of today's daily note:

```markdown
### HH:MM — [Brief title]
- Item(s): [names and categories]
- What: [1-2 sentences]
- Next: [pending items]
```

**Never modify the Morning, Capture, or Evening sections.** Those belong to [YOUR_NAME].

The daily journal complements per-item activity logs. Activity logs are the detailed record; the daily journal is a lightweight cross-item index of the day's work and a space for personal reflection.

---

## Graceful Degradation

When a file is missing or empty:
- **profile.md or brief.md missing** → note internally, continue with available context, offer to scaffold after current task
- **activity-log.md missing** → treat as new item with no history, create the file with proper header when first logging
- **strategy.md missing** → skip strategy context, do not fail (Areas only)
- **meetings/ missing** → create the directory when saving the first meeting note

When a referenced file (SOP, template) does not exist:
- Log the broken reference in the daily journal
- Continue with best-effort behavior
- Do not interrupt [YOUR_NAME] about infrastructure issues mid-task

---

## Context Preservation

During long sessions, proactively write important state to the daily journal before it gets lost to context compression:
- Decisions made this session
- Context shared verbally but not yet written to KB files
- In-progress work state (what's done, what's next)

After context compression, re-read the daily journal to recover state.

---

## Procedures & References

| Topic | Where to find it |
|-------|-----------------|
| Meeting notes format | `_templates/meeting-note.md` |
| Meeting processing workflow | `SOPs/meeting-processing.md` |
| Weekly review | `SOPs/weekly-review.md` |
| Name → folder mapping | `_templates/project-mapping.md` |
| Area template | `_templates/area-profile.md` |
| Project template | `_templates/project-brief.md` |
| Activity log template | `_templates/activity-log.md` |
| Daily note template | `_templates/daily-note.md` |
| Solution documentation format | `_templates/solution.md` |
| Available automations | `SOPs/available-automations.md` |

## Vault Structure

```
Projects/
└── {slug}/
    ├── brief.md          — Goal, scope, deadline, deliverables, stakeholders
    ├── activity-log.md   — Chronological work log (newest first)
    └── meetings/         — Meeting note files (YYYY-MM-DD-topic.md)

Areas/
└── {slug}/
    ├── profile.md        — Context, contacts, overview, status
    ├── strategy.md       — Current goals, priorities, engagement details
    ├── activity-log.md   — Chronological work log (newest first)
    └── meetings/         — Meeting note files (YYYY-MM-DD-topic.md)

Archives/
├── Projects/             — Completed/inactive projects (move from Projects/)
└── Areas/                — Completed/inactive areas (move from Areas/)
```

- `_overview.md` — Your role, tools, and workflow notes
- `SOPs/` — Standard operating procedures
- `_templates/` — File format templates
- `_daily/` — Cross-item daily session journals
- `_solutions/` — Cross-cutting problem→solution documentation (searchable by problem type and tags)

## Data Sources

Always check the KB first before external sources. The KB is the canonical view.

<!-- Data sources are configured during setup. The setup wizard adds entries here based on your connected tools. -->
