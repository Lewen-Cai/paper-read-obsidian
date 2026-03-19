# paper-read-obsidian

AI-assisted paper reading for Obsidian. Connect your Zotero library to Claude Code and get structured reading notes, interactive Q&A, and post-reading synthesis — all from within Obsidian.

Built for students and early researchers learning a field.

## What It Does

- **`/read-papers`** — Search your Zotero library, pick a paper, choose a template (detailed or simple), and get an AI-generated pre-reading analysis with background concepts, key terms, and guiding questions
- **`/review-papers`** — After reading, generate a synthesis that connects the paper to your existing knowledge, updates topic notes, and recommends what to read next (from the web and your Zotero library)
- **`/library-papers`** — Browse and search your Zotero library: recent papers, keyword search, collections, unread papers
- **`/log-papers`** — Save a session summary before closing Obsidian so Claude can pick up where you left off next time

Between commands, just talk to Claude naturally — ask questions about the paper, request explanations, discuss concepts. Claude always sees your currently open note.

## Prerequisites

- [Zotero 7](https://www.zotero.org/) (running locally)
- [Obsidian](https://obsidian.md/)
- [Claudian](https://github.com/YishenTu/claudian) (Claude Code plugin for Obsidian)
- [Claude Code CLI](https://docs.anthropic.com/en/docs/claude-code)
- Python 3.10+ with pip

## Installation

Open Claudian in your Obsidian vault and say:

> "Install arxiv-ob from https://github.com/\<your-username\>/arxiv-ob"

Claude will handle cloning the repo, installing the Zotero MCP server, setting up skills, and creating the vault folder structure.

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
# From your vault directory
mkdir -p .claude/skills
ln -s /path/to/arxiv-ob/skills/read-papers .claude/skills/read-papers
ln -s /path/to/arxiv-ob/skills/review-papers .claude/skills/review-papers
ln -s /path/to/arxiv-ob/skills/library-papers .claude/skills/library-papers
ln -s /path/to/arxiv-ob/skills/log-papers .claude/skills/log-papers
```

4. **Configure the MCP server** in `.claude/settings.json`:

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
cp /path/to/arxiv-ob/templates/config.yaml .
```

## Vault Structure

```
Your-Vault/
├── Papers/                     # One subfolder per paper
│   └── paper-title-slug/
│       ├── ai-analysis.md      # AI-generated pre-reading notes
│       ├── my-notes.md         # Your personal notes
│       ├── synthesis.md        # Post-reading synthesis (/review-papers)
│       └── logs/               # Session logs (date-based)
├── Topics/                     # Concept notes (auto-created by /review-papers)
├── Reading Plans/              # Optional reading sequences
└── config.yaml                 # Preferences (output language, etc.)
```

## Configuration

Create `config.yaml` in your vault root to customize defaults:

```yaml
# Language for AI-generated content (default: en)
output-language: en
```

You can override the language per-paper by editing the `output-language` field in any paper's frontmatter.

## Usage

### Start reading a paper

```
/read-papers attention mechanisms
```

### Ask questions while reading

Just talk to Claude — no command needed. Claude sees your open note.

### Save your session before closing

```
/log-papers
```

### Generate synthesis after finishing

```
/review-papers
```

### Browse your library

```
/library-papers recent
/library-papers search transformer
/library-papers collections
/library-papers unread
```

## License

MIT
