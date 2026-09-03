# Plan: Meeting Minutes (MOM) HTML Generator

## Overview

Build a single, self-contained `index.html` file that accepts a meeting transcript (via paste or file upload), parses it using rule-based/regex logic entirely in the browser, and renders a beautiful, printable HTML Minutes of Meeting (MOM) report — no backend, no external dependencies.

**Scope:**
- One deliverable file: `index.html`
- All CSS and JS inline
- Offline-capable (zero network requests)
- Corporate/professional visual theme (navy/blue accents, clean whites, formal typography)

**Confirmed Design Decisions:**
- Attendees auto-populated one-per-line in the editable textarea
- Action item `Status` column is clickable in the rendered report (toggles Open → Closed inline)
- Report header includes a company name placeholder area above the meeting title

**Non-goals:**
- No LLM/AI processing
- No server-side code
- No external CSS frameworks or JS libraries

---

## Sub-Tasks

---

### Sub-Task 1 — Page Shell & Input Panel

**Status:** `[ ] pending`

**Intent:**
Create the base HTML document with the input panel UI. This is the "entry screen" the user sees before generating a report. It must provide two input methods and editable metadata fields.

**Expected Outcomes:**
- `index.html` exists and opens in a browser
- User can paste transcript text into a `<textarea>`
- User can upload a `.txt` file (file input auto-populates the textarea)
- Editable fields are present for: Meeting Title, Date, and Attendees (auto-populated by parser, manually overridable)
- A "Generate MOM" button is visible
- A "Clear" button resets all fields
- Page is styled with the corporate theme (navy header, white body, clean sans-serif font)

**Todo List:**
- [ ] Create `index.html` with `<!DOCTYPE html>`, `<head>` (charset, viewport, title), inline `<style>` block, inline `<script>` block
- [ ] Add a top header bar with app title "Meeting Minutes Generator" in navy/blue
- [ ] Add the Input Panel section with:
  - Text area labelled "Paste Transcript" (large, resizable)
  - File upload button labelled "Or upload a .txt file" — on file select, reads content into textarea via `FileReader`
  - Editable text input: Meeting Title
  - Editable date input: Meeting Date
  - Editable textarea: Attendees (one per line)
- [ ] Add "Generate MOM" and "Clear" action buttons
- [ ] Apply corporate CSS: navy `#1a3a5c` primary, white `#ffffff` background, accent `#2e6da4`, font stack `'Segoe UI', Arial, sans-serif`

**Relevant Context:**
- No prior files exist — this is the first file created
- All CSS goes into a `<style>` tag in `<head>`; all JS goes into a `<script>` tag before `</body>`

---

### Sub-Task 2 — Transcript Parser (Rule-Based)

**Status:** `[ ] pending`

**Intent:**
Implement the JavaScript parser that processes the raw transcript text and extracts structured data: attendees (from speaker labels), decisions, action items (with owner + due date), and a summary. This is the core intelligence of the tool.

**Expected Outcomes:**
- `parseTranscript(text)` function returns an object: `{ attendees, decisions, actionItems, summary }`
- Speaker detection: lines matching `Name: text` pattern are parsed; unique speaker names are collected as auto-detected attendees
- Decision extraction (structured first, fuzzy fallback):
  - Structured: lines/sentences starting with `DECIDED:`, `AGREED:`, `RESOLVED:`, `DECISION:` (case-insensitive)
  - Fuzzy fallback: sentences containing `we agreed`, `it was decided`, `we will`, `we resolved`
- Action item extraction (structured first, fuzzy fallback):
  - Structured: lines starting with `ACTION:`, `TODO:`, `FOLLOW UP:`, `@mention` patterns
  - Each item: attempts to extract owner (`Owner: Name` or `@Name` or `assigned to Name`) and due date (`Due: date`, `by [date pattern]`, `before [date]`)
  - Fuzzy fallback: sentences containing `will`, `needs to`, `should`, `must`, `responsible for`, combined with a name-like token
- Summary: first 5 sentences of the transcript (after stripping speaker labels) — truncated cleanly at sentence boundary

