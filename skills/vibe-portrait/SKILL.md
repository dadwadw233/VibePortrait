---
name: "vibe-portrait"
description: "Analyze the user's conversation history with AI coding assistants and generate a beautiful HTML personality portrait plus a persona skill. Use when the user wants to see their developer personality profile, generate their persona skill, MBTI-like type, famous person match, developer rating, or coding style analysis. Also use when the user says 'generate my portrait', 'analyze my personality', 'what kind of developer am I', 'think like XXX', '像XXX一样思考', or wants to import/merge history from another machine."
---

# Vibe Portrait

Generate a developer personality portrait from your AI conversation history.

This skill reads your Claude Code / Codex conversation history, analyzes your communication patterns, technical depth, decision-making style, and collaboration behavior, then produces:

1. **A self-contained HTML portrait page** with visual charts and personality insights
2. **A persona skill file** that captures your thinking patterns, methodology, and philosophy — loadable by any AI agent to "think like you"

It also supports importing history from multiple machines and loading community persona skills.

## Step 0: Ask analysis mode

Before reading any data, ask the user which analysis mode they prefer:

> **How thorough should the analysis be?**
>
> 1. **⚡ Quick (cost-efficient)** — Sample ~200 messages. Fast, low token cost, good enough for most portraits.
> 2. **🔍 Full (comprehensive)** — Read ALL messages. Higher token cost, but captures every nuance and evolution of your personality. Best for users with long history who want maximum accuracy.

If the user doesn't respond or says "just do it" / "默认" / "whatever", default to **Quick mode**.

In **Quick mode**, follow the sampling strategy described in Step 1c.
In **Full mode**, read all lines from `history.jsonl` (and any imported files). Skip the sampling strategy entirely. If the file exceeds 3000 lines, still read in batches (e.g., 500 lines at a time) to avoid tool errors, but process every line.

## Step 1: Locate and read conversation data

### 1a: Local history

Find the user's conversation history. Check these locations in order:

1. `~/.claude/history.jsonl` — primary source. Each line is a JSON object with a `display` field containing the user's message text.
2. `~/.claude/projects/` — subdirectories may contain additional `.jsonl` transcript files.
3. If on Codex, check `~/.codex/history/` or ask the user where their history is stored.

### 1b: Multi-machine sync via Git repo

Vibe Portrait uses a **private GitHub repo** to sync persona data across machines. This is the recommended approach for users with multiple development machines.

**First-time setup (if no portrait repo exists yet):**

After analysis is complete (Step 7), the skill will offer to create a private repo. See Step 7 for details.

**If the user already has a portrait repo:**

Ask the user for their portrait repo URL, or check if `~/.vibe-portrait-repo` exists (a file containing the repo URL, written during first-time setup).

```bash
# Check for existing repo config
cat ~/.vibe-portrait-repo 2>/dev/null
```

If found, clone or pull the repo into a temp directory:
```bash
# Clone if not already local
REPO_URL=$(cat ~/.vibe-portrait-repo)
REPO_DIR=~/.cache/vibe-portrait-sync
git clone "$REPO_URL" "$REPO_DIR" 2>/dev/null || (cd "$REPO_DIR" && git pull)
```

Then load existing analysis files from `$REPO_DIR/analysis/*.json`. When merging with the current machine's analysis:
- Each machine's analysis is stored as a separate JSON file: `analysis/{hostname}-{date}.json`
- To merge: average the numeric scores across all machine analyses, weighted by message count
- Union all quotes (deduplicate by text)
- Union all detected domains and tools
- The persona skill (`me/SKILL.md`) is always regenerated from the merged result

**If the user provides a `.jsonl` file directly (fallback):**

Read it as a supplementary source. Merge messages from all sources before sampling:
- Deduplicate messages with identical `display` content
- If timestamps are available, sort merged messages chronologically
- Treat all sources equally

### 1c: Read strategy

Use a bash command to count total lines first:
```bash
wc -l ~/.claude/history.jsonl
```

