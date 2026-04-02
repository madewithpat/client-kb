# Setup Wizard

This file is read by Claude, not by the user. CLAUDE.md auto-triggers this when the system is unconfigured.

---

## Behavior Rules

Follow these strictly throughout the entire wizard:

1. **One question at a time.** Never ask multiple things in one message. Use the AskUserQuestion tool for structured choices. Use plain chat only for free-text input (like names).
2. **Show progress.** Use TodoWrite at the start of the chosen flow. Mark tasks complete as you go.
3. **Validate immediately.** After connecting any tool, test it right away and report the result.
4. **Be concise.** Short sentences. No lectures. If you can say it in one line, don't use three.
5. **Skip gracefully.** If something fails or the user doesn't have a tool, say "Skipping — you can add this later" and move on. Never block the wizard on a failure.
6. **Batch silent work.** When replacing placeholders or creating files, do it all at once without narrating each file. Just confirm the result.
7. **Re-entry safe.** If some placeholders are already replaced (partial setup), skip completed steps. Check before replacing.

---

## Entry Point

When triggered by CLAUDE.md, start with this welcome message:

> Welcome! This vault is a PARA-structured knowledge base that works with Claude Code. It automatically remembers context about your projects and areas, logs what you do, and helps you stay on top of meetings and reviews.
>
> Let's get you set up.

Then immediately ask the mode question:

**Use AskUserQuestion:**
- Question: "How would you like to set up?"
- Options:
  - **Quick setup** — "Sensible defaults, done in 2 minutes. No integrations. You can customize later."
  - **Custom setup** — "I'll ask what tools you use, add your initial projects and areas, and tailor everything. Takes 5-10 minutes."

---

## Quick Path

If Quick setup is chosen:

### Progress
Create TodoWrite:
```
□ Set up your profile
□ Create vault structure
□ Ready to go
```

### Step 1: Name
Ask in chat: "What's your first name?"

Mark "Set up your profile" as in_progress.

