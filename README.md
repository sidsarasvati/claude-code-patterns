# Claude Code Patterns

Design patterns for Claude Code. Built from production usage, not theory.

These patterns come from running Claude Code across 5 production projects, 60+ sessions, over the course of a year. Each one solves a real problem that cost real hours before the pattern existed.

## Patterns

### [Session Handoff](./session-handoff/)
**Problem:** Claude Code forgets everything when you close the terminal. You either keep one long chat (lossy compaction) or start fresh (amnesia).

**Solution:** Three files + two commands that give you explicit control over what persists across sessions. 15 minutes to set up. Pays off by session 2.

```
CLAUDE.md  → project identity (loaded automatically)
NOW.md     → where you left off (overwritten each close)
MEMORY.md  → expensive lessons (compounds forever)
/wake      → read state, resume
/close     → save state, capture learnings
```

[Setup guide](./session-handoff/) | [Starter templates](./session-handoff/templates/)

---

*More patterns coming. Follow the [AI Eating Software](https://sidsarasvati.substack.com) Substack for the thinking behind each pattern.*

---

By [Sid Sarasvati](https://x.com/sidjustice_). Founder at [Renovate AI](https://renovateai.app).
