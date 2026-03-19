---
name: review-papers
description: Generate a post-reading synthesis for the paper you just finished. Connects to your vault knowledge, updates topic notes, and recommends what to read next.
---

# /review-papers

You are generating a post-reading synthesis for a paper the user has finished reading. This creates connections to their existing knowledge and suggests next steps.

## Step 1: Identify the Paper

Look at the user's currently open note. Determine which paper folder it belongs to by checking:
1. Is the open file inside a `Papers/<paper-slug>/` directory?
2. Does it have frontmatter with a `title` or `zotero-item-id` field?

If you cannot identify the paper, ask: **"Which paper would you like to review?"** and list paper folders in `Papers/`.

## Step 2: Gather Context

Read all available sources for this paper:

1. **`Papers/<paper-slug>/ai-analysis.md`** — the pre-reading analysis
2. **`Papers/<paper-slug>/my-notes.md`** — the user's personal notes
3. **`Papers/<paper-slug>/logs/`** — all session logs (read every file in the folder)
4. **Paper fulltext** — use `zotero_get_item_fulltext` with the `zotero-item-id` from frontmatter
5. **Existing vault context** — use the Glob tool to find all files in `Topics/` and `Papers/*/synthesis.md` to understand what the user has already read

If `my-notes.md` is empty or barely filled in, warn the user:
> "Your personal notes are mostly empty. I can still generate a synthesis based on the AI analysis and paper content, but it will be more generic. Want to proceed, or would you like to add notes first?"

## Step 3: Generate Synthesis

Read the `output-language` field from the paper's `ai-analysis.md` frontmatter. Generate all content in that language.

Create `Papers/<paper-slug>/synthesis.md`:

```markdown
---
title: "{{title}}"
type: synthesis
reviewed-date: YYYY-MM-DD
output-language: {{language}}
---

# Synthesis: {{title}}

## Summary
<!-- 2-3 paragraph summary of the paper in the user's own context — reference their notes and questions from logs -->

## Key Contributions
<!-- What this paper adds to the field — be specific and educational -->

## Connections to Your Knowledge
<!-- Link to other papers in the vault and topic notes. Use [[wikilink]] syntax for Obsidian. -->
<!-- Example: "This builds on the attention mechanism you studied in [[attention-is-all-you-need/synthesis]]" -->

## Strengths & Limitations
<!-- Balanced assessment — what the paper does well and where it falls short -->

## Open Questions
<!-- Questions that remain after reading, drawn from logs and the paper itself -->

## Recommended Next Reading
<!-- 3-5 papers to read next, with one sentence each explaining why -->
<!-- These come from both web search AND Zotero library -->
```

Fill in every section with substantive, educational content. The synthesis should:
- Reference the user's actual notes and questions from logs (not generic)
- Use `[[wikilink]]` syntax for all vault cross-references
- Be written for a student learning the field

## Step 4: Update Topic Notes

Scan the synthesis for key concepts. For each major concept (3-5 per paper, not every keyword):

1. Check if `Topics/<concept-slug>.md` exists
2. If it exists: append a new entry linking to this paper's synthesis
3. If it doesn't exist: create it

**New topic note format:**

```markdown
---
topic: "{{concept name}}"
type: topic
---

# {{Concept Name}}

## Related Papers
- [[Papers/<paper-slug>/synthesis|{{paper title}}]] — {{one-line relevance description}}
```

**Appending to existing topic note:** Add a new line under `## Related Papers`:

```markdown
- [[Papers/<paper-slug>/synthesis|{{paper title}}]] — {{one-line relevance description}}
```

Only create/update topics for genuinely important concepts (3-5 per paper), not every keyword.

## Step 5: Recommend Next Papers

Search for related papers using two sources:

1. **Web search** (primary) — search for papers related to the key topics and contributions of this paper. Focus on:
   - Foundational papers this one builds on
   - Follow-up work that extends this research
   - Alternative approaches to the same problem
   - Accessible survey papers if the user seems early in their learning

2. **Zotero library** — use `zotero_search_items` (or `zotero_semantic_search` if available) to find related papers the user already has but may not have read yet.

**If web search is unavailable** (permissions not granted), fall back to Zotero-only recommendations and inform the user: "Web search is not available. Recommendations are based on your Zotero library only. Grant web search permissions for broader suggestions."

Present recommendations as:

> **Recommended Next Reading:**
>
> **From the web:**
> 1. "Paper Title" (Author, Year) — Why: ...
> 2. ...
>
> **Already in your Zotero library:**
> 1. "Paper Title" (Author, Year) — Why: ...
> 2. ...
>
> Would you like to add any of the web recommendations to Zotero?

## Step 6: Update Status and Log

1. Update `ai-analysis.md` frontmatter: set `status: reviewed`
2. Append to `Papers/<paper-slug>/logs/YYYY-MM-DD.md`:

```markdown

---

## Review Session: HH:MM

### Actions
- Generated synthesis.md
- Updated topic notes: {{list of topics}}
- Recommended next papers: {{count}} from web, {{count}} from Zotero
```

## Step 7: Confirm

Tell the user:

> "Review complete for **{{title}}**:
> - `synthesis.md` — Your reading synthesis with connections to your vault
> - Updated topic notes: {{list of [[wikilinks]]}}
> - {{N}} paper recommendations (check the synthesis for details)
>
> You can continue exploring the recommendations, or use `/read-papers` to start your next paper."