**Quick mode (default):** Sample strategically:
- Read the **first 50 lines** (early personality)
- Read the **last 100 lines** (current personality)
- Read **50 random lines from the middle** (evolution)
- Total: ≤200 messages per source file

**Full mode:** Read all lines. For large files (>500 lines), read in batches of 500 lines using offset/limit to avoid tool errors. Process every message.

**Parse each line as JSON and extract the `display` field.** Skip lines where `display` is empty, null, or a slash command (starts with `/`).

**Minimum threshold:** If fewer than 20 non-empty messages are found across all sources, inform the user that there isn't enough data for a meaningful portrait. Suggest they try again after more conversations.

## Step 2: Analyze across six dimensions

Read the analysis framework for detailed signal definitions:
→ `references/analysis-framework.md`

Analyze across six dimensions: Communication Style, Technical Breadth, Technical Depth, Decision Patterns, Collaboration Style, Work Rhythm. See the reference file for detailed signal definitions.

**Language detection (for i18n)**: if >50% of sampled messages are in Chinese, set `meta.lang = "zh"`. Otherwise `"en"`. This determines the portrait page language.

**For each dimension, produce:** a score (0-100), a one-line summary, and 2-3 evidence quotes (keep quotes short, anonymize sensitive content).

## Step 3: Compute personality type, rating, and famous match

### 3.1 MBTI Mapping
Read: → `references/mbti-mapping.md`

Map observed signals to four axes:
- **E/I**: delegation breadth vs self-reliance
- **S/N**: implementation focus vs architecture focus
- **T/F**: logic-driven vs people-aware
- **J/P**: structured planning vs exploratory iteration

Each axis: 0-100 score. Letter determined by which side of 50.

### 3.2 Developer Rating
Read: → `references/rating-rubric.md`

Rate on a six-tier scale (provide BOTH `label` for Chinese and `labelEn` for English):

| Tier | label (zh) | labelEn (en) | Emoji |
|------|-----------|--------------|-------|
| S+ | 夯爆了 | Legendary | 👑 |
| S | 夯 | Elite | 💎 |
| A | 人上人 | Above Average | ⭐ |
| B | NPC | NPC | 🤖 |
| C | 拉 | Below Average | 😅 |
| D | 拉完了 | Inactive | 💀 |

Always include strength highlights AND growth areas, even for top-tier ratings.

### 3.3 Famous Person Match
Read: → `references/famous-matching.md`

Match against ~30 figures spanning technologists, scientists, philosophers, and creators — across eras and cultures. Trait overlap is computed across communication style, technical/intellectual focus, decision pattern, ambition level, and collaboration approach.

## Step 4: Generate the portrait HTML

Read the HTML template:
→ `templates/portrait.html`

The template contains a `PORTRAIT_DATA` JavaScript object near the top of the `<body>`. Replace the ENTIRE `PORTRAIT_DATA` object with the computed data. The structure is:

```javascript
const PORTRAIT_DATA = {
  meta: {
    username: "...",         // infer from system username or ask
    generatedAt: "...",      // today's date
    messageCount: 0,         // actual count of sampled messages
    dateRange: "...",        // earliest to latest message date
    confidence: "...",       // "Early Sketch" / "Clear Portrait" / "Deep Portrait"
    lang: "..."              // "zh" if user's messages are predominantly Chinese, otherwise "en"
  },
  personality: {
    mbtiType: "XXXX",
    axes: {
      EI: { score: 0, letter: "X", label: "..." },
      SN: { score: 0, letter: "X", label: "..." },
      TF: { score: 0, letter: "X", label: "..." },
      JP: { score: 0, letter: "X", label: "..." }
    },
    summary: "..."
  },
  radar: {
    technicalDepth: 0,
    technicalBreadth: 0,
    communication: 0,
    decisionSpeed: 0,
    collaboration: 0,
    creativity: 0
  },
  rating: {
    tier: "...",
    label: "...",             // Chinese label: 夯爆了/夯/人上人/NPC/拉/拉完了
    labelEn: "...",           // English label: Legendary/Elite/Above Average/NPC/Below Average/Inactive
    emoji: "...",
    color: "...",
    reason: "...",
    strengthHighlights: [],
    growthAreas: []
  },
  famousPerson: {
    name: "...",
    emoji: "...",
    reason: "...",
    sharedTraits: []
  },
  communication: {
    languages: {},
    directnessScore: 0,
    avgMessageLength: 0,
    topKeywords: [],
    questionRatio: 0
  },
  technical: {
    domains: {},
    topTools: []
  },
  workRhythm: {
    hourlyActivity: [/* 24 integers, one per hour */],
    sessionPattern: "...",
    avgSessionLength: "...",
    projectCount: 0
  },
  quotes: [
    { text: "...", context: "..." }
    // 3-5 representative quotes
  ]
};
```

