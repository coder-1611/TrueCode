# Academic Dishonesty Checker — CLAUDE.md

## Project Overview
A web app that helps teachers identify potentially suspicious student code submissions. Flags are soft signals ("worth a look"), never accusations. Teachers have final say on everything.

## Core Philosophy
- Flags are low-stakes nudges, not verdicts
- Language throughout should be neutral: "may want to review", "worth checking", "unusual pattern detected"
- No automated escalation — a flag is only meaningful when a human acts on it
- Show confidence levels (low / moderate / high concern), never binary flagged/not-flagged
- Dismissed flags are permanently deleted — no archive, no record

---

## Architecture

### Components
1. **Web App** — single-page HTML app; full review interface with side-by-side code comparisons, flag management, student profiles, assignment management, curriculum tracking
2. **Firebase Realtime Database** — stores teacher accounts and all teacher data (students, assignments, submissions, flags, curricula) for cross-device sync
3. **Groq API** — generates model solutions from assignment directions for concept-level analysis

### Tech Stack
- Frontend: Single HTML file with vanilla JS
- Data storage: Firebase Realtime Database (REST API)
- Auth: Custom email/password with OTP verification via EmailJS
- AI: Groq API for model code generation
- Similarity: AST-based comparison for Python code (not raw string diff)

---

## Data Models

### Student
```
id, name, class_id, effort_rating (1-5)
```
Students are matched by **name only** (no unique ID from CodeHS).

### Assignment
```
id, class_id, title, directions_text, assignment_type (structured | semi_open | creative), created_at
```
Assignment type is **manually tagged by the teacher**.

### Submission
```
id, student_id, assignment_id, code, line_count, submitted_at
```
All submissions are stored permanently to build each student's historical baseline.

### Flag
```
id, submission_id, check_type (advancement | similarity | ai_pattern), confidence (low | moderate | high), reason_text, created_at
```
Flags are deleted permanently when dismissed by teacher.

### Teacher Effort Rating
```
student_id, rating (1-5)
```
One global rating per student, manually set and updated by the teacher.

---

## The Three Checks

### Check 1 — Sudden Advancement
**Goal:** Detect when a student's code uses Python concepts significantly beyond what the assignment implies, weighted against their known effort level.

**Logic:**
1. Parse assignment directions using an LLM to infer the expected Python concept level (e.g. basic loops, no classes, no recursion)
2. Analyze the student's submitted code for concepts used (AST parse)
3. Compare against the student's historical baseline — what concepts have they used before?
4. If the gap between expected level and submitted code is large AND inconsistent with their history → flag
5. Apply effort rating weight:
   - Rating 1-2: lower threshold to flag (less expected to go beyond)
   - Rating 4-5: much higher threshold required (naturally goes above and beyond)

**Key Rule:** Natural growth over the course is expected and NOT flagged. Only sudden, unexplained jumps relative to both the assignment scope AND the student's personal history are flagged.

### Check 2 — Cross-Student Similarity
**Goal:** Detect when two or more students submitted suspiciously similar code for the same assignment.

**Method:** AST-based structural comparison (not raw string matching) so variable renaming doesn't hide copying.

**Threshold Curve (logarithmic scale by line count):**
| Lines of Code | Flag Threshold |
|--------------|----------------|
| ~10 lines    | 98% similarity |
| ~25 lines    | 95% similarity |
| ~50 lines    | 92% similarity |
| ~100 lines   | 88% similarity |
| ~200+ lines  | 83% similarity |

**Assignment Type Multiplier:**
- **Structured** → use base thresholds above
- **Semi-open** → tighten thresholds by ~5%
- **Creative** → dramatic tightening; flag at ~50-60% similarity (infinite solution space means convergence is nearly impossible by chance)

### Check 3 — AI Manual Verification (Side Panel)
**Goal:** Give teachers a frictionless way to manually verify suspicious code against AI tools — no API required, no cost.

**How it works:**
- A "Check with AI" button appears on any flagged submission
- Clicking it opens a side panel on the dashboard
- The side panel displays a fully pre-written prompt the teacher can copy and paste directly into ChatGPT, Claude, or Gemini
- Direct links to open each AI tool in a new tab are shown alongside the prompt
- The teacher pastes the prompt, reviews the AI's response themselves, and decides if it looks suspicious

**The auto-generated prompt template:**
```
I'm a teacher reviewing a student's Python code submission. 
The assignment was: [assignment directions]

Here is the student's code:
[student code]

Please tell me:
1. Does this code look like it was written by a beginner student, or does it have patterns typical of AI-generated code?
2. If you were asked to solve this assignment, would you write something similar to this?
3. Are there any specific patterns, comments, or structures that seem unusual for a student at this level?
```

**The prompt is auto-populated with:**
- The actual assignment directions for that submission
- The student's actual submitted code

**Side panel UI elements:**
- The pre-written prompt in a scrollable text box
- "Copy prompt" button (copies to clipboard)
- Three buttons: "Open in ChatGPT", "Open in Claude", "Open in Gemini" (each opens the respective tool in a new tab)
- A note: "Paste the prompt into the AI tool and use your own judgment on the response"

**No API keys required. No automated scoring. Teacher uses their own account on each AI service.**

---

## Dashboard UI Requirements

### Flag Display
- Show confidence level badge (low / moderate / high) — color coded (yellow / orange / red)
- Show which check triggered it and a plain-English reason
- Side-by-side code diff view for similarity flags
- "Check with AI" button on every flagged submission — opens the AI side panel
- Single "Looks fine, dismiss" button — deletion is permanent
- Never use words like "cheating", "plagiarism", "violation" in the UI

### AI Side Panel
- Slides in from the right when teacher clicks "Check with AI"
- Only available on flagged submissions
- Contains a fully pre-written, auto-populated prompt (assignment directions + student code inserted automatically)
- "Copy prompt" button copies the full prompt to clipboard
- Three launch buttons: "Open in ChatGPT" / "Open in Claude" / "Open in Gemini" — each opens the tool in a new tab
- Small helper note: "Paste the copied prompt into the AI tool and use your judgment on what it tells you"
- No API keys, no automated scoring — teacher does the review themselves

### Student Profiles
- Show full submission history per student
- Show effort rating (editable inline)
- Show all current open flags

### Assignment Management
- Upload assignment directions (text or file)
- Tag as structured / semi-open / creative
- View all submissions per assignment

---

## Flagging Language Guidelines
Always use soft, neutral language:
- ✅ "This submission may be worth a closer look"
- ✅ "Unusual pattern detected compared to past work"
- ✅ "High structural similarity with another student's submission"
- ❌ "This student cheated"
- ❌ "Plagiarism detected"
- ❌ "AI-generated content found"

---

## What's Out of Scope (For Now)
- Schoology integration (planned for later)
- CodeHS API scraping (teacher provides code manually)
- Auto-escalation to admins or parents
- Student-facing features
- Non-Python languages
- Automated AI API calls (Check 3 is manual — teachers use their own AI accounts)

---

## Open Questions To Resolve Before Building
- Email provider for teacher notifications
- Make the code easy to edit when more updates happen