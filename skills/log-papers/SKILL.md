---
name: log-papers
description: Save a session summary before exiting Obsidian. Captures what you discussed, insights gained, and where you left off so Claude can resume context in the next session.
---

# /log-papers

You are saving a session summary for the user's current paper reading session. This preserves context that would otherwise be lost between sessions.

## Step 1: Identify the Paper

Look at the user's currently open note. Determine which paper folder it belongs to by checking:
1. Is the open file inside a `Papers/<paper-slug>/` directory?
2. Does it have frontmatter with a `title` or `zotero-item-id` field?

If you cannot identify the paper, ask: **"Which paper would you like to save a session log for?"** and search the `Papers/` directory for existing paper folders.

## Step 2: Review the Session

Look at the current conversation history. Summarize:
- **Questions asked:** What did the user ask about?
- **Key insights:** What important things were discussed or clarified?
- **Progress:** What sections or topics of the paper were covered?
- **Where we left off:** What was the user working on when they decided to stop?
- **Open questions:** Any unresolved questions or topics to revisit?

## Step 3: Write the Log

Check if `Papers/<paper-slug>/logs/YYYY-MM-DD.md` already exists (using today's date).

**If the file exists:** Append a new session section with a separator:

```markdown

---

## Session: HH:MM

### Questions Discussed
- ...

### Key Insights
- ...

### Progress
- ...

### Left Off At
- ...

### Open Questions
- ...
```

**If the file does not exist:** Create it:

```markdown
# Log: YYYY-MM-DD

## Session: HH:MM

### Questions Discussed
- ...

### Key Insights
- ...

### Progress
- ...

### Left Off At
- ...

### Open Questions
- ...
```

## Step 4: Update Status

Read the `ai-analysis.md` frontmatter for this paper. If `status` is `unread`, update it to `reading`.

## Step 5: Confirm

Tell the user:

> "Session saved to `Papers/<paper-slug>/logs/YYYY-MM-DD.md`. Next time you return to this paper, I'll read the logs to pick up where you left off."
