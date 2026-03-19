---
name: read-papers
description: Start the AI-assisted reading workflow for a paper from your Zotero library. Searches Zotero, lets you pick a paper and template depth, then generates a pre-reading analysis note.
---

# /read-papers

You are helping a student set up a new paper for AI-assisted reading. Follow these steps exactly.

## Input

The user provides search terms via `$ARGUMENTS`: paper title keywords, arXiv ID, or DOI.

If `$ARGUMENTS` is empty, ask the user what paper they want to read.

## Step 1: Search Zotero

Use `zotero_search_items` with the user's query. Present the results as a numbered list showing:
- Title
- Authors (first author + "et al." if more than 2)
- Year
- Publication venue (if available)

If no results are found, tell the user:
> "No papers found in your Zotero library for that search. Try different keywords, or make sure the paper is added to Zotero first."

If results are found, ask: **"Which paper? (enter number)"**

## Step 2: Get Paper Metadata

Once the user picks a paper, use `zotero_get_item_metadata` to get the full metadata: title, authors, year, DOI, abstract.

Store these values — you will need them for the template.

## Step 3: Choose Template

Ask the user:

> **Which template would you like?**
> 1. **Detailed** — Background, key terms, guiding questions, section-by-section notes, math & formulas, code & implementation. Best for complex or foundational papers.
> 2. **Simple** — One-paragraph overview and key takeaways. Best for lighter reads or quick understanding.

Wait for the user's choice.

## Step 4: Read Paper Content

Use `zotero_get_item_fulltext` to extract the paper's text content.

If fulltext is not available:
- Warn the user: "Could not extract full text from this paper. I'll generate the analysis from the abstract and metadata only."
- Proceed using the abstract from Step 2.

Also use `zotero_get_annotations` to check if the user has any existing highlights or annotations in Zotero.

## Step 5: Check for Existing Notes

Check if a folder already exists at `Papers/<paper-slug>/` where `<paper-slug>` is the paper title converted to lowercase-kebab-case (e.g., "Attention Is All You Need" → `attention-is-all-you-need`).

If the folder exists, ask:
> "You already have notes for this paper. Would you like to:
> 1. Regenerate the AI analysis (your personal notes will be preserved)
> 2. Open the existing notes instead"

If regenerating, only overwrite `ai-analysis.md`. Never touch `my-notes.md` or `synthesis.md`.

## Step 6: Generate Notes

Read the template file from this skill's directory:
- If detailed: read `${CLAUDE_SKILL_DIR}/detailed-template.md`
- If simple: read `${CLAUDE_SKILL_DIR}/simple-template.md`

Check if `config.yaml` exists in the vault root. If it has an `output-language` field, use that value instead of the default `en`.

Create the paper folder: `Papers/<paper-slug>/`
Create the logs subfolder: `Papers/<paper-slug>/logs/`

**Create `Papers/<paper-slug>/ai-analysis.md`:**

Use the chosen template structure. Replace all `{{placeholder}}` values with actual metadata. Fill in all `<!-- AI-generated -->` sections with substantive content based on the paper's fulltext (or abstract if fulltext unavailable). The content should be:
- Written in the language specified by `output-language`
- Targeted at a student who is learning this field
- Clear, educational, and thorough
- Connected to the user's existing knowledge where possible (check `Topics/` folder for related concept notes)
- If the user has existing Zotero annotations (from Step 4), incorporate their highlights and margin notes into the relevant template sections to personalize the analysis

**Create `Papers/<paper-slug>/my-notes.md`:**

```markdown
---
title: "{{title}}"
type: personal-notes
---

# My Notes: {{title}}

<!-- This file is for your personal notes while reading the paper. -->
<!-- Claude will never overwrite this file. -->
```

## Step 7: Log

Create `Papers/<paper-slug>/logs/YYYY-MM-DD.md` (using today's date):

```markdown
# Session: YYYY-MM-DD

## Setup
- **Paper:** {{title}}
- **Template:** detailed/simple
- **Fulltext available:** yes/no
- **Existing annotations:** yes/no
- **Output language:** {{language}}

## Actions
- Created ai-analysis.md with pre-reading analysis
- Created my-notes.md (empty, ready for user)
```

## Step 8: Done

Tell the user:

> "Reading notes are set up for **{{title}}**. Here's what I created:
> - `Papers/<paper-slug>/ai-analysis.md` — Pre-reading analysis ({{template}} template)
> - `Papers/<paper-slug>/my-notes.md` — Your personal notes (empty, ready for you)
>
> Open `ai-analysis.md` to review the background, key terms, and guiding questions before you start reading. Take your notes in `my-notes.md` as you go. When you're done reading, use `/review-papers` to generate a synthesis."
