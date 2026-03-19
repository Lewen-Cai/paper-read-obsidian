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

- [Zotero 7](https://www.zotero.org/) with "Allow other applications on this computer to communicate with Zotero" enabled (Settings → Advanced)
- [Obsidian](https://obsidian.md/) with [Claudian](https://github.com/YishenTu/claudian) plugin installed and working
- [Claude Code CLI](https://docs.anthropic.com/en/docs/claude-code)
- [Python 3.10+](https://www.python.org/) with pip

---

## Installation

### Step 1: Install the Zotero MCP Server

```bash
pip install zotero-mcp-server
```

### Step 2: Find the Zotero MCP Executable Path

You need the **full path** to `zotero-mcp` for configuring Claudian. This is important because Claudian does not inherit your shell's PATH — it spawns processes directly.

**Find the path:**

```bash
# Windows
where zotero-mcp

# macOS / Linux
which zotero-mcp
```

If `where`/`which` returns nothing, your Python package manager installed it somewhere not in your system PATH. Find it manually:

```bash
# pip-managed Python (common)
# Windows: C:\Users\<you>\AppData\Local\Programs\Python\Python3xx\Scripts\zotero-mcp.exe
# macOS/Linux: ~/.local/bin/zotero-mcp

# pixi-managed Python
# Windows: C:\Users\<you>\.pixi\envs\python\Scripts\zotero-mcp.exe
# macOS/Linux: ~/.pixi/envs/python/bin/zotero-mcp

# conda-managed Python
# Windows: C:\Users\<you>\miniconda3\Scripts\zotero-mcp.exe
# macOS/Linux: ~/miniconda3/bin/zotero-mcp
```

**Save this full path** — you need it in Step 4.

### Step 3: Clone This Repo and Install Skills

Clone anywhere on your machine:

```bash
git clone https://github.com/Lewen-Cai/paper-read-obsidian.git
```

Symlink the skill folders into your vault's `.claude/skills/` directory so Claudian can discover them.

**macOS / Linux:**
```bash
cd /path/to/your/vault
mkdir -p .claude/skills
ln -s /path/to/paper-read-obsidian/skills/read-papers .claude/skills/read-papers
ln -s /path/to/paper-read-obsidian/skills/review-papers .claude/skills/review-papers
ln -s /path/to/paper-read-obsidian/skills/library-papers .claude/skills/library-papers
ln -s /path/to/paper-read-obsidian/skills/log-papers .claude/skills/log-papers
```

**Windows (Command Prompt as Administrator):**
```cmd
cd C:\path\to\your\vault
mkdir .claude\skills
mklink /D .claude\skills\read-papers C:\path\to\paper-read-obsidian\skills\read-papers
mklink /D .claude\skills\review-papers C:\path\to\paper-read-obsidian\skills\review-papers
mklink /D .claude\skills\library-papers C:\path\to\paper-read-obsidian\skills\library-papers
mklink /D .claude\skills\log-papers C:\path\to\paper-read-obsidian\skills\log-papers
```

**Windows alternative (copy, no admin needed, but won't auto-update):**
```cmd
xcopy /E /I C:\path\to\paper-read-obsidian\skills\read-papers .claude\skills\read-papers
xcopy /E /I C:\path\to\paper-read-obsidian\skills\review-papers .claude\skills\review-papers
xcopy /E /I C:\path\to\paper-read-obsidian\skills\library-papers .claude\skills\library-papers
xcopy /E /I C:\path\to\paper-read-obsidian\skills\log-papers .claude\skills\log-papers
```

### Step 4: Configure the Zotero MCP Server in Claudian

> **Important:** Claudian manages MCP servers through its own settings UI, **not** through `.claude/settings.json`. You must configure the MCP server in Claudian's settings panel.

1. In Obsidian, go to **Settings → Claudian → MCP Servers**
2. Click **Add** to create a new MCP server
3. Fill in:
   - **Server name:** `Zotero-mcp`
   - **Type:** `stdio (local command)`
   - **Command:** The full path from Step 2 (e.g., `C:\Users\Admin\.pixi\envs\python\Scripts\zotero-mcp.EXE`)
   - **Environment variables:**
     ```
     ZOTERO_LOCAL=true
     ```
4. Click **Update**

#### Windows: "Connection closed" or "Connection failed" error

If you get a connection error when verifying, Claudian likely can't find the Python runtime that `zotero-mcp` depends on. This happens when Python is managed by pixi, conda, or installed in a non-standard location.

**Fix:** Add a `PATH` environment variable pointing to your Python installation directory. In the Claudian MCP settings, set environment variables to:

```
ZOTERO_LOCAL=true
PATH=C:\path\to\your\python;C:\path\to\your\python\Scripts;C:\path\to\your\python\Library\bin
```

For example, with pixi-managed Python:
```
ZOTERO_LOCAL=true
PATH=C:\Users\Admin\.pixi\envs\python;C:\Users\Admin\.pixi\envs\python\Scripts;C:\Users\Admin\.pixi\envs\python\Library\bin
```

With conda-managed Python:
```
ZOTERO_LOCAL=true
PATH=C:\Users\Admin\miniconda3;C:\Users\Admin\miniconda3\Scripts;C:\Users\Admin\miniconda3\Library\bin
```

#### Verify

1. Make sure **Zotero is running**
2. In Claudian MCP settings, click **Verify** on your Zotero-mcp server
3. It should show "Connection successful"

> **Note:** The first verification may time out because the server loads heavy dependencies (chromadb, sentence-transformers). If you get a timeout, try verifying again — subsequent starts are faster. You can also try using it directly by typing `/library-papers recent` in the Claudian chat.

### Step 5: Create Vault Folders

Create these folders in your vault (manually in Obsidian or via terminal):

```bash
mkdir -p Papers Topics "Reading Plans"
```

### Step 6: Copy Config (Optional)

```bash
cp /path/to/paper-read-obsidian/templates/config.yaml /path/to/your/vault/config.yaml
```

This sets the default language for AI-generated content. Edit `output-language` to change it.

---

## Vault Structure

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
├── Reading Plans/              # Optional reading sequences
├── config.yaml                 # Preferences (output language, etc.)
└── .claude/
    └── skills/                 # Symlinked skills from this repo
```

## Configuration

`config.yaml` in your vault root:

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

### Ask questions while reading

Just talk to Claude — no command needed. It sees your open note.

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
/library-papers search diffusion
/library-papers collections
/library-papers unread
```

## Templates

- **Detailed** — Background, key terms, guiding questions, section-by-section notes, math & formulas, code & implementation. Best for complex papers.
- **Simple** — One-paragraph overview and key takeaways. Best for lighter reads.

Customize by editing files in `skills/read-papers/`.

## Troubleshooting

| Problem | Solution |
|---------|----------|
| "Could not connect to Zotero" | Make sure Zotero is running and "Allow other applications to communicate with Zotero" is enabled in Zotero Settings → Advanced |
| Skills not showing as slash commands | Check `.claude/skills/` has the skill folders with `SKILL.md` inside. Restart Claudian. |
| MCP "Connection closed" | Claudian can't find the Python runtime. Add the `PATH` environment variable in Claudian's MCP settings pointing to your Python installation directory (see Step 4). |
| MCP "Connection timeout" | First startup loads heavy dependencies and can be slow. Try verifying again, or just use `/library-papers recent` directly. |
| Zotero MCP tools not available | MCP servers must be configured in **Claudian's settings UI** (Settings → Claudian → MCP Servers), not in `.claude/settings.json`. |
| `where zotero-mcp` returns nothing | Your Python package manager installed it outside your system PATH. Check common locations listed in Step 2, or run `pip show zotero-mcp-server` to find the install location. |

## License

MIT
