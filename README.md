# paper-read-obsidian

AI-assisted academic paper reading workflow for Obsidian, powered by Claude Code and Zotero.

Pick a paper from your Zotero library, get AI-generated pre-reading analysis, take notes with real-time AI assistance, and generate post-reading synthesis that connects to your growing knowledge base — all through natural conversation inside Obsidian.

Built for students and early researchers who are learning a field and want structured guidance while reading papers.

## How It Works

This project provides [Claude Code](https://docs.anthropic.com/en/docs/claude-code) skills that run inside Obsidian via the [Claudian](https://github.com/YishenTu/claudian) plugin. Claude connects to your local [Zotero](https://www.zotero.org/) library through the [zotero-mcp](https://github.com/54yyyu/zotero-mcp) server, so you can search, read, and manage papers entirely through conversation.

### Commands

| Command | What it does |
|---------|-------------|
| `/read-papers` | Search your Zotero library, pick a paper, choose a template depth (detailed or simple), and generate a pre-reading analysis with background concepts, key terms, guiding questions, math explanations, and more |
| `/review-papers` | After finishing a paper, generate a synthesis that summarizes your reading, connects to other papers in your vault, updates topic notes, and recommends what to read next (from the web and your Zotero library) |
| `/library-papers` | Browse your Zotero library — recent papers, keyword search, collections, or find papers you haven't read yet |
| `/log-papers` | Save a session summary before closing Obsidian so Claude can pick up where you left off next time |

Between commands, just talk to Claude naturally — ask questions about the paper, request explanations of equations, discuss concepts. Claude always sees your currently open note through Claudian.

## Prerequisites

- [Zotero 7](https://www.zotero.org/) — running locally with "Allow other applications to communicate with Zotero" enabled (Settings → Advanced)
- [Obsidian](https://obsidian.md/)
- [Claudian](https://github.com/YishenTu/claudian) — Claude Code plugin for Obsidian
- [Claude Code CLI](https://docs.anthropic.com/en/docs/claude-code)
- [Python 3.10+](https://www.python.org/) with pip

## Installation

### Quick Install (via Claudian)

Open Claudian in your Obsidian vault and say:

> "Install paper-read-obsidian from https://github.com/Lewen-Cai/paper-read-obsidian"

Claude will clone the repo, install the Zotero MCP server, set up skills, and create the vault folder structure automatically.

### Manual Installation

1. **Install the Zotero MCP server:**

```bash
pip install zotero-mcp-server
```

2. **Clone this repo** (anywhere on your machine):

```bash
git clone https://github.com/Lewen-Cai/paper-read-obsidian.git
```

3. **Symlink skills** into your vault's `.claude/skills/`:

```bash
# From your Obsidian vault directory
mkdir -p .claude/skills
ln -s /path/to/paper-read-obsidian/skills/read-papers .claude/skills/read-papers
ln -s /path/to/paper-read-obsidian/skills/review-papers .claude/skills/review-papers
ln -s /path/to/paper-read-obsidian/skills/library-papers .claude/skills/library-papers
ln -s /path/to/paper-read-obsidian/skills/log-papers .claude/skills/log-papers
```

4. **Configure the MCP server** in your vault's `.claude/settings.json`:

```json
{
  "mcpServers": {
    "zotero": {
      "command": "zotero-mcp",
      "env": {
        "ZOTERO_LOCAL": "true"
      }
    }
  }
}
```

5. **Enable Zotero's local API:** In Zotero → Settings → Advanced, check "Allow other applications to communicate with Zotero."

6. **Create vault folders:**

```bash
mkdir -p Papers Topics "Reading Plans"
```

7. **Copy the config template** (optional):

```bash
cp /path/to/paper-read-obsidian/templates/config.yaml .
```

## Vault Structure

Each paper gets its own folder with AI-generated content separated from your personal notes:

```
Your-Vault/
├── Papers/
│   └── attention-is-all-you-need/
│       ├── ai-analysis.md      # AI-generated pre-reading analysis
│       ├── my-notes.md         # Your personal notes (never overwritten by AI)
│       ├── synthesis.md        # Post-reading synthesis (from /review-papers)
│       └── logs/
│           ├── 2026-03-19.md   # Session logs (date-based)
│           └── 2026-03-21.md
├── Topics/                     # Concept notes linking papers (auto-created by /review-papers)
│   ├── attention-mechanisms.md
│   └── transformer-architecture.md
├── Reading Plans/              # Optional reading sequences
└── config.yaml                 # Preferences (output language, etc.)
```

## Configuration

Create `config.yaml` in your vault root to customize defaults:

```yaml
# Language for AI-generated content (default: en)
# Can be overridden per-paper in the note's frontmatter
output-language: en
```

## Usage

### Start reading a paper

```
/read-papers attention mechanisms
```

Claude searches your Zotero library, lets you pick a paper, asks whether you want a detailed or simple template, then generates the pre-reading analysis.

### Ask questions while reading

No command needed — just talk to Claude. It sees your currently open note and can reference the paper's content, your notes, and your existing vault knowledge.

### Save your session before closing

```
/log-papers
```

Claude summarizes what you discussed and saves it to a date-based log file. Next time you return, it reads the logs to pick up where you left off.

### Generate synthesis after finishing

```
/review-papers
```

Claude reads your notes, the paper, and all session logs to generate a synthesis with connections to your vault. It also recommends related papers from the web and your Zotero library.

### Browse your library

```
/library-papers recent           # Recently added papers
/library-papers search diffusion # Search by keyword
/library-papers collections      # List Zotero collections
/library-papers unread           # Papers without notes yet
```

## Templates

Two templates are available when starting a new paper:

- **Detailed** — Background concepts, key terms, guiding questions, section-by-section notes, math & formula explanations, code & implementation notes. Best for complex or foundational papers.
- **Simple** — One-paragraph overview and key takeaways. Best for lighter reads or quick understanding.

Templates are in `templates/` for reference. You can customize them by editing the files in `skills/read-papers/`.

## License

MIT
