# Meeting Minutes Generator

A workspace powered by a [Bob](https://www.ibm.com/bob) skill that turns any plain-text meeting transcript into a beautiful, self-contained HTML Minutes of Meeting (MOM) report — with key action items, decisions, attendees, and a summary. No backend, no external dependencies, works fully offline.

---

## Project Structure

```
MeetingMinutes/
├── .bob/
│   └── skills/
│       └── mom-report/
│           ├── SKILL.md               ← Bob skill instructions
│           └── README.md              ← Skill-level docs
├── meeting-minutes-report.html        ← Generated report (created on each run)
├── mom-generator-plan.md              ← Original build plan
└── README.md                          ← This file
```

---

## What It Produces

Running the skill writes `meeting-minutes-report.html` to the workspace root. Open it in any browser. It includes:

| Section | Details |
|---|---|
| **Header** | Meeting title, company/org, date, facilitator |
| **Attendees** | Auto-detected from speaker labels in the transcript |
| **Summary** | First 5 meaningful sentences extracted from the transcript |
| **Key Decisions** | Extracted from structured labels (`DECIDED:`, `AGREED:`, etc.) or inferred from context |
| **Action Items** | Table with Task, Owner, Due Date, and a clickable Status badge (Open → Closed) |
| **Print / PDF** | "Print / Save as PDF" button using the browser's print dialog |

---

## Prerequisites

- [IBM Bob](https://www.ibm.com/bob) installed and running
- This workspace open in Bob

No other tools, runtimes, or dependencies are required.

---

## How to Run

### Option 1 — Paste transcript directly (auto-invoke)

Open a Bob chat in this workspace and paste your meeting transcript. Bob detects it and activates the skill automatically:

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

Type `/mom-report` in Bob, optionally followed by the transcript:

```
/mom-report

<paste your transcript here>
```

If you type `/mom-report` alone, Bob will ask you to paste the transcript.

### Option 3 — Natural language

Any message clearly asking for a MOM or action items report will trigger the skill:

```
Generate a minutes of meeting report from this transcript: ...
```

```
Create an action items report from the meeting below: ...
```

---

## Transcript Format Guide

The skill works with **any plain-text transcript**, including raw Microsoft Teams and Zoom auto-transcripts. Extraction quality improves with structured labels.

### Speaker Labels — attendee auto-detection
```
John Smith: Let's move on to the next item.
Sarah Connor: I agree, we should finalise this today.
```

### Explicit Metadata (optional but recommended)
```
Meeting: Q3 Planning Session
Date: 15 July 2025
Company: Acme Corporation
Attendees: John Smith, Sarah Connor, Mike Johnson
```

### Structured Action Items — best quality
```
ACTION: Update the project timeline. Owner: John Smith Due: 22 July 2025
TODO: Send budget summary to stakeholders. Owner: Sarah Connor
TASK: Book the venue for the offsite. Owner: Mike Johnson Deadline: 30 July
```

Supported labels: `ACTION:` · `TODO:` · `TASK:` · `FOLLOW UP:`

### Structured Decisions — best quality
```
DECIDED: The project will launch in Q4 2025.
AGREED: Budget cap is set at $500,000.
RESOLVED: Remote-first policy will apply to all new hires.
```

Supported labels: `DECIDED:` · `AGREED:` · `RESOLVED:` · `DECISION:`

### Unstructured / Natural Language — fuzzy fallback
If the transcript has no labels, the skill falls back to inferring:
- Action items from sentences containing `will`, `needs to`, `should`, `must`, `is responsible for`
- Decisions from phrases like `we agreed`, `it was decided`, `we resolved`

---

## Using the Generated Report

The report is written to `meeting-minutes-report.html` in this folder.

### Open in browser
Double-click `meeting-minutes-report.html` — no server needed.

### Export as PDF
1. Open the file in Chrome, Edge, or Firefox
2. Click **Print / Save as PDF** in the report
3. In the print dialog set **Destination** → **Save as PDF**
4. Click **Save**

### Track action item status
Each row in the Action Items table has an **Open** badge (amber).  
Click it to toggle to **Closed** (green) — useful for live tracking before exporting.

---

## Example End-to-End

**Input:**
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

**Output — `meeting-minutes-report.html` containing:**
- 3 attendees (Alice, Bob, Carol)
- 1 key decision
- 2 action items with owners and due dates
- A summary paragraph
- Print-ready professional layout

---

## Making the Skill Global

By default this skill is **workspace-scoped** — it only activates inside this project. To make it available in every workspace, copy the skill directory to your global Bob skills folder:

**Windows:**
```powershell
Copy-Item -Recurse .bob\skills\mom-report "$env:USERPROFILE\.bob\skills\mom-report"
```

**macOS / Linux:**
```bash
cp -r .bob/skills/mom-report ~/.bob/skills/mom-report
```

The skill will be available in all workspaces after restarting Bob.

---

## Skill Files

| File | Purpose |
|---|---|
| [`.bob/skills/mom-report/SKILL.md`](.bob/skills/mom-report/SKILL.md) | Procedural instructions Bob follows when the skill activates |
| [`.bob/skills/mom-report/README.md`](.bob/skills/mom-report/README.md) | Skill-level usage reference |
