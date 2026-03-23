# Session Handoff Setup Guide

Claude Code forgets everything when you close the terminal. Fix that in 15 minutes.

---

## What you're building

Four files that give you session continuity:

```
your-project/
├── CLAUDE.md                ← your project's rules (loaded every session automatically)
├── NOW.md                   ← where you left off (overwritten each close)
├── MEMORY.md                ← lessons learned (grows over time)
└── .claude/commands/
    ├── wake.md              ← /wake to start a session
    └── close.md             ← /close to end a session
```

Think of it like the movie Ghajini. You write tattoos before you sleep. You read them when you wake up. You never lose what matters.

---

## Step 1: Create CLAUDE.md (2 min)

In your project root, create `CLAUDE.md`. This is your project's identity. Claude Code reads it automatically at the start of every session.

```markdown
# [Your Project Name]

## Stack
- Language: [Python / TypeScript / etc]
- Framework: [Next.js / FastAPI / etc]
- Database: [Postgres / SQLite / etc]
- Hosting: [Vercel / AWS / etc]

## Architecture
- [Brief description of how the app is structured]
- [Key folders and what they do]

## Rules
- [Coding conventions you care about]
- [Things Claude should NOT do]
- [Things Claude should ALWAYS do]

## Current State
- [What the app does today]
- [What's working, what's not]
```

**Keep it honest.** Only put things you've actually decided. Every word matters. Don't over-engineer this file with aspirational rules you won't follow. Write what's true about your project today. This file changes rarely. When you make a big architectural decision (switch from REST to GraphQL, drop a dependency), update it.

**Example:**

```markdown
# TransitionAI

## Stack
- Python 3.11, FastAPI backend
- FFmpeg for video processing
- Replicate API for model inference
- SQLite for local state

## Architecture
- /api — FastAPI endpoints
- /core — video processing pipeline
- /models — ML model wrappers
- /output — generated videos land here

## Rules
- Always use async for API calls
- Never hardcode API keys, use .env
- Keep video clips under 5 seconds for cost control
- Test with a single frame before running full pipeline

## Current State
- Image-to-image working
- Transition videos generating but quality inconsistent
- Audio pipeline not started yet
```

---

## Step 2: Create NOW.md (1 min)

Create `NOW.md` in your project root. This is your session handoff note. For now, just write where you are:

```markdown
# NOW

## Status
First session with Session Handoff pattern.

## Next Step
[Whatever you're working on right now]

## Open Questions
- [Anything unresolved]
```

This file gets overwritten every time you run `/close`. Don't precious about it. It's a sticky note, not a document.

---

## Step 3: Create MEMORY.md (1 min)

Create `MEMORY.md` in your project root. This starts empty and grows over time:

```markdown
# Project Memory

Lessons learned, decisions made, things that cost time once and shouldn't again.

---
```

You'll add to this as you work. When you discover something expensive ("that API returns 200 on failure", "don't use library X because of Y"), it goes here.

---

## Step 4: Create /wake command (3 min)

Create the folder and file:

```
mkdir -p .claude/commands
```

> **Windows note:** Use `mkdir .claude\commands` instead. PowerShell doesn't support `-p` flag.

Create `.claude/commands/wake.md` and paste this exactly:

```markdown
# Wake Protocol

Read these files to restore session context:

1. Read `CLAUDE.md` — project identity and rules
2. Read `NOW.md` — where we left off last session
3. Read `MEMORY.md` — lessons learned so far

Then report back:
- What session number is this? (count from NOW.md or estimate)
- Where did we leave off?
- What's the next step?
- Any relevant memories that apply?

Pick up exactly where we left off. No re-explanation needed.
```

Now when you type `/wake` in Claude Code, it reads your state and picks up.

---

## Step 5: Create /close command (3 min)

Create `.claude/commands/close.md` and paste this exactly:

```markdown
# Close Protocol

Before ending this session, save state:

## 1. Update NOW.md
Overwrite NOW.md with:
- **Status**: One line summary of where we are
- **What happened this session**: Key things we shipped or discovered
- **Next step**: Exact task to pick up next time
- **Open questions**: Anything unresolved

Keep it under 15 lines. It's a handoff note, not a report.

## 2. Update MEMORY.md (only if needed)
If we learned something that will save time in future sessions, append it.
Examples:
- "Approach X doesn't work because of Y"
- "This API has quirk Z, always do W"
- "Library A conflicts with library B"

Don't add routine stuff. Only expensive lessons.

## 3. Update CLAUDE.md (rare)
Only if we made a major architectural decision that changes project rules.

## 4. Confirm
Show me the updated NOW.md so I can verify before we end.
```

