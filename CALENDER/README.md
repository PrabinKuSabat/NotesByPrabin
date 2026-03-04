# 📅 CALENDER

Time-based notes — the **temporal layer** of this vault. In the ACE framework, Calendar captures everything that is anchored to a specific point in time.

## What Lives Here

- **Daily Notes** — day-by-day journals, logs, and reflections
- **Weekly Reviews** — end-of-week reflections and planning
- **Monthly Reviews** — broader retrospectives and goal check-ins
- **Meeting Notes** — notes from lectures, discussions, and events
- **Fleeting Notes** — quick time-stamped captures to be processed later

## Why a Separate Calendar Folder?

Time-based notes have a different lifecycle from evergreen knowledge. Daily notes are written once and referenced later — they are not meant to be continuously updated like concept notes. Keeping them here prevents them from cluttering the knowledge structure.

## Dataview Integration

Many calendar notes use frontmatter (`date:`, `week:`) that can be queried using [Dataview](https://github.com/blacksmithgu/obsidian-dataview) to build dynamic dashboards and timelines:

```dataview
TABLE date, summary FROM "CALENDER"
SORT date DESC
LIMIT 7
```

> 📅 The Calendar folder is the journal of your intellectual life — it captures *when* you were thinking, not just *what* you were thinking.