**IMPORTANT:**
- Replace ONLY the `PORTRAIT_DATA` object. Do not modify any other part of the template.
- All values must be valid JavaScript (strings quoted, arrays bracketed, no trailing commas).
- The rest of the template's JavaScript will automatically render everything from this object.
- Keep quote texts under 100 characters. Anonymize any sensitive data (API keys, tokens, passwords, personal URLs).

## Step 5: Generate persona skill

Read the persona skill template:
→ `references/persona-skill-template.md`

Generate a SKILL.md file that captures the user's personality as a loadable AI persona. This skill, when activated, allows any AI agent to "think like" the analyzed user.

**Derive the persona content from the analysis in Steps 2-3.** Specifically:
- **Thinking Patterns**: from technical depth analysis + decision patterns
- **Decision Framework**: from decision patterns + collaboration style
- **Communication Style**: from communication style analysis
- **Engineering Philosophy**: from technical depth + breadth + collaboration patterns
- **Anti-Patterns**: invert the strongest observed preferences
- **Signature Phrases**: from the collected quotes

**Write the persona skill to:**
```
~/.claude/skills/vibe-portrait-personas/me/SKILL.md
```

Create the directory if it doesn't exist. The `me/` path is reserved for the user's own persona. Community/downloaded personas go in sibling directories (e.g., `~/.claude/skills/vibe-portrait-personas/john-carmack/SKILL.md`).

## Step 6: Write output files

Write the following files to the current working directory:

**1. HTML Portrait:**
```
vibe-portrait-YYYY-MM-DD.html
```

**2. Portrait Image (auto-export):**

After writing the HTML, attempt to generate a PNG screenshot of the portrait. Try these methods in order:

- **If Playwright MCP is available** (check for `playwright_navigate` and `playwright_screenshot` tools):
  1. Navigate to the local HTML file: `file:///absolute/path/to/vibe-portrait-YYYY-MM-DD.html`
  2. Wait for charts to render (2-3 seconds)
  3. Take a full-page screenshot, save as `vibe-portrait-YYYY-MM-DD.png`

- **If Playwright is not available**, try bash:
  ```bash
  # Try with npx playwright if installed
  npx --yes playwright screenshot --full-page "file://$(pwd)/vibe-portrait-YYYY-MM-DD.html" "vibe-portrait-YYYY-MM-DD.png" 2>/dev/null
  ```

- **If neither works**, skip silently. Tell the user they can use the "Export as Image" button on the HTML page instead.

**3. Analysis JSON** (for sync/merge):
```
vibe-portrait-analysis-YYYY-MM-DD.json
```

Contents:
```json
{
  "version": "1.0",
  "machine": "<hostname from `hostname` command>",
  "exportedAt": "YYYY-MM-DD",
  "sourceFiles": ["~/.claude/history.jsonl"],
  "messageCount": 200,
  "portraitData": { /* the full PORTRAIT_DATA object */ }
}
```

**3. Persona skill** (also written in Step 5):
```
~/.claude/skills/vibe-portrait-personas/me/SKILL.md
```

## Step 7: Sync to portrait repo

After writing all output files, ask the user:

> **Sync to your portrait repo?**
>
> 1. **Yes — create a new repo** (first time)
> 2. **Yes — push to existing repo**
> 3. **No — keep everything local**

### Option 1: Create new portrait repo

1. Check `gh auth status`. If not authenticated, tell user to run `! gh auth login`.
2. Copy `repo-template/` contents into a temp directory.
3. Copy the generated analysis JSON, HTML portrait, and persona skill into the temp directory under `analysis/`, `portraits/`, and `me/` respectively.
4. **Fill in the README.md** — the template contains `{{PLACEHOLDER}}` markers. Read `repo-template/README.md` (see the file for the full list of placeholders) and replace them all with the user's analysis data. Key formatting rules:
   - Badge lists → inline code: `` `item1` `item2` ``
   - Domain bars → Unicode blocks: `**ML/AI** ██████████████░░░░░░ 30%` (20 chars wide, `█` filled, `░` empty)
   - Quotes → blockquotes: `> "text" — *context*`
   - Language breakdown → `"Chinese 65% · English 30% · Mixed 5%"`
5. `git init && git add -A && git commit -m "Initial portrait"`
6. `gh repo create my-vibe-portrait --private --source=. --push`
7. Save repo URL: `gh repo view --json url -q '.url' > ~/.vibe-portrait-repo`

### Option 2: Push to existing repo

1. Read repo URL from `~/.vibe-portrait-repo`.
2. Clone (or pull if already cached at `~/.cache/vibe-portrait-sync`).
3. Copy the latest analysis JSON, HTML, and persona skill into the repo directory.
4. Regenerate `README.md` using the same placeholder-filling process as Option 1.
5. `git add -A && git commit -m "Update portrait from $(hostname)" && git push`

### Option 3: Keep local

Do nothing. Files are already written locally.

## Step 8: Present results

Tell the user:
1. Where the HTML portrait was saved (open in browser)
2. Where the persona skill was installed (`~/.claude/skills/vibe-portrait-personas/me/`)
3. Whether the portrait repo was synced (and the repo URL if applicable)
4. A brief summary of key findings (MBTI type, rating, famous match)
5. How to use the persona: **"think like me"** or **"像我一样思考"**
6. How to sync from another machine: install vibe-portrait there, run it, choose "push to existing repo"

## Confidence levels

Based on total non-empty messages found:
- **< 20**: Do not generate. Tell user to accumulate more history.
- **20-50**: Generate with `confidence: "Early Sketch"`. Warn that results are approximate.
- **50-200**: Generate with `confidence: "Clear Portrait"`.
- **200+**: Generate with `confidence: "Deep Portrait"`.

## Output contract

This skill produces THREE outputs:

### 1. HTML Portrait (`vibe-portrait-YYYY-MM-DD.html`)
- Single self-contained file (only external dependencies: Tailwind CDN + Chart.js CDN)
- Opens correctly in any modern browser
- Contains all analysis data in the `PORTRAIT_DATA` JavaScript object
- Includes actual quotes (anonymized for sensitive content)

### 2. Persona Skill (`~/.claude/skills/vibe-portrait-personas/me/SKILL.md`)
- Valid Claude Code / Codex skill file with proper YAML frontmatter
- Under 200 lines, dense and actionable
- Activates on "think like me" / "像我一样思考" and similar phrases
- Captures thinking patterns, decision framework, communication style, engineering philosophy

### 3. Analysis JSON (`vibe-portrait-analysis-YYYY-MM-DD.json`)
- Portable data file for cross-machine syncing
- Contains full PORTRAIT_DATA plus machine hostname and export metadata
- Synced to portrait repo (if configured) or kept local

### 4. Portrait Repo (optional, private GitHub repo)
- Created via `gh repo create my-vibe-portrait --private`
- Stores persona skill, analysis JSONs from all machines, and latest portrait
- Repo URL cached in `~/.vibe-portrait-repo` for future syncs
- Template provided in `repo-template/` within this skill