### Step 2: Apply configuration
Detect the vault root (the directory containing this file's parent — i.e., the repo root).

Replace all placeholders across every file in the vault:
- `[YOUR_NAME]` → their name
- `[VAULT_ROOT]` → detected absolute path

Files to update: CLAUDE.md, _templates/project-mapping.md, _templates/_overview.md, SOPs/meeting-processing.md, SOPs/weekly-review.md, SOPs/available-automations.md, _setup/settings.local.json

Mark "Set up your profile" as completed.

### Step 3: Create structure
Mark "Create vault structure" as in_progress.

Create directories:
- `Projects/`
- `Areas/`
- `Resources/`
- `Archives/Projects/`
- `Archives/Areas/`
- `_solutions/`

Copy `_templates/_overview.md` → `_overview.md` (with their name filled in).
Copy `_setup/settings.local.json` → `.claude/settings.local.json` (create `.claude/` if needed).
Create `.env` from `.env.example`.

Mark "Create vault structure" as completed.

### Step 4: Done
Mark "Ready to go" as completed.

Send:
> You're all set, [name]!
>
> **Your vault is organized using PARA:**
> - `Projects/` — time-bound work with a clear goal
> - `Areas/` — ongoing responsibilities (clients, team roles, personal domains)
> - `Archives/` — completed or inactive items
>
> **How to use this:**
> - Mention a project or area by name and I'll load its context
> - After I do meaningful work, I'll log it automatically
> - Share a meeting transcript and I'll turn it into structured notes
> - Say "weekly review" on Fridays for a summary
>
> **To add items:** Say "add projects" or "add areas" and give me names — I'll create the folders.
> **To connect tools:** Say "add tools" anytime to connect Google Calendar, meeting recorders, or task managers.

---

## Custom Path

If Custom setup is chosen:

### Progress
Create TodoWrite:
```
□ Set up your profile
□ Create vault structure
□ Connect your tools
□ Add your projects and areas
□ Choose automations
□ Verify everything works
```

---

### Phase 1: Profile

Mark "Set up your profile" as in_progress.

**Step 1.1 — Name**
Ask in chat: "What's your first name?"

**Step 1.2 — Language**
Use AskUserQuestion:
- Question: "What language should generated content be in? (Follow-up emails, reviews, etc.)"
- Options:
  - **English (Recommended)**
  - **Swedish**
  - (Other as free text)

Note the language — if not English, add an instruction to CLAUDE.md's auto-log section specifying the language for generated content.

**Step 1.3 — Apply configuration**
Detect vault root. Replace all placeholders:
- `[YOUR_NAME]` → their name
- `[VAULT_ROOT]` → detected absolute path

Files to update: CLAUDE.md, _templates/project-mapping.md, _templates/_overview.md, SOPs/meeting-processing.md, SOPs/weekly-review.md, SOPs/available-automations.md, _setup/settings.local.json

If not English, append to CLAUDE.md after the Data Sources section:
```markdown

## Language

Generate all client-facing content (follow-up emails, weekly reviews, meeting summaries) in [chosen language]. Internal KB entries (activity logs, meeting notes) can mix English and [chosen language] as natural.
```

Mark "Set up your profile" as completed.

---

### Phase 2: Vault Structure + Tools

Create directories: `Projects/`, `Areas/`, `Resources/`, `Archives/Projects/`, `Archives/Areas/`, `_solutions/`.
Copy `_templates/_overview.md` → `_overview.md`.
Copy `_setup/settings.local.json` → `.claude/settings.local.json` (create `.claude/` if needed).
Create `.env` from `.env.example`.
Mark "Create vault structure" as completed.

Mark "Connect your tools" as in_progress.

**Step 2.1 — Detect existing connections**
Silently check:
- Claude in Chrome: try `mcp__Claude_in_Chrome__tabs_context_mcp`
- Google Calendar: `mcp__mcp-registry__search_mcp_registry` with ["google", "calendar"]
- Google Drive: `mcp__mcp-registry__search_mcp_registry` with ["google", "drive"]
- Node.js: `node --version`

**Step 2.2 — Show status and ask**
Present what's detected, then ask:

Use AskUserQuestion (multi-select):
- Question: "Which of these do you use? I'll connect them now. (Select all that apply)"
- Options:
  - **Google Calendar** — "Meeting detection, prep briefs, and follow-up reminders"
  - **Meeting recorder** — "Fireflies, Otter, Fathom, etc. — auto-process recordings into notes"
  - **Task manager** — "Todoist, Linear, Asana — create tasks from action items"
  - **None right now** — "Skip this — the KB works fine without integrations"

**Step 2.3 — Connect each selected tool**

**Google Calendar:**
1. Search MCP registry
2. If found and not connected → `mcp__mcp-registry__suggest_connectors`
3. Wait for user to connect
4. Test: fetch this week's events
5. Report: "✓ Google Calendar connected — I can see [N] events this week"
6. Add MCP permissions to `.claude/settings.local.json`
7. Ask: "Do you have a company email domain? (e.g., @yourcompany.com) This helps me identify external meetings."
   - If yes → update meeting filter in `_templates/project-mapping.md`
   - If no → "I'll flag all meetings and you tell me which are relevant."

**Meeting recorder:**
1. Ask: "Which recording tool do you use?" (Fireflies / Otter / Fathom / Other)
2. Check if Claude in Chrome is connected
3. If not connected: "To auto-fetch transcripts, you need the Claude in Chrome browser extension. Want to set it up?"
   - If yes: guide install → test → add permissions
   - If no: "No problem — you can paste transcripts manually. I'll still create structured notes."
4. Report result

**Task manager:**
1. Ask: "Which task manager?" (free text)
2. If Todoist:
   a. Tell [YOUR_NAME]: "Run this in your terminal to connect Todoist via MCP:"
      ```
      claude mcp add --transport http todoist https://ai.todoist.net/mcp
      ```
      This opens an OAuth browser flow — they'll authorize Claude's access to Todoist.
   b. Wait for confirmation that the command ran, then add Todoist MCP permissions to `.claude/settings.local.json`:
      ```json
      "mcp__todoist__get-overview",
      "mcp__todoist__find-tasks",
      "mcp__todoist__find-projects",
      "mcp__todoist__find-completed-tasks",
      "mcp__todoist__add-tasks",
      "mcp__todoist__add-projects",
      "mcp__todoist__update-tasks",
      "mcp__todoist__complete-tasks"
      ```
   c. Test: call `mcp__todoist__get-overview` — confirm it returns projects and tasks.
   d. Report: "✓ Todoist connected — I can see [N] projects"
   e. Append to the Data Sources section in `CLAUDE.md`:
      ```
      - **Todoist** — task and project manager. Use `mcp__todoist__*` tools. Always look up the item's `Todoist Project ID` from `_templates/project-mapping.md` before creating or fetching tasks.
      ```
3. Other: "I don't have a built-in integration for [tool], but you can always ask me to create tasks and I'll tell you what to add."

**None selected:**
"No problem. The core system works great on its own — context loading, activity logging, and the daily journal all work without integrations. You can always say 'add tools' later."

Mark "Connect your tools" as completed.

---

### Phase 3: Add Projects and Areas

Mark "Add your projects and areas" as in_progress.

Explain: "The KB is organized around two things: **Areas** (ongoing responsibilities — client relationships, team roles, personal domains) and **Projects** (time-bound work with a clear goal). Let's add yours."

**Step 3.1 — Areas first**

Ask in chat: "What are your main areas of ongoing responsibility? These could be client relationships, internal roles, or personal domains. List as many as you want, or say 'skip'."

For each area name provided:
1. Ask sub-type using AskUserQuestion:
   - Question: "What type is [name]?"
   - Options:
     - **ggt-client** — "A client at GGT"
     - **ggt-internal** — "An internal GGT responsibility"
     - **personal** — "A personal area"
2. Generate a slug (lowercase, hyphens, no special chars)
3. Create `Areas/{slug}/` with:
   - `profile.md` from `_templates/area-profile.md` (fill in name and sub-type)
   - `strategy.md` from `_templates/strategy.md` (fill in name)
   - `activity-log.md` from `_templates/activity-log.md` (fill in name)
   - `meetings/` directory
4. Add to `_templates/project-mapping.md`

Ask: "Any short names or nicknames for these? (e.g., 'Acme' for 'Acme Corporation')"
- If yes → add to the Aliases table in project-mapping.md

**Step 3.2 — Projects**

Ask in chat: "Now let's add active projects — things with a specific goal and a likely end date. List them, or say 'skip'."

For each project name provided:
1. Ask sub-type (same options as above)
2. Ask: "Does this belong to one of your areas? If so, which one? (Or press enter to leave it unlinked)"
3. Generate a slug
4. Create `Projects/{slug}/` with:
   - `brief.md` from `_templates/project-brief.md` (fill in name, sub-type, parent_area)
   - `activity-log.md` from `_templates/activity-log.md` (fill in name)
   - `meetings/` directory
5. Add to `_templates/project-mapping.md`

**Step 3.3 — Optional profile fill-in**

Use AskUserQuestion:
- Question: "Want to fill in any profiles or briefs now?"
- Options:
  - **Yes, walk me through one** — "I'll ask about the first area as an example"
  - **No, I'll do it as I go** — "When you mention a name, I'll load whatever context exists"

If yes: walk through `profile.md` for their first area — ask about overview, contacts, scope, status.

Mark "Add your projects and areas" as completed.

---

### Phase 4: Automations

Mark "Choose automations" as in_progress.

Explain briefly: "The KB can run tasks automatically on a schedule — like compiling a weekly review every Friday. I'll only show ones that work with your connected tools."

Build the list based on what was connected in Phase 2:

**Always show:**
- Weekly Review (Friday 14:00) — "Compiles a summary of your week's work across all projects and areas"
- Safety Net (Wednesday 08:00) — "Catches forgotten follow-ups and quiet items"
- Systems Review (Friday 15:00) — "Weekly analysis of what's working and what to improve"

**Show if Google Calendar connected:**
- Review Nudge (Friday 08:00) — "Flags items that need context before the weekly review"
- Meeting Prep (Weekday mornings) — "Generates talking points before meetings"

**Show if calendar + recorder + Claude in Chrome:**
- Daily Meeting Follow-up (Weekday 08:30) — "Processes yesterday's meetings into notes and emails"

Use AskUserQuestion (multi-select):
- Question: "Which automations do you want? (You can add more anytime)"
- Show only the relevant options from above
- Include: **None for now** — "I'll use the KB manually for a while first"

For each selected:
1. Read the prompt from `SOPs/available-automations.md`
2. Install with `mcp__scheduled-tasks__create_scheduled_task`
3. Confirm: "✓ [Name] — runs [schedule]"

Mark "Choose automations" as completed.

---

### Phase 5: Verify

Mark "Verify everything works" as in_progress.

**Test 1: Context loading**
Pick one of their areas (or use a hypothetical if none were added).
"Let me test context loading..." → read the profile.md → "✓ I can load [name]'s context."

**Test 2: Connected tools** (for each)
- Calendar: "✓ Calendar — [N] events this week"
- Claude in Chrome: "✓ Browser — connected"
- Task manager: "✓ [Tool] — connected"
- Or "Skipped — no tools connected" if none

**Test 3: Daily journal**
Write to `_daily/YYYY-MM-DD.md`:
```
### HH:MM — Knowledge Base Setup
- What: Set up KB with [N] areas, [N] projects, [N] tools connected, [N] automations
- Next: Fill in profiles and briefs as you go
```
"✓ Daily journal works."

Mark "Verify everything works" as completed.

---

### Summary

```
All done, [name]!

Areas: [list or "none yet — say 'add areas' to create some"]
Projects: [list or "none yet — say 'add projects' to create some"]
Tools: [list or "none — say 'add tools' anytime"]
Automations: [list or "none — say 'add automations' anytime"]

Day-to-day:
- Mention a project or area → I load its context
- After work → I log what was done
- Share a meeting link → I create structured notes
- "weekly review" on Fridays → I compile a summary
- "reconfigure" → change settings or add tools
```

---

## Re-entry

When triggered by CLAUDE.md for reconfiguration:

1. Check what the user asked for:
   - "add tools" / "connect tools" → run Phase 2 tools section only
   - "add projects" → run Phase 3 projects section only
   - "add areas" → run Phase 3 areas section only
   - "add automations" → run Phase 4 only
   - "reconfigure" / "change settings" → offer choice of which phase to re-run

2. Use AskUserQuestion if "reconfigure":
   - Question: "What do you want to change?"
   - Options:
     - **Connect more tools**
     - **Add areas**
     - **Add projects**
     - **Change automations**
     - **Full reconfigure** — "Start the setup from scratch"
