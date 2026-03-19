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

---

## Installation

Follow these steps in order. Each step must be completed before moving to the next.

### Step 1: Install Prerequisites

You need these installed on your machine before proceeding:

**1a. Python 3.10+**

Check if you have it:
```bash
python --version
```
If not installed, download from [python.org](https://www.python.org/downloads/). Make sure to check "Add Python to PATH" during installation on Windows.

**1b. Claude Code CLI**

Install via npm:
```bash
npm install -g @anthropic-ai/claude-code
```
Verify it works:
```bash
claude --version
```
See [Claude Code docs](https://docs.anthropic.com/en/docs/claude-code) for detailed installation instructions.

**1c. Zotero 7**

Download and install from [zotero.org](https://www.zotero.org/download/). Make sure you have some papers in your library.

**1d. Obsidian**

Download and install from [obsidian.md](https://obsidian.md/). Create or open the vault you want to use for paper reading.

### Step 2: Configure Zotero

Open Zotero and enable the local API so other applications can access your library:

1. Open Zotero
2. Go to **Edit → Settings** (Windows/Linux) or **Zotero → Settings** (macOS)
3. Click the **Advanced** tab
4. Check **"Allow other applications on this computer to communicate with Zotero"**
5. Keep Zotero running (it must be open whenever you use the paper reading commands)

### Step 3: Install the Zotero MCP Server

This is the bridge that lets Claude access your Zotero library.

```bash
pip install zotero-mcp-server
```

Verify it installed correctly:
```bash
zotero-mcp --help
```

You should see the help output with available commands. If you get "command not found", make sure Python's Scripts directory is in your PATH.

### Step 4: Install Claudian in Obsidian

Claudian is the plugin that runs Claude Code inside Obsidian. It is **not in the Obsidian community plugin store yet**, so you need to install it manually.

**Option A: Using BRAT (recommended — handles auto-updates)**

1. In Obsidian, go to **Settings → Community plugins → Browse**
2. Search for **"BRAT"** and install it
3. Enable BRAT in Settings → Community plugins
4. Open the command palette (Ctrl/Cmd + P), type **"BRAT: Add a beta plugin"**
5. Enter the URL: `https://github.com/YishenTu/claudian`
6. Click **Add Plugin**
7. Go to **Settings → Community plugins** and enable **Claudian**

**Option B: Manual install from GitHub release**

1. Go to [Claudian releases](https://github.com/YishenTu/claudian/releases)
2. Download `main.js`, `manifest.json`, and `styles.css` from the latest release
3. In your vault, create the folder: `.obsidian/plugins/claudian/`
4. Copy the 3 downloaded files into that folder
5. In Obsidian, go to **Settings → Community plugins** and enable **Claudian**

**After enabling Claudian:**

1. Go to **Settings → Claudian**
2. Make sure the Claude CLI path is detected (if not, set it manually under Advanced → Claude CLI path)
3. Test it works: open the Claudian chat panel and type "hello"

### Step 5: Install the Zotero MCP Server in Claudian

Tell Claudian where to find the Zotero MCP server so Claude can access your library.

1. In your vault, create the folder `.claude/` if it doesn't exist
2. Create the file `.claude/settings.json` with this content:

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

**On Windows**, the `zotero-mcp` command may not be found by Claudian. If that happens, use the full path to the executable instead:

```json
{
  "mcpServers": {
    "zotero": {
      "command": "C:/Users/<your-username>/AppData/Local/Programs/Python/Python3xx/Scripts/zotero-mcp.exe",
      "env": {
        "ZOTERO_LOCAL": "true"
      }
    }
  }
}
```

To find the exact path, run:
```bash
where zotero-mcp
```

**Alternatively**, you can configure this through Claudian's UI:
1. Go to **Settings → Claudian → MCP Servers**
2. Add a new MCP server with the configuration above

### Step 6: Clone This Repo and Install Skills

```bash
git clone https://github.com/Lewen-Cai/paper-read-obsidian.git
```

Now symlink the skills into your vault's `.claude/skills/` directory:

**macOS / Linux:**
```bash
cd /path/to/your/vault
mkdir -p .claude/skills
ln -s /path/to/paper-read-obsidian/skills/read-papers .claude/skills/read-papers
ln -s /path/to/paper-read-obsidian/skills/review-papers .claude/skills/review-papers
ln -s /path/to/paper-read-obsidian/skills/library-papers .claude/skills/library-papers
ln -s /path/to/paper-read-obsidian/skills/log-papers .claude/skills/log-papers
```

**Windows (run Command Prompt as Administrator):**
```cmd
cd C:\path\to\your\vault
mkdir .claude\skills
mklink /D .claude\skills\read-papers C:\path\to\paper-read-obsidian\skills\read-papers
mklink /D .claude\skills\review-papers C:\path\to\paper-read-obsidian\skills\review-papers
mklink /D .claude\skills\library-papers C:\path\to\paper-read-obsidian\skills\library-papers
mklink /D .claude\skills\log-papers C:\path\to\paper-read-obsidian\skills\log-papers
```

**Windows alternative (copy instead of symlink — no admin needed, but won't auto-update):**
```bash
xcopy /E /I C:\path\to\paper-read-obsidian\skills\read-papers .claude\skills\read-papers
xcopy /E /I C:\path\to\paper-read-obsidian\skills\review-papers .claude\skills\review-papers
xcopy /E /I C:\path\to\paper-read-obsidian\skills\library-papers .claude\skills\library-papers
xcopy /E /I C:\path\to\paper-read-obsidian\skills\log-papers .claude\skills\log-papers
```

### Step 7: Create Vault Folders

Create the folder structure for your papers:

```bash
cd /path/to/your/vault
mkdir -p Papers Topics "Reading Plans"
```

Or create them manually in Obsidian: right-click in the file explorer and create new folders named `Papers`, `Topics`, and `Reading Plans`.

### Step 8: Copy Config (Optional)

Copy the default config file to your vault root:

```bash
cp /path/to/paper-read-obsidian/templates/config.yaml /path/to/your/vault/config.yaml
```

This sets the default language for AI-generated content to English. Edit it to change the language.

### Step 9: Verify Everything Works

1. Make sure **Zotero is running** with some papers in your library
2. Open **Obsidian** with your vault
3. Open the **Claudian** chat panel
4. Type: `/library-papers recent`
5. You should see a list of your recently added Zotero papers

If you get an error about connecting to Zotero, check:
- Zotero is running
- "Allow other applications to communicate with Zotero" is enabled (Step 2)
- The MCP server is configured correctly in `.claude/settings.json` (Step 5)
- `zotero-mcp` is in your PATH (run `zotero-mcp --help` in terminal)

---

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
├── config.yaml                 # Preferences (output language, etc.)
└── .claude/
    ├── settings.json           # MCP server config (Step 5)
    └── skills/                 # Symlinked skills (Step 6)
        ├── read-papers/
        ├── review-papers/
        ├── library-papers/
        └── log-papers/
```

## Configuration

The `config.yaml` file in your vault root controls defaults:

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

## Troubleshooting

### "Could not connect to Zotero"
- Make sure Zotero is running
- Check that "Allow other applications to communicate with Zotero" is enabled in Zotero Settings → Advanced
- Verify `zotero-mcp --help` works in your terminal

### Skills not showing up as slash commands
- Verify the skill files exist in `.claude/skills/` inside your vault
- Each skill folder should contain a `SKILL.md` file
- Restart Claudian after adding skills

### "zotero-mcp: command not found"
- Make sure `pip install zotero-mcp-server` completed successfully
- On Windows, check that Python's Scripts directory is in your PATH
- Try using the full path to `zotero-mcp` in your `.claude/settings.json`

### MCP server not connecting
- Check `.claude/settings.json` exists in your vault root and has the correct JSON
- On Windows, you may need the full path to `zotero-mcp.exe` instead of just `zotero-mcp`
- Restart Claudian/Obsidian after changing MCP configuration

## License

MIT
