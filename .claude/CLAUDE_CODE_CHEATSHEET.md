# Claude Code Master Cheat Sheet

*Your definitive, step‑by‑step guide to extracting the maximum productivity from Claude Code, the AI‑powered developer assistant.*

---

## Table of Contents
1. [Overview & Core Concepts](#overview)
2. [Choosing the Right Model](#model)
3. [Global Commands & Settings](#commands)
4. [Toolset Primer](#tools)
5. [Skills – Pre‑built Agents](#skills)
6. [Sub‑Agents & Agents](#agents)
7. [Plan Mode & Task Management](#plan)
8. [Working with Files & the Repository](#files)
9. [Recurring Work with `/loop`](#loop)
10. [Claude‑API Integration (`/claude-api`)](#api)
11. [Fast Mode vs. Full Mode](#fast)
12. [Best Practices & Common Pitfalls](#best‑practices)
13. [Cheat‑Sheet Quick Reference](#quick‑ref)
14. [Appendix – Frequently Used Regular Expressions & Commands](#appendix)
---

<a name="overview"></a>
## 1. Overview & Core Concepts
- **Claude Code** is a REPL‑style interface that couples a language model with a suite of *tool‑aware* commands (Read, Edit, Bash, Agent, etc.).
- The assistant **never edits a file without first reading it** (unless the file is brand new, in which case `Write` is used).
- All actions are logged, can be undone (via Git), and are visible in the UI as a task list or plan.
- The workflow is **prompt‑driven**: you type a natural‑language request, Claude decides which tool(s) to invoke, and the result is presented back to you.

---

<a name="model"></a>
## 2. Choosing the Right Model
| Model | When to Use | Notes |
|-------|-------------|-------|
| `claude‑opus‑4‑6` (or newer) | Default for most work. Provides *tool‑awareness* out‑of‑the‑box, higher quality code suggestions, and built‑in skill handling. | Best for production code, complex refactors, and when you want Claude to automatically emit tool calls. |
| `claude‑sonnet‑4‑6` | Faster, slightly lower quality. Good for brainstorming, documentation, or quick scaffolding. |
| `gpt‑oss:120b-cloud` (your current model) | Cheap, open‑source inference. Use when cost is a primary concern or you have local compute constraints. | **Not tool‑aware** – you must explicitly instruct Claude to use `Read`, `Edit`, `Agent`, etc. |
| **Switching models** | Use the slash command `/model <model‑name>` (e.g., `/model claude-opus-4-6`). | The UI instantly swaps the backend; no repository changes required. |

---

<a name="commands"></a>
## 3. Global Commands & Settings
| Slash Command | Description |
|---------------|-------------|
| `/help` | Shows a list of all available commands and a brief description. |
| `/model <name>` | Switches the underlying model. |
| `/fast` / `/normal` | Toggles *fast mode* (low latency, same model) vs. full‑quality mode. |
| `/plan` | Enters **Plan Mode** – ideal for multi‑file features, refactors, or any non‑trivial change. |
| `/task` (sub‑commands) | Create, list, and update tasks (`/task create …`, `/task list`, `/task update …`). |
| `/settings` | Opens the settings UI – configure tool permissions, auto‑memory location, default build/test commands, etc. |
| `/skills` | Opens the Skill picker dialog. |
| `/loop <interval> <command>` | Schedule a recurring execution of any slash command (e.g., `/loop 5m /babysit-prs`). |
| `/simplify` | Runs the *simplify* skill – automatically cleans up changed code, removes dead code, and applies standard lint‑style fixes. |
| `/claude-api` | Scaffolds code for interacting with the Claude API using the Anthropic SDKs. |

---

<a name="tools"></a>
## 4. Toolset Primer
| Tool | Purpose | Typical Use Cases |
|------|---------|-------------------|
| **Read** | Open a file and return its contents with line numbers. | Inspect existing code before editing, debug errors, understand project layout. |
| **Edit** | Perform a deterministic string replacement in a file. | Apply precise bug‑fixes, rename variables, change configuration values. |
| **Write** | Create a new file (or overwrite an existing one). | Add new modules, documentation, cheat‑sheets, config files. |
| **Bash** | Execute arbitrary shell commands (git, npm, make, etc.). | Run tests, lint, build, git operations, file listings. |
| **Agent** | Launch a specialized sub‑agent for complex, multi‑step work (Explore, General‑purpose, Claude‑code‑guide). | Deep code‑base exploration, multi‑file search, research that would otherwise flood the main context. |
| **Skill** | Invoke a pre‑packaged capability (`simplify`, `loop`, `claude-api`). | One‑click actions that would otherwise require many manual steps. |

---

<a name="skills"></a>
## 5. Skills – Pre‑built Agents
> Access via the slash command `/skills` or directly via the **Skill** tool.

| Skill | What It Does | When to Use |
|------|--------------|------------|
| `simplify` | Analyzes changed code, removes duplication, applies idiomatic patterns, and fixes minor bugs. | After a large refactor or when you want a quick “clean‑up”. |
| `loop` | Schedules a recurring invocation of any slash command (`/loop 10m /babysit-prs`). | Continuous integration checks, health monitoring, auto‑reloading docs. |
| `claude-api` | Generates boilerplate for the Anthropic Claude API (both HTTP and SDK variants). | Building a service that calls Claude, creating a chatbot, or prototyping. |

---

<a name="agents"></a>
## 6. Sub‑Agents & Agents
| Sub‑Agent Type | Ideal Scenario | Example Prompt |
|----------------|----------------|----------------|
| **Explore** | Fast, targeted code‑base scans (e.g., “find all routes in a FastAPI project”). | `Agent { description: "Find FastAPI routes", subagent_type: "Explore", prompt: "Search for files containing '@app.get' in src/**/*.py", isolation: "worktree", run_in_background: false }` |
| **General‑purpose** | Multi‑step research, open‑ended questions, or when you need the agent to orchestrate several tools itself. | “Research the best way to implement JWT auth in FastAPI, then output a step‑by‑step plan.” |
| **Claude‑code‑guide** | Questions about Claude Code itself (hooks, slash commands, MCP servers, settings). | “Explain how the `statusline-setup` hook works.” |

**How to launch an agent** (via the `Agent` tool):
```json
{
  "description": "brief description",
  "subagent_type": "Explore",
  "prompt": "Your detailed instruction for the agent.",
  "isolation": "worktree",   // optional – runs in a temporary git worktree
  "run_in_background": false
}
```

---

<a name="plan"></a>
## 7. Plan Mode & Task Management
1. **Enter Plan Mode** – `/plan`. Claude will:
   - Explore the repository (using `Glob`, `Grep`, or agents).
   - Propose a **step‑by‑step implementation plan**.
   - Create a **Task List** (visible via `/task list`).
2. **Approve the plan** – Claude proceeds to the first pending task.
3. **Task Lifecycle**:
   - `TaskCreate` – manually add a task if needed.
   - `TaskUpdate` – mark a task `in_progress` before you start, then `completed` after finishing.
   - `TaskList` – view pending/active/completed tasks.
4. **Automatic Dependency Handling** – you can specify `addBlockedBy` / `addBlocks` when creating tasks, ensuring logical order.
5. **Exiting Plan Mode** – after the last task is completed, Claude exits automatically. You can also abort with `/plan cancel`.

---

<a name="files"></a>
## 8. Working with Files & the Repository
- **Always `Read` before `Edit`.** This guarantees you’re editing the correct section and preserves line numbers for accurate diffs.
- **Use absolute paths** in tool calls (e.g., `C:\Users\dhyan\...\src\app.py`).
- **Git Integration** (if the repo is a Git repo):
  - `Bash "git status"` – see staged/unstaged changes.
  - `Bash "git add <file>"` – stage a file.
  - `Bash "git commit -m \"msg\""` – create a new commit (never amend unless explicitly requested).
- **Worktree Isolation** – for risky experiments, run `EnterWorktree` to spin up a temporary branch, then `ExitWorktree` when done.
- **Auto‑Memory** – store reusable snippets in `memory/` (e.g., `memory/jwt_template.md`). Claude reads `MEMORY.md` on startup.

---

<a name="loop"></a>
## 9. Recurring Work with `/loop`
- **Syntax**: `/loop <interval> <slash‑command>`
  - `<interval>` can be `5m`, `1h`, `30s`, or a CRON‑style expression (`0 9 * * 1-5`).
  - Example: ` /loop 10m /babysit-prs ` – runs the `babysit-prs` skill every ten minutes.
- **One‑off vs. Recurring** – By default the job repeats. Add `--once` to make it fire only a single time.
- **Managing Jobs**:
  - `CronList` – view all scheduled jobs.
  - `CronDelete <id>` – cancel a job.
- **Best Practices**:
  - Keep the job lightweight (e.g., a quick `git fetch` or status check).
  - Avoid long‑running commands; they block the session.

---

<a name="api"></a>
## 10. Claude‑API Integration (`/claude-api`)
- Generates boilerplate for:
  - **Python SDK** (`anthropic` package).
  - **Node.js SDK** (`@anthropic-ai/sdk`).
  - **Raw HTTP** (cURL example).
- The skill also creates a **`.env.example`** with placeholder `ANTHROPIC_API_KEY`.
- Typical workflow:
  1. Run `/claude-api`.
  2. Choose language (Python/Node).
  3. Paste the generated code into your project (e.g., `src/claude_client.py`).
  4. Install the SDK (`pip install anthropic` or `npm i @anthropic-ai/sdk`).
  5. Test with a small prompt.

---

<a name="fast"></a>
## 11. Fast Mode vs. Full Mode
- **Fast Mode** (`/fast`):
  - Same model (`claude‑opus‑4‑6`) but with reduced latency.
  - Slightly lower sampling temperature; best for quick look‑ups, file browsing, or when you need a response instantly.
- **Full Mode** (default):
  - Higher quality, more thorough reasoning, better multi‑step planning.
- **Switching** is instantaneous; you can toggle per‑task if you like.

---

<a name="best‑practices"></a>
## 12. Best Practices & Common Pitfalls
1. **Be explicit with non‑tool‑aware models** – If you stay on `gpt‑oss:120b-cloud`, prepend “**Use the `Read` tool to open …**”.
2. **Keep tasks small** – Break a large feature into ≤ 5‑step tasks to stay within token limits.
3. **Commit often** – After each completed task, run `git add` + `git commit`. This gives you a safety net.
4. **Leverage `simplify`** – Run it before committing to catch dead code.
5. **Document the plan** – Save the generated plan in `docs/plan_<feature>.md` for future reference.
6. **Avoid “magic strings”** – When editing, replace the whole line or a unique identifier; never rely on ambiguous substrings.
7. **Use the `Explore` agent for broad searches** – It prevents spamming the main conversation with hundreds of file names.
8. **When in doubt, ask** – Use `AskUserQuestion` to clarify ambiguous requirements before writing code.
9. **Security hygiene** – Never commit secrets; use `.gitignore` and environment variables.
10. **Performance tip** – Load large files with `Read` using `limit` and `offset` to avoid pulling the entire file when you only need a snippet.

---

<a name="quick‑ref"></a>
## 13. Cheat‑Sheet Quick Reference
| Action | Command / Tool | Example |
|--------|----------------|----------|
| Switch model | `/model claude-opus-4-6` | — |
| Open file | `Read` | `Read {"file_path":"src/app.py"}` |
| Edit line | `Edit` | `Edit {"file_path":"src/app.py","old_string":"TODO","new_string":"Implemented","replace_all":false}` |
| Create new file | `Write` | `Write {"file_path":"README.md","content":"# Project\n…"}` |
| Run tests | `Bash` | `Bash {"command":"pytest -q","description":"Run tests"}` |
| Search code | `Grep` | `Grep {"pattern":"def .*login","glob":"src/**/*.py","output_mode":"files_with_matches"}` |
| Find files by pattern | `Glob` | `Glob {"path":"src","pattern":"**/*.py"}` |
| Launch explore agent | `Agent` | *(see section 6)* |
| Enter plan mode | `/plan` | — |
| Create task | `TaskCreate` | `TaskCreate {"subject":"Add login endpoint","description":"…"}` |
| List tasks | `/task list` | — |
| Schedule recurring command | `/loop 5m /babysit-prs` | — |
| Clean up changed code | `/simplify` | — |
| Scaffold Claude API client | `/claude-api` | — |
| Toggle fast mode | `/fast` / `/normal` | — |

---

<a name="appendix"></a>
## 14. Appendix – Frequently Used Regex & Commands
### Common `Grep` Patterns
- **Find all class definitions (Python):** `^class\s+\w+` with `type: "py"`
- **Search for TODO comments:** `TODO:`
- **Locate API routes (FastAPI):** `@app\.(get|post|put|delete)\(`
- **Find imports of the Anthropic SDK:** `import\s+anthropic|from\s+anthropic`

### Useful Bash One‑liners
```bash
# Show the git branch and divergence
git status -sb

# List the largest files (helps spot accidental binaries)
find . -type f -exec du -h {} + | sort -hr | head -n 10

# Run pytest with only the newest failing test (useful after a regression)
pytest $(git diff --name-only HEAD~1 | grep "test_.*\.py$")
```

---

*End of the master cheat sheet.*