> **Shortcut:** You can also just tell Claude "create a wake command with the following protocol" and paste the text. Claude will create the folder and file for you. Same for close. Either way works.

> **Tip:** Use the word "protocol", not "text." These are protocols. Every word shapes how Claude treats them.

> **Gotcha (especially Windows):** After creating new commands, you may need to restart Claude Code for `/wake` and `/close` to show up. Exit and start a new session. The commands will be there.

---

## How to use it

### Starting a session
```
claude
/wake
```

Claude reads your three files, tells you where you are, picks up.

### During a session
Just work normally. Build features, fix bugs, whatever.

### Ending a session
```
/close
```

Claude saves your state. NOW.md gets updated. MEMORY.md gets appended if you learned something. Next session picks up clean.

### When to close and restart
Claude Code has a context window. Every message, every file read, every tool call fills it up. When it's full, older context gets summarized and compressed. That compression loses your decisions. `/close` and restart before that happens. Fresh sessions with good state files beat long sessions with compacted memory every time.

### The rule
**Never just close the terminal.** Always `/close` first. 30 seconds now saves 10 minutes of re-explanation next time.

---

## What this looks like after a few sessions

**NOW.md after session 5:**
```markdown
# NOW

## Status
Transition pipeline working for single-room renders. Quality at 7/10.

## What happened this session
- Fixed the frame interpolation bug (was using linear, switched to slerp)
- Added retry logic for Replicate API timeouts
- Tested with 10 sample rooms, 8/10 passed quality check

## Next step
Tackle the 2 failed renders. Both are kitchen scenes with reflective surfaces.
Hypothesis: the model struggles with specular highlights in transitions.

## Open questions
- Should we add a pre-processing step to reduce reflections?
- Or switch to a different model for kitchen scenes?
```

**MEMORY.md after session 5:**
```markdown
# Project Memory

Lessons learned, decisions made, things that cost time once and shouldn't again.

---

### Frame interpolation
Linear interpolation causes ghosting on hard edges. Always use slerp.
Discovered session 3. Cost: 2 hours debugging.

### Replicate API
Times out ~10% of the time on videos longer than 3 seconds.
Always add retry with exponential backoff. Max 3 retries.

### Kitchen scenes
Reflective surfaces (granite, stainless steel) cause artifacts.
Still investigating. Don't promise kitchen renders until solved.
```

---

## FAQ

**Do I commit these files to git?**
Yes. CLAUDE.md and MEMORY.md are project knowledge. NOW.md is session state. All three belong in the repo. If you're working with a team, everyone benefits from shared CLAUDE.md and MEMORY.md.

**What if I forget to /close?**
Start next session with `/wake`. It'll pick up from the last saved NOW.md. You lose whatever happened in the un-closed session. That's the cost. Build the habit.

**How long before this pays off?**
Session 2. The first time `/wake` picks up exactly where you left off without you re-explaining anything, you'll feel it.

**Can I use this with Cursor too?**
The files work anywhere. CLAUDE.md is read by Claude Code automatically. For Cursor, you'd put the same content in their rules file. The wake/close commands are Claude Code specific, but the three-file pattern works with any AI tool. The files are the system. The commands are just convenience.

**What if MEMORY.md gets too long?**
After ~50 sessions, prune it. Remove entries that are no longer relevant (fixed bugs, abandoned approaches). Keep the lessons that still apply. Think of it as weeding a garden, not maintaining a database.

---

## Troubleshooting

**`/wake` doesn't show up after creating it?**
Restart Claude Code. Exit and start a new session. Commands created during a session aren't available until restart.

**Claude says it can't find NOW.md or MEMORY.md?**
Check the file is in your project root (same folder as CLAUDE.md). Check for typos in the filename. Case matters.

**Close didn't seem to write anything?**
Open NOW.md in your editor and check. If it's unchanged, run `/close` again. Sometimes Claude asks for confirmation before writing.

---

## Quick reference

| File | Purpose | Changes | Size |
|------|---------|---------|------|
| CLAUDE.md | Project identity and rules | Rarely (major decisions) | 30-100 lines |
| NOW.md | Where you left off | Every /close (overwritten) | Under 15 lines |
| MEMORY.md | Expensive lessons | When you learn something | Grows over time |
| wake.md | Start session command | Set once, forget | ~10 lines |
| close.md | End session command | Set once, forget | ~20 lines |

---

*Setup time: 15 minutes. Payoff: every session after that.*