**Todo List:**
- [ ] Implement `parseTranscript(text)` in the `<script>` block
- [ ] Write `detectSpeakers(lines)` — regex `/^([A-Z][a-zA-Z ]+):\s/` per line, deduplicate
- [ ] Write `extractDecisions(text)` — structured regex first, then fuzzy sentence scan
- [ ] Write `extractActionItems(text)` — structured regex first, then fuzzy scan; each returns `{ task, owner, dueDate }`
- [ ] Write `extractSummary(text)` — strip speaker labels, split on sentence boundaries, take first 5
- [ ] Wire "Generate MOM" button click to call `parseTranscript`, pre-populate the editable metadata fields (attendees) if they are empty, then call the report renderer (Sub-Task 3)

**Relevant Context:**
- Parser lives entirely in the `<script>` block of `index.html`
- Manual overrides (editable fields from Sub-Task 1) are read at render time — parser populates defaults only

---

### Sub-Task 3 — Report Renderer & HTML Output

**Status:** `[ ] pending`

**Intent:**
Build the function that takes the parsed data + user-edited metadata and renders a beautiful, print-ready MOM HTML report inside the same page. The report replaces or appears below the input panel and must look like a formal business document.

**Expected Outcomes:**
- A styled report section is injected into the DOM (or the page transitions to report view)
- Report contains all required sections:
  1. **Header** — company/document title, meeting title, date, prepared-by line
  2. **Attendees** — bulleted list
  3. **Summary** — paragraph block
  4. **Key Decisions** — numbered list with a decision badge/icon
  5. **Action Items** — table with columns: `#`, `Task`, `Owner`, `Due Date`, `Status` (default: Open)
- Report has a "Back / Edit" button to return to input panel
- Report has a "Print / Save as PDF" button that triggers `window.print()`
- Print CSS (`@media print`) hides the input panel and buttons, expands the report to full width, sets appropriate page margins

**Todo List:**
- [ ] Implement `renderReport(metadata, parsed)` function
- [ ] Build report HTML string with all five sections, using template literals
- [ ] Style the report:
  - Header block: navy background, white text, meeting details in subtitle
  - Section headings: navy text, bottom border in accent blue
  - Decisions: left navy border accent per item
  - Action items: striped table, header in navy, `Status` column badge (Open = amber, Closed = green)
- [ ] Add "Back / Edit" button that hides the report and shows the input panel
- [ ] Add "Print / Save as PDF" button calling `window.print()`
- [ ] Add `@media print` CSS rules: hide input panel, hide buttons, full-width report, proper margins
- [ ] Ensure the report section is hidden by default and shown on generate

**Relevant Context:**
- Metadata comes from the editable fields (Sub-Task 1), not directly from the parser output
- `renderReport` is called from the "Generate MOM" button handler set up in Sub-Task 2

---

### Sub-Task 4 — Polish, Edge Cases & Validation

**Status:** `[ ] pending`

**Intent:**
Harden the tool against empty/unexpected inputs, add user-facing feedback, and ensure the page looks polished and professional across browsers and in print preview.

**Expected Outcomes:**
- Validation: show an inline error if transcript is empty when "Generate MOM" is clicked
- Empty sections: if no decisions or no action items are found, show a tasteful "None identified" placeholder row/item
- File upload: gracefully handles non-text files (show error message)
- Responsive layout: input panel and report are readable on screen widths down to 768px
- Print preview: report looks like a clean PDF document (no UI chrome, proper page breaks between sections)
- Favicon: simple inline SVG favicon (document icon) set in `<head>`

**Todo List:**
- [ ] Add input validation in the "Generate MOM" click handler — show styled error banner if transcript textarea is empty
- [ ] In `renderReport`, handle empty `decisions` array and empty `actionItems` array with fallback messages
- [ ] Add file type check in the file upload handler — alert if non `.txt` / `.md` file is selected
- [ ] Add responsive CSS: single-column layout below 768px breakpoint
- [ ] Review and refine `@media print` CSS: test section page-break hints (`page-break-inside: avoid`)
- [ ] Add inline SVG data URI favicon in `<head>`
- [ ] Final review: check all interactive states (hover on buttons, table row hover) are styled

**Relevant Context:**
- All changes are within `index.html`
- This sub-task is deliberately last so it wraps around the completed feature set

---

## File Structure

```
index.html   ← single deliverable, all HTML/CSS/JS inline
mom-generator-plan.md  ← this plan file
```
