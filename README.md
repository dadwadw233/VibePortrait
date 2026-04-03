<div align="center">

# 🎭 VibePortrait

**Know yourself as a developer.**

One command. AI analyzes your coding history. Beautiful personality portrait.

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Claude Code](https://img.shields.io/badge/Claude_Code-skill-7C3AED?logo=anthropic&logoColor=white)](https://claude.ai/claude-code)
[![Codex](https://img.shields.io/badge/Codex-skill-10a37f?logo=openai&logoColor=white)](https://github.com/openai/codex)
[![GitHub stars](https://img.shields.io/github/stars/dadwadw233/VibePortrait?style=social)](https://github.com/dadwadw233/VibePortrait)

[English](#-what-is-vibeportrait) · [中文](#-vibeportrait-是什么)

<br>

<img src="https://img.shields.io/badge/MBTI-16_Types-8B5CF6?style=for-the-badge" alt="MBTI"> <img src="https://img.shields.io/badge/Famous_Match-30_Figures-3B82F6?style=for-the-badge" alt="Famous"> <img src="https://img.shields.io/badge/Rating-6_Tiers-F59E0B?style=for-the-badge" alt="Rating"> <img src="https://img.shields.io/badge/i18n-中文_English-10B981?style=for-the-badge" alt="i18n">

</div>

---

## 🌍 What is VibePortrait?

VibePortrait is a **skill for Claude Code and Codex** that reads your AI conversation history and generates:

1. **🖼️ An HTML personality portrait** — a stunning, self-contained page with visual charts, MBTI type, capability radar, developer rating, and famous person match
2. **🧠 A persona skill** — a SKILL.md that captures your thinking patterns, so any AI can "think like you"
3. **📸 An exportable image** — one-click PNG export for sharing on social media
4. **🔄 Multi-machine sync** — a private GitHub repo that keeps your portrait in sync across all your dev machines

### What's in the portrait?

| Section | Description |
|---------|-------------|
| 🧬 **MBTI Type** | Four-axis personality mapped to developer behavior, with per-type color theme |
| 📊 **Capability Radar** | Six dimensions: depth, breadth, communication, decisions, collaboration, creativity |
| 🏆 **Developer Rating** | `Legendary` → `Elite` → `Above Average` → `NPC` → `Below Average` → `Inactive` |
| 🎭 **Famous Match** | 30 figures across tech, science, philosophy & history — from Linus Torvalds to 诸葛亮 |
| 💬 **Communication** | Language distribution, directness score, keyword cloud |
| 🗺️ **Tech Domain Map** | Domain breakdown with tool/framework badges |
| ⏰ **Work Rhythm** | 24-hour activity heatmap and session patterns |
| 💡 **Signature Quotes** | Your most representative messages |

### Rating philosophy

> Ratings follow a **materialist methodology**: judged by observable output, not by claims or intent. No flattery, no benefit of the doubt. The evidence speaks.

---

## 🇨🇳 VibePortrait 是什么？

VibePortrait 是一个 **Claude Code / Codex 技能**，它读取你与 AI 的对话历史，生成：

1. **🖼️ HTML 人格画像页** — 精美的单文件网页，包含可视化图表、MBTI 类型、能力雷达、开发者评级、名人匹配
2. **🧠 人格 skill** — 一个 SKILL.md 文件，凝练你的思维方式，让任何 AI 都能"像你一样思考"
3. **📸 一键导出图片** — PNG 格式，方便在社交媒体分享
4. **🔄 多机器同步** — 通过 private GitHub 仓库在所有开发机之间同步画像数据

### 画像包含什么？

| 板块 | 说明 |
|------|------|
| 🧬 **MBTI 人格类型** | 四轴映射到开发者行为，16 种 MBTI 类型独立配色 |
| 📊 **能力雷达** | 六维度：技术深度、技术广度、沟通能力、决策速度、协作能力、创造力 |
| 🏆 **开发者评级** | `夯爆了` → `夯` → `人上人` → `NPC` → `拉` → `拉完了` |
| 🎭 **名人匹配** | 30 位人物，横跨科技、科学、哲学与历史——从 Linus Torvalds 到诸葛亮到居里夫人 |
| 💬 **沟通风格** | 语言分布、直接度评分、高频关键词 |
| 🗺️ **技术版图** | 领域分布 + 工具/框架徽章 |
| ⏰ **工作节奏** | 24 小时活跃度热力图与会话模式 |
| 💡 **经典语录** | 你最有代表性的消息 |

### 评分哲学

> 评分遵循**唯物主义方法论**：以可观察的实际产出为准，不以自我描述、意图或潜力为依据。不谄媚，不推测。证据说话。

---

## 🚀 Quick Start / 快速开始

### Claude Code — Plugin Marketplace

```
/plugin marketplace add dadwadw233/VibePortrait
/plugin install vibe-portrait@vibe-portrait
/vibe-portrait:vibe-portrait
```

### Claude Code — Manual Install / 手动安装

```bash
git clone https://github.com/dadwadw233/VibePortrait.git
cp -R VibePortrait/skills/vibe-portrait ~/.claude/skills/vibe-portrait
```

Then / 然后:

```
/vibe-portrait
```

### Codex

```
$skill-installer install https://github.com/dadwadw233/VibePortrait/tree/main/skills/vibe-portrait
```

Then / 然后:

```
Use $vibe-portrait to generate my developer personality portrait.
```

---

## ⚡ How It Works / 工作流程

```
┌─────────────────────────────────────────────────────────────────┐
│  Step 0   Choose analysis mode                                  │
│           ⚡ Quick (~200 msgs, low cost)                        │
│           🔍 Full (all msgs, max accuracy)                      │
├─────────────────────────────────────────────────────────────────┤
│  Step 1   Read ~/.claude/history.jsonl                          │
│           + optional: import from other machines                │
├─────────────────────────────────────────────────────────────────┤
│  Step 2   Analyze across 6 dimensions                           │
├─────────────────────────────────────────────────────────────────┤
│  Step 3   Compute MBTI type + developer rating + famous match   │
├─────────────────────────────────────────────────────────────────┤
│  Step 4   Fill HTML template → vibe-portrait-YYYY-MM-DD.html    │
├─────────────────────────────────────────────────────────────────┤
│  Step 5   Generate persona skill → ~/.claude/skills/.../me/     │
├─────────────────────────────────────────────────────────────────┤
│  Step 6   Export analysis JSON + auto-screenshot (PNG)          │
├─────────────────────────────────────────────────────────────────┤
│  Step 7   Sync to private GitHub repo (optional)                │
└─────────────────────────────────────────────────────────────────┘
```

---

## 🎨 MBTI Color Themes / MBTI 配色主题

Each MBTI type gets its own color palette. / 每种 MBTI 类型拥有独立配色方案。

| Group / 组 | Types / 类型 | Colors / 配色 |
|------------|-------------|--------------|
| 🧠 Analysts / 分析师 | INTJ · INTP · ENTJ · ENTP | Teal + Slate / 青绿 + 灰蓝 |
| 💚 Diplomats / 外交官 | INFJ · INFP · ENFJ · ENFP | Emerald + Amber / 翠绿 + 琥珀 |
| 🛡️ Sentinels / 哨兵 | ISTJ · ISFJ · ESTJ · ESFJ | Cobalt + Steel / 钴蓝 + 钢灰 |
| 🔥 Explorers / 探险家 | ISTP · ISFP · ESTP · ESFP | Gold + Rose / 金橙 + 玫红 |

---

## 🏆 Rating Scale / 评级体系

| Tier | 中文 | English | Emoji | Description |
|------|------|---------|-------|-------------|
| S+ | 夯爆了 | Legendary | 👑 | Multi-domain mastery, architecture-level thinking, builds novel systems |
| S | 夯 | Elite | 💎 | Deep expertise in 2+ domains, complex problem solver |
| A | 人上人 | Above Average | ⭐ | Solid skills with clear growth trajectory |
| B | NPC | NPC | 🤖 | Standard developer — the statistical norm, not a failure |
| C | 拉 | Below Average | 😅 | Basic tasks are challenging, heavy AI dependency |
| D | 拉完了 | Inactive | 💀 | Minimal technical interaction |

---

## 🎭 Famous Matching Database / 名人匹配库

30 figures across 4 categories / 30 位人物，横跨 4 大类：

| Category / 类别 | Figures / 人物 |
|----------------|---------------|
| 💻 Modern Tech / 现代技术 | Linus Torvalds · John Carmack · Andrej Karpathy · Yann LeCun · DHH · Fabrice Bellard · George Hotz · Grace Hopper · Rich Hickey · Steve Wozniak · Guido van Rossum · Satoshi Nakamoto · Elon Musk · Alan Kay |
| 🔬 Scientists / 科学家 | Einstein · Feynman · Turing · von Neumann · Marie Curie · Tesla |
| 📜 Philosophers / 哲学家 | 诸葛亮 · 王阳明 · 墨子 · 老子 · 孙子 · Aristotle · Leonardo da Vinci · Karl Marx |
| 🛠️ Creators / 创造者 | 鲁班 · Steve Jobs · Claude Shannon · 华罗庚 |

---

## 🧠 Persona Skills / 人格技能

After analysis, VibePortrait generates a **persona skill** that captures your: / 分析完成后，VibePortrait 会生成一个**人格技能**，凝练你的：

- 🧩 Thinking patterns / 思维模式
- ⚖️ Decision framework / 决策框架
- 💬 Communication style / 沟通风格
- 🔧 Engineering philosophy / 工程哲学

### Use it / 使用方式

```
think like me                    # Activate your own persona / 激活你自己的人格
像我一样思考这个问题               # Same in Chinese / 中文版
think like linus-torvalds        # Use a community persona / 使用社区人格
像诸葛亮一样设计这个方案           # Think like Zhuge Liang / 像诸葛亮一样思考
```

### Directory layout / 目录结构

```
~/.claude/skills/vibe-portrait-personas/
├── me/                  # Your own persona / 你自己的人格
│   └── SKILL.md
├── linus-torvalds/      # Community persona / 社区人格
│   └── SKILL.md
└── zhuge-liang/         # Community persona / 社区人格
    └── SKILL.md
```

---

## 🔄 Multi-Machine Sync / 多机器同步

VibePortrait uses a **private GitHub repo** to sync across machines. / VibePortrait 通过 **private GitHub 仓库**在多台机器间同步。

```
Machine A                          GitHub (private)                    Machine B
─────────                          ────────────────                    ─────────
/vibe-portrait                     my-vibe-portrait/                   /vibe-portrait
  → analyze local history            ├── me/SKILL.md                     → pull existing data
  → "create new repo"                ├── analysis/                       → merge with local
  → push ──────────────────────►     │   ├── macbook.json                → push ─────────►
                                     │   └── linux-4090.json
                                     ├── portraits/latest.html
                                     └── README.md (auto-filled)
```

Requirements / 需要: `gh` CLI installed and authenticated / 已安装并登录 `gh`

---

## 🔒 Privacy / 隐私

- ✅ Only reads local conversation history / 仅读取本地对话历史
- ✅ All analysis happens locally in your AI session / 所有分析在本地 AI 会话中完成
- ✅ API keys, tokens, passwords auto-redacted / 敏感信息自动脱敏
- ✅ All output files are local — you decide what to share / 所有输出文件均在本地
- ✅ Portrait repo is private by default / 画像仓库默认 private

---

## 📋 Requirements / 环境要求

- Claude Code or Codex with conversation history / Claude Code 或 Codex，且有对话历史
- At least 20 messages for a meaningful portrait / 至少 20 条消息才能生成有意义的画像
- A modern browser to view the HTML / 现代浏览器查看 HTML
- `gh` CLI for multi-machine sync (optional) / `gh` CLI 用于多机器同步（可选）

---

## 🗺️ Roadmap / 路线图

- [x] HTML portrait with 10 visual sections / HTML 画像（10 个可视化板块）
- [x] MBTI-based color themes (16 types) / MBTI 个性化配色（16 种类型）
- [x] Bilingual support (zh/en) / 中英双语支持
- [x] Persona skill generation / 人格 skill 生成
- [x] Multi-machine sync via GitHub / 多机器 GitHub 同步
- [x] One-click image export / 一键导出图片
- [x] Materialist rating rubric / 唯物主义评分体系
- [ ] Community platform — share & download persona skills / 社区平台：分享与下载人格 skill

---

<div align="center">

**Every great developer evolves. The first step is seeing clearly where you stand.**

**每一次对话都是你的进化轨迹。看见自己，分享自己，成为更强的自己。**

</div>
