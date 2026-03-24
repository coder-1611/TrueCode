<p align="center">
  <strong>TrueCode</strong><br>
  <em>Code authenticity checker for teachers</em>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/python-only-3776AB?logo=python&logoColor=white" alt="Python only" />
  <img src="https://img.shields.io/badge/vanilla_JS-no_frameworks-F7DF1E?logo=javascript&logoColor=black" alt="Vanilla JS" />
  <img src="https://img.shields.io/badge/Firebase-Realtime_DB-FFCA28?logo=firebase&logoColor=black" alt="Firebase" />
  <img src="https://img.shields.io/badge/hosted-GitHub_Pages-222?logo=github" alt="GitHub Pages" />
</p>

---

TrueCode helps teachers review student Python code submissions for unusual patterns. It flags things worth a closer look — sudden jumps in complexity, suspiciously similar code between students, or signs of AI-generated work. **You always have the final say.**

> Flags are soft nudges, never accusations. The words "cheating" and "plagiarism" never appear in the app.

---

## Table of Contents

- [Quick Start](#quick-start)
- [How Students Submit](#how-students-submit)
- [Reviewing Submissions](#reviewing-submissions)
- [Detection Checks](#detection-checks)
- [Managing Your Class](#managing-your-class)
- [Settings Reference](#settings-reference)
- [Tips](#tips)
- [Troubleshooting](#troubleshooting)

---

## Quick Start

### 1. Create Your Account

1. Open TrueCode in your browser.
2. Click **Sign Up** — enter your name, email, and a password.
3. Enter the one-time verification code sent to your email.
4. Choose **"Try with demo data"** to explore, or **"Set up my class"** to start fresh.

### 2. Add Students

- During setup, type each student's name and click **Start**.
- Add more later from the **Students** page — click **+ Add Student** or **Bulk Add** to paste a list.

### 3. Set a Class Code

1. Go to the **Pending** page.
2. Under **Class Code Settings**, enter a code (e.g., `PERIOD1` or `MR-SMITH-CS`).
3. Click **Save**.
4. Share this code with your students.

### 4. Passwords (Optional)

| Mode | How it works |
|------|-------------|
| **Passwords on** *(default)* | Set a password for each student on their profile page. Students must enter it to submit. |
| **Passwords off** | Uncheck **"Require student passwords"** in Class Code Settings. Students just pick their name. |

### 5. Create Assignments

Go to **Assignments** → **+ Add Assignment**. Enter the title, paste directions, and choose a type:

| Type | When to use | Similarity sensitivity |
|------|------------|----------------------|
| **Structured** | One correct approach (e.g., "print 1-10 with a for loop") | Standard thresholds |
| **Semi-open** | Some room for variation | ~5% tighter |
| **Creative** | Open-ended, many valid solutions | Much tighter (~50-60%) |

---

## How Students Submit

```
Student Portal → Enter class code → Select name → Enter password → Pick assignment → Paste code → Submit
```

1. Students go to the **Student Portal** page (separate from your dashboard).
2. They enter the class code, select their name, and choose an assignment.
3. They paste their Python code **or** load it from a CodeHS link.
4. Click **Submit** — it appears on your dashboard in seconds.

**Group submissions**: One student checks "This is a group submission," adds partners by name, and all members are credited with the same code.

---

## Reviewing Submissions

### The Grid

Your dashboard shows a **student x assignment grid**. Click any cell to see the full code and flags.

### Flags

Each flag shows:
- **Confidence level** — `low` (yellow) · `moderate` (orange) · `high` (red)
- **Plain-English reason** — what was detected and why
- **Threshold** — the numeric cutoff that was exceeded

If a flag looks fine, click **"Looks fine, dismiss"** — it's permanently removed. No record is kept.

### Check with AI

Click **"Check with AI"** on any flagged submission to open the side panel:

1. A **pre-written prompt** is auto-filled with the assignment directions and student code.
2. Click **Copy prompt**.
3. Click **Open in ChatGPT**, **Open in Claude**, or **Open in Gemini**.
4. Paste the prompt, read the response, and use your own judgment.

No API keys needed. No cost. You use your own accounts.

### Side-by-Side Diff

For similarity flags, click **"View diff"** to see both students' code side-by-side with matching structural patterns highlighted.

---

## Detection Checks

### Check 1 — Advancement

Detects when a student's code suddenly uses Python concepts far beyond the assignment scope and their own history.

- Compares concepts in the submission vs. what the assignment expects
- Weighs against the student's historical baseline (past submissions)
- Factors in your **effort rating** — a student rated 5/5 gets more leeway than one rated 1/5

### Check 2 — Similarity

Detects when two students submit structurally similar code, even if they renamed all variables.

- Uses AST-based structural comparison (not raw string matching)
- Threshold scales with code length (short programs need higher similarity to flag)
- Assignment type multiplier adjusts sensitivity
- Automatically strips starter code (explicit or auto-detected) before comparing
- Cross-assignment similarity also checked (catches reuse across different assignments)

### Check 3 — AI Patterns

Detects signs common in AI-generated code:

- Tutorial-style comment narration
- Polished, grammatically perfect error messages
- Uniform function body lengths
- Perfect PEP 8 import ordering
- Unusually broad vocabulary of built-in functions
- Style inconsistency vs. the student's previous submissions

---

## Managing Your Class

### Student Profiles

Click any student name to see:
- Full submission history
- Concept level progression (bar chart)
- Effort rating (editable, 1–5)
- Open flags

**Effort rating** affects flag sensitivity:

| Rating | Effect |
|--------|--------|
| 1–2 | Flags trigger more easily |
| 3 | Default behavior |
| 4–5 | Flags require a bigger gap to trigger |

### Curriculum Tracking

Add **curriculum units** with expected Python terms (e.g., Unit 1: `print`, `input`, `variable`). This helps the advancement check understand what students should know at each point in your course.

### CodeHS Import

On the **Upload** page, paste CodeHS sandbox links (one per line). TrueCode scrapes the code and student names, matches them to your roster, and creates submissions automatically.

---

## Settings Reference

| Setting | Location |
|---------|----------|
| Class code | Pending → Class Code Settings |
| Require passwords | Pending → Class Code Settings (checkbox) |
| Student passwords | Each student's profile page |
| Effort ratings | Each student's profile page |
| Assignment type | Each assignment's detail page |
| Deactivate old codes | Pending → below class code input |
| Reset to demo data | Bottom of page (deletes all data) |
| Export data (CSV) | Dashboard |

### Multi-Device Sync

All data syncs to Firebase automatically. Sign in on any device and everything is there — students, assignments, submissions, flags, and settings.

### Real-Time Updates

When a student submits, it appears on your dashboard within seconds. No refresh needed. A badge shows how many new submissions were synced.

---

## Tips

- **Start with demo mode** to see how flags look before adding real students.
- **Set assignment types accurately** — a creative assignment flagged at structured thresholds produces false positives.
- **Review and dismiss flags regularly** — keeps your dashboard clean and actionable.
- **Use effort ratings** — they significantly improve flag quality.
- **Curriculum units are optional** but improve advancement detection if you add them.

---

## Troubleshooting

| Problem | Fix |
|---------|-----|
| Student can't submit | Check that your class code is saved (Pending page) |
| "No password set" error | Set a password on the student's profile, or turn off passwords in Class Code Settings |
| Student not in dropdown | Add them on the Students page, then re-save the class code |
| Flags not updating | Hard-refresh (`Cmd+Shift+R` / `Ctrl+Shift+R`) to get the latest code |
| Data missing on new device | Sign in with the same email — data syncs from Firebase automatically |

---

<p align="center">
  Built by <a href="https://github.com/coder-1611">Soham Sthitpragya</a>
</p>