## Anti-patterns

- **Do not fabricate data.** If you can't determine a signal, use a neutral score (50) and note the uncertainty.
- **Do not read files beyond conversation history.** Do not read source code, private documents, or SSH keys.
- **Do not make the portrait mean-spirited.** Even low ratings should be motivating with clear growth paths.
- **Do not skip the template.** Always read and use `templates/portrait.html` as the base.
- **Do not read all history lines.** Use the sampling strategy to stay within context limits.
- **Do not include raw API keys, tokens, or passwords in quotes.** Replace with `[REDACTED]`.

## Quick reference

| Reference file | Purpose |
|----------------|---------|
| `references/analysis-framework.md` | Detailed signal extraction guide for 6 dimensions |
| `references/mbti-mapping.md` | MBTI axis mapping rules and scoring |
| `references/famous-matching.md` | Famous person database (~30 figures) and matching algorithm |
| `references/rating-rubric.md` | Six-tier developer rating criteria (materialist methodology) |
| `references/persona-skill-template.md` | Persona SKILL.md generation template and rules |
| `templates/portrait.html` | HTML template with PORTRAIT_DATA placeholder |
| `repo-template/` | Template for user's private portrait repo |

## Persona skill management

### Installing a community persona from GitHub

When the user says "install persona from {URL}" or provides a GitHub link to someone else's portrait repo, follow this process:

1. **Validate the URL.** It should be a GitHub repo URL (e.g., `https://github.com/someone/my-vibe-portrait`).

2. **Extract the persona skill.** The target repo follows the portrait repo template structure — the persona skill is at `me/SKILL.md`.

```bash
REPO_URL="<the provided GitHub URL>"
TEMP_DIR=$(mktemp -d)
git clone --depth 1 "$REPO_URL" "$TEMP_DIR" 2>/dev/null
```

3. **Determine the person-id.** Read the SKILL.md frontmatter `name` field to extract the persona identifier. If it says `persona-jane-doe`, the person-id is `jane-doe`. Alternatively, infer from the repo owner's GitHub username as a fallback.

4. **Install to the community personas directory:**

```bash
PERSON_ID="<extracted person-id>"
TARGET_DIR=~/.claude/skills/vibe-portrait-personas/$PERSON_ID
mkdir -p "$TARGET_DIR"
cp "$TEMP_DIR/me/SKILL.md" "$TARGET_DIR/SKILL.md"
rm -rf "$TEMP_DIR"
```

5. **Confirm to the user:**
   - Where the persona was installed
   - How to invoke it: `"think like {person-id}"` or `"像{person-id}一样思考"`
   - Remind them to review the SKILL.md content if they have security concerns

### Listing installed personas

When the user asks "what personas do I have" / "我安装了哪些人格" / "list personas":

```bash
ls ~/.claude/skills/vibe-portrait-personas/
```

List each directory with a brief summary (read the first few lines of each SKILL.md for the name and description).

### Removing a community persona

When the user asks to remove a persona:

```bash
rm -rf ~/.claude/skills/vibe-portrait-personas/{person-id}
```

### Directory layout

```
~/.claude/skills/vibe-portrait-personas/
├── me/                  # YOUR persona (auto-generated by VibePortrait)
│   └── SKILL.md
├── jane-doe/            # Installed from someone's portrait repo
│   └── SKILL.md
└── zhuge-liang/         # Installed from community
    └── SKILL.md
```

- `me/` is always YOUR own persona. It is regenerated each time you run VibePortrait.
- All other directories are community personas installed from external GitHub repos.
- This separation is intentional and must not be changed.

### Invocation

Any installed persona can be activated with:
- `"think like {person-id}"` / `"像{person-id}一样思考"`
- `"what would {person-id} do"` / `"{person-id}会怎么做"`
- `"channel {person-id}"` / `"用{person-id}的方式来做"`

For your own persona: `"think like me"` / `"像我一样思考"`
