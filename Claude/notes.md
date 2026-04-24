@ -0,0 +1,149 @@
# Claude Code — Quick Reference Guide

## 1. Modes (`Shift + Ctrl + M`)

| Mode | Shortcut | What it does | When to use |
|---|---|---|---|
| **Ask permissions** | `1` | Asks approval before every file edit and command | Learning / want to review every change |
| **Accept edits** | `2` | Edits files freely, still asks for bash commands | Normal daily coding |
| **Plan mode** | `3` | Discuss and plan only — cannot make any changes | Big task, want to align approach first |

### Recommended flow
```
Big new task → Plan mode (discuss first)
              → Accept edits (start coding)

Learning / unsure → Ask permissions (see every change)
```

---

## 2. Models (`Shift + Ctrl + I`)

| Model | Shortcut | Best for |
|---|---|---|
| **Opus 4.7** | `1` | Hard bugs, complex architecture, security review, with 200k tokens memory |
| **Opus 4.7 1M** | `2` | Entire codebase in context, 50+ files at once, , with 1M tokens memory |
| **Sonnet 4.6** | `3` | Daily coding — good balance of speed and quality ✅ |
| **Haiku 4.5** | `4` | Quick questions, simple edits, fast responses |

### Effort levels (`Shift + Ctrl + E`)

| Level | When to use |
|---|---|
| **Low** | Simple questions, quick lookups |
| **Medium** | Most daily tasks ✅ |
| **High** | Complex bugs, architecture decisions |

### Simple rule
- **90% of the time** → Sonnet 4.6 + Medium
- **Hard problem / Sonnet got it wrong** → Opus 4.7 + High
- **Entire codebase analysis** → Opus 4.7 1M + High
- **Quick question** → Haiku 4.5 + Low

---

## 3. Slash Commands

| Command | When to use |
|---|---|
| `/init` | **Run once per project** — auto-generates CLAUDE.md with codebase summary |
| `/review` | Before merging a PR — checks changes for issues |
| `/security-review` | Before deploying — scans for security vulnerabilities |
| `/simplify` | After finishing a feature — cleans up the code you just wrote |
| `/help` | See all available commands |

### Recommended daily workflow
```
New project
   └── /init                    ← run once, generates CLAUDE.md

Write feature with Claude
   └── Accept edits mode        ← faster workflow

Feature finished
   ├── /simplify                ← clean up code
   ├── /security-review         ← check for vulnerabilities
   └── /review                  ← final check before PR

Push via GitHub Desktop
```

---

## 4. Context Window & Sessions

| | Detail |
|---|---|
| **Context limit** | 200,000 tokens per session (Opus 4.7 1M = 1,000,000) |
| **When full** | System auto-compresses old messages |
| **Across sessions** | Context does NOT carry over — start fresh each session |

### What carries over between sessions

| | Persists? |
|---|---|
| `CLAUDE.md` | ✅ Auto-loaded every session |
| `MEMORY.md` | ✅ Auto-loaded every session |
| Code changes (saved files) | ✅ Always on disk |
| Git history | ✅ Always available |
| Conversation history | ❌ Gone when session closes |

### Handover to new session
Before closing a long session, ask:
> "Summarise what we did and what's left so I can continue in a new session"

Then in the new session paste the summary and say **"continue from here"**.

---

## 5. Agents

Claude automatically decides when to use agents. You don't need to trigger them manually.

| | |
|---|---|
| **Auto-used when** | Task needs 4+ file reads, or parts can run in parallel |
| **You can request** | "Use 3 agents in parallel: agent 1 do X, agent 2 do Y, agent 3 do Z" |
| **Each agent is isolated** | No memory of the conversation — Claude briefs them automatically |

---

## 6. Key Files

| File | Location | Purpose |
|---|---|---|
| `CLAUDE.md` | Project root | Common knowledge auto-loaded every session |
| `MEMORY.md` | `C:/Users/{user}/.claude/projects/{project}/memory/` | Personal preferences, feedback, auto-loaded |
| `ARCHITECTURE.md` | Project root | Deep reference — read on demand |

### CLAUDE.md tip
- One per folder, all loaded (parent + child folders)
- Put **project-wide rules** at root level
- Put **folder-specific rules** inside that folder

---

## 7. Quick Decision Chart

```
What do I need?
│
├── Simple question / quick edit
│     └── Haiku + Low + Accept edits
│
├── Normal feature / bug fix
│     └── Sonnet + Medium + Accept edits
│
├── Complex bug / architecture
│     └── Opus 4.7 + High + Plan mode first
│
├── Analyse entire codebase
│     └── Opus 4.7 1M + High
│
├── Before merging PR
│     └── /review + /security-review
│
└── New project setup
      └── /init
```
