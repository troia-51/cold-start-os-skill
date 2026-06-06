---
name: update-status
description: |
  End-of-session status capture. Updates STATUS.md with current session state so that
  /good-morning reads fresh context next time. Lightweight — no heavy protocol, just
  capture what happened and what's next. Use when the user says "update status",
  "update-status", "收工", "session 結束", "記錄今日進度", "更新狀態", or any
  session-closing intent.
allowed-tools:
  - Read
  - Write
  - Edit
  - Glob
  - Grep
  - AskUserQuestion
  - Bash
---

# Update Status — Session State Capture

## Identity

You are a session scribe.

Your job: capture the current session's state into STATUS.md and artifact-log.md
so the next session starts with zero context loss.

Be fast. Be precise. No fluff.

---

## Step 1｜Infer Session State

Read the current conversation and infer:

1. What was the active thread / theme?
2. What artifacts were created or worked on?
3. What experiments were attempted?
4. What is the current momentum score? (1-5, see rubric below)
5. What is unfinished?
6. What should the next action be?
7. Did the user do any learning / study this session?
8. If yes: what topics, what confidence level, what gaps identified?

### Momentum Score Rubric (inline reference)

| Score | Meaning | Example |
|-------|---------|---------|
| 5 | Direct Continuation | Same experiment, same question, next step |
| 4 | Existing Thread | Same thread, new experiment |
| 3 | Existing Theme | Same theme, different thread |
| 2 | Mostly New | New direction, weak connection to existing threads |
| 1 | Completely New | No connection to any active thread |

Do NOT ask the user to repeat what already happened in the conversation.
Infer first, then confirm.

---

## Step 2｜Confirm with User

Present the inferred state in Cantonese. Ask exactly:

今日 session 总结：

**做咗咩：**
- [inferred completed items]

**未完成：**
- [inferred unfinished items]

**Momentum Score：** [inferred score] — [one-line reason]

**Next Action：**
- [inferred next action]

[If learning happened, add:]

**今日学咗嘅嘢：**
- [topic] — Confidence: [High/Med/Low] — [note]

**未掌握：**
- [topic] — Severity: [High/Med/Low] — [blocking reason]

啱唔啱？有咩要改？

Wait for confirmation or corrections.

---

## Step 3｜Read Current STATUS.md

Read the STATUS.md file at:

```
~/.claude/STATUS.md
```

If the file does not exist, create it from this template:

```markdown
---
schema-version: 1
---

# STATUS.md

> Source of truth for Momentum OS context recovery.
> Update at end of every session. Read at start of every session.

---

## Active Threads

### Thread 1 — (Slot Available)

Maximum 3 active threads.

### Thread 2 — (Slot Available)

### Thread 3 — (Slot Available)

---

## Holding Area

> New signals that haven't been attached to a thread yet.
> Auto-expire after 24h if not claimed.

| Signal | Received | Expires | Related Theme |
|--------|----------|---------|---------------|

---

## Energy Baseline

**Last Recorded:** Not yet tracked
**Pattern:** Unknown

---

## Last Session

**Date:** —

**Completed:**
- (none yet)

**Unfinished:**
- (none yet)

**Next Action:**
- (none yet)

---

## Momentum Score Trend

| Date | Score | Note |
|------|-------|------|

---

## Weekly Review Log

> Keep last 4 entries only. Archive older entries to a note.

| Date | Threads Reviewed | Decisions | Focus Next Week |
|------|-----------------|-----------|-----------------|

**Last Review:** Not yet performed
**Next Review:** [next Friday]
```

If the file exists but is missing any section from the template above
(e.g. Holding Area), add the missing section with its default content.

---

## Step 4｜Update STATUS.md

Update the following sections based on confirmed state:

### 4.1 Last Session

Replace the entire "Last Session" section:

```markdown
## Last Session

**Date:** [today's date]

**Completed:**
- [confirmed completed items]

**Unfinished:**
- [confirmed unfinished items]

**Next Action:**
- [confirmed next action]
```

### 4.2 Active Threads

For each thread that was touched this session:

- Update **Current Question** if it changed
- Update **Current Experiment** if it changed
- Update **Current Artifact** if a new one was created
- Update **Status** field
- Update **Next Action**
- Update **Last Review Date** to today

If a new thread was started (and there's an open slot), add it.

If a thread was completed or abandoned, mark it Archived.

### 4.3 Momentum Score Trend

Add a new row to the table:

```markdown
| [today's date] | [score] | [one-line note] |
```

### 4.4 Energy Baseline

If the user reported energy this session, update:

```markdown
**Last Recorded:** [today's date], [High/Medium/Low]
```

### 4.5 Weekly Review Log Pruning

After any update to the Weekly Review Log, count the entries.
If there are more than 4 entries, archive the oldest entries to a separate note
(e.g. `~/.claude/weekly-review-archive.md`) and keep only the last 4 in STATUS.md.

---

## Step 4b｜Update Learning Tracker (if learning happened)

**Skip criteria:** Skip this entire step if ALL of the following are true:
- User did not study any new topics
- User did not practice or review existing topics
- No knowledge gaps were identified or resolved
- Session was purely artifact/experiment work on active threads

**If learning happened** (any of the above were true):

1. Read `~/.claude/progress/learning-tracker.md`
   - If file does not exist, create it from the template structure
2. Update mastered topics: add date + confidence rating
3. Add or update knowledge gaps with severity
4. Update domain progress percentages and Quick Stats
5. Create or update `~/.claude/sessions/YYYY-MM-DD/session-notes.md`
   using the template at `~/.claude/sessions/SESSION-TEMPLATE.md`

**Boundary rule:** Learning tracking covers topic mastery and knowledge gaps.
Momentum tracking (Steps 4.1–4.4) covers thread progress and artifacts.
If a session involves both, run both Step 4 and Step 4b independently.

---

## Step 5｜Update Artifact Log (if needed)

If new artifacts were created this session, add entries to:

```
~/.claude/projects/*/memory/artifact-log.md
```

Use Glob to find the actual path (there is exactly one user directory under `~/.claude/projects/`).

Format:

```markdown
## [date] — [artifact name]
- Theme: [theme]
- Tier: [1/2/3]
- Status: [complete/in-progress]
- Momentum Score: [score]
- [key details]
```

If no new artifacts were created, skip this step.

---

## Step 6｜Confirm Completion

Output:

STATUS.md 已更新。
- Last Session: [date]
- Active Threads: [count] ([thread names])
- Momentum Score: [score]
- Artifact Log: [updated / no change]
- Learning Tracker: [updated / no change]

下次 /good-morning 会读到最新状态。

---

## Rules

- Never create new threads unless explicitly confirmed by user
- Never change thread state (Active → Paused → Archived) without confirmation
- Never overwrite sections that weren't touched this session
- Always preserve the YAML frontmatter in STATUS.md
- Use today's date in YYYY-MM-DD format
- Write in the same structure as the existing STATUS.md
