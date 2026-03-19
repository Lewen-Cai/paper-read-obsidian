---
name: library-papers
description: Browse and search your Zotero library from within Obsidian. Subcommands: recent, search, collections, unread.
---

# /library-papers

You are helping the user browse their Zotero library. Parse `$ARGUMENTS` to determine the subcommand.

## Subcommand Routing

Parse the first word of `$ARGUMENTS`:

- `recent` → **Show Recent**
- `search <query>` → **Search Library**
- `collections` → **List Collections**
- `unread` → **Show Unread**
- Empty or unrecognized → **Show Help**

---

## Show Help

If no arguments or unrecognized subcommand:

> **Available commands:**
> - `/library-papers recent` — Show recently added papers
> - `/library-papers search <query>` — Search your library by keyword
> - `/library-papers collections` — List your Zotero collections
> - `/library-papers unread` — Show papers you haven't started reading yet

---

## Show Recent

Use `zotero_get_recent` with a limit of 10.

Present as a numbered list:
```
1. "Paper Title" — First Author et al. (2024)
2. ...
```

If the user wants to start reading one, tell them to use `/read-papers` with the paper title.

---

## Search Library

Extract the search query from `$ARGUMENTS` (everything after "search").

Use `zotero_search_items` with the query.

Present results as a numbered list (same format as recent). If no results, suggest trying different keywords or checking if the paper is in Zotero.

---

## List Collections

Use `zotero_get_collections`.

Present as a list:
```
Collection Name (N items)
```

If the user wants to see items in a collection, use `zotero_get_collection_items`.

---

## Show Unread

1. Use `zotero_get_recent` with a high limit (100) to get the library.
2. List all existing paper folders in `Papers/` using the Glob tool.
3. Cross-reference: papers in Zotero that do NOT have a corresponding folder in `Papers/` are "unread."

Present unread papers as a numbered list. If all papers have notes, say:

> "You have notes for all your recent Zotero papers! Add new papers to Zotero or use `/library-papers search` to find older ones."

---

## Error Handling

If any Zotero MCP tool call fails, tell the user:

> "Could not connect to Zotero. Make sure:
> 1. Zotero is running
> 2. 'Allow other applications to communicate with Zotero' is enabled in Zotero preferences
> 3. The zotero-mcp-server is configured in `.claude/settings.json`"
