# mom-report Skill

A Bob skill that reads a plain-text meeting transcript and generates a beautiful, self-contained HTML Minutes of Meeting (MOM) report — complete with key action items, decisions, attendees, and a summary. No backend, no external dependencies, works fully offline.

---

## What It Produces

A single file — `meeting-minutes-report.html` — written to your workspace root. Open it in any browser. It includes:

| Section | Details |
|---|---|
| **Header** | Meeting title, company/org, date, facilitator |
| **Attendees** | Auto-detected from speaker labels in the transcript |
| **Summary** | First 5 meaningful sentences extracted from the transcript |
| **Key Decisions** | Extracted from structured labels (`DECIDED:`, `AGREED:`, etc.) or inferred from context |
| **Action Items** | Table with Task, Owner, Due Date, and a clickable Status badge (Open → Closed) |
| **Print / PDF** | "Print / Save as PDF" button using the browser's print dialog |

---

## How to Invoke

### Option 1 — Paste the transcript directly (auto-invoke)

Just paste your meeting transcript into a Bob chat message. Bob will detect it and activate the skill automatically.

```
Here is the transcript from today's meeting:

Date: 15 July 2025
Company: Acme Corp

John: Let's finalise the budget.
Sarah: AGREED: The Q3 budget is set at $500,000.
John: ACTION: Sarah to send the breakdown by 20 July. Owner: Sarah Due: 20 July 2025
Mike: ACTION: Schedule a follow-up for next week. Owner: Mike
```

### Option 2 — Slash command (explicit invoke)

Type `/mom-report` followed by your transcript (or on its own — Bob will ask for the transcript):

```
/mom-report

<paste transcript here>
```

### Option 3 — Natural language trigger

Any message that clearly signals "generate a MOM" or "create a meeting minutes report" will activate the skill:

```
Generate a minutes of meeting report from this transcript: ...
```

```
Create an action items report from the meeting transcript below: ...
```

---

## Transcript Format Guide

The skill works with **any plain-text transcript** — including raw Teams/Zoom auto-transcripts. It works best when these conventions are used:

### Speaker Labels (for attendee detection)
```
John Smith: Let's move on to the next item.
Sarah Connor: I agree, we should finalise this today.
```
Lines in the format `Name: text` are parsed automatically. All unique speaker names become the attendee list.

### Explicit Metadata (optional but recommended)
```
Meeting: Q3 Planning Session
Date: 15 July 2025
Company: Acme Corporation
Attendees: John Smith, Sarah Connor, Mike Johnson
```

### Structured Action Items (best extraction quality)
```
ACTION: Update the project timeline. Owner: John Smith Due: 22 July 2025
TODO: Send budget summary to stakeholders. Owner: Sarah Connor
TASK: Book the venue for the offsite. Owner: Mike Johnson Deadline: 30 July
```

### Structured Decisions (best extraction quality)
```
DECIDED: The project will launch in Q4 2025.
AGREED: Budget cap is set at $500,000.
RESOLVED: Remote-first policy will apply to all new hires.
```

### Unstructured / Natural Language (fuzzy fallback)
If the transcript has no labels, the skill falls back to detecting:
- Action items from sentences containing `will`, `needs to`, `should`, `must`, `is responsible for`
- Decisions from phrases like `we agreed`, `it was decided`, `we resolved`

---

## Output File

The generated report is written to:

```
<workspace-root>/meeting-minutes-report.html
```

Open it directly in any browser — no server needed.

### Exporting as PDF
1. Open `meeting-minutes-report.html` in Chrome, Edge, or Firefox
2. Click **Print / Save as PDF**
3. In the print dialog, set **Destination** → **Save as PDF**
4. Click **Save**

### Toggling Action Item Status
Each row in the Action Items table has an **Open** badge (amber). Click it to toggle to **Closed** (green). This is useful for tracking progress directly in the report before exporting.

---

## File Location

```
.bob/
└── skills/
    └── mom-report/
        ├── SKILL.md      ← skill instructions (read by Bob)
        └── README.md     ← this file
```

This is a **workspace-scoped** skill — it is only active in this project. To make it available globally (across all projects), move the `mom-report/` directory to:

```
~/.bob/skills/mom-report/
```

---

## Example End-to-End

**Input message to Bob:**
```
/mom-report

Meeting: Sprint 12 Review
Date: 10 July 2025
Company: Acme Corp

Alice: Welcome everyone. Let's go through the sprint outcomes.
Bob: AGREED: We will move the release date to 18 July.
Alice: ACTION: Bob to update the release calendar. Owner: Bob Due: 11 July 2025
Carol: ACTION: Alice to send the retrospective survey link. Owner: Alice Due: 12 July 2025
Bob: Any other business?
Alice: Nothing further. Thanks everyone.
```

**Bob writes** `meeting-minutes-report.html` with:
- 3 attendees (Alice, Bob, Carol)
- 1 decision
- 2 action items (both assigned with due dates)
- A summary paragraph
- Print-ready layout
