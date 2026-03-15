# Professional Guide to Using Claude Code

*An in‑depth manual for developers, team leads, and DevOps engineers who want to get the most out of Claude Code in a production environment.*

---

## Table of Contents
1. [Why Professionals Choose Claude Code](#why)
2. [Setting Up a Professional Workspace](#setup)
   - Repository layout
   - Model selection & configuration
   - CI/CD integration
3. [Core Workflow](#workflow)
   - Feature development
   - Bug fixing
   - Refactoring large codebases
4. [Advanced Toolchain](#advanced)
   - Skills (built‑in agents)
   - Sub‑agents & custom agents
   - Plugins & external integrations
5. [Task Management & Planning](#tasks)
   - Using Plan Mode
   - Task lists, dependencies, and blockers
6. [Collaboration Patterns](#collab)
   - Pair‑programming with Claude Code
   - Code review automation
   - Knowledge sharing via Memory
7. [Production‑Ready Practices](#prod)
   - Git hygiene, worktrees, and branching strategies
   - Security & secret handling
   - Performance monitoring of Claude Code itself
8. [Real‑World Example Projects](#examples)
   - Web API service (FastAPI + PostgreSQL)
   - Front‑end React application
   - Data‑science pipeline (Pandas + Airflow)
9. [FAQ & Troubleshooting](#faq)
10. [Reference Cheat‑Sheets](#cheatsheet)

---

<a name="why"></a>
## 1️⃣ Why Professionals Choose Claude Code
| Benefit | What it means for a team | Example |
|---------|--------------------------|---------|
| **Tool‑aware LLM** | The model can *directly* edit files, run tests, and query the repo without manual copy‑paste. | A senior engineer runs `Bash "pytest -q"` from within the chat and gets instant test results. |
| **Deterministic planning** | Plan Mode gives a concrete, version‑controlled roadmap before any line of code is written. | Before adding a new authentication flow, Claude produces a 5‑step plan that is saved in `docs/plan_auth.md`. |
| **Extensible skill set** | Pre‑packaged skills (`simplify`, `loop`, `claude‑api`) cover many day‑to‑day tasks. | `simplify` automatically removes dead imports after a large refactor. |
| **Collaborative AI** | Multiple developers can invoke Claude in the same terminal session, sharing the same task list and memory. | A feature team uses a shared `memory/` directory to store common snippets (e.g., JWT token creation). |
| **Zero‑setup scaffolding** | New projects can be bootstrapped with a single `/claude-api` or `/plan` command. | A startup generates a full FastAPI service skeleton in under two minutes. |

---

<a name="setup"></a>
## 2️⃣ Setting Up a Professional Workspace
### 2.1 Repository Layout (recommended)
```
my‑project/
├─ src/                # Application source code
├─ tests/              # Unit / integration tests
├─ docs/               # Architecture docs, plans, cheat‑sheets
├─ memory/             # Persistent AI‑memory files (shared)
├─ .claude/            # Claude‑specific config (hooks, settings)
│   └─ settings.json
├─ .gitignore
├─ requirements.txt    # Python deps (or package.json, go.mod…)
└─ CLAUDE.md          # High‑level commands & project summary
```
### 2.2 Model Selection & Configuration
1. **Default for production** – `claude‑opus‑4‑6` (or newer) for tool‑awareness and best quality.
2. **Set via slash command**: ` /model claude‑opus‑4‑6 `.
3. **Persist the choice** – add to `.claude/settings.json`:
```json
{
  "defaultModel": "claude-opus-4-6",
  "toolPermissions": {
    "read": true,
    "edit": true,
    "bash": true,
    "agent": true,
    "skill": true
  }
}
```
### 2.3 CI/CD Integration (GitHub Actions example)
Create `.github/workflows/claude.yml`:
```yaml
name: Claude‑Code CI
on: [push, pull_request]
jobs:
  ai‑assist:
    runs-on: ubuntu‑latest
    steps:
      - uses: actions/checkout@v3
      - name: Install Claude‑Code CLI
        run: pip install claude-code  # or appropriate installer
      - name: Run Claude‑Code lint/format checks
        run: |
          claude-code \
            --model claude-opus-4-6 \
            --task lint \
            --repo ${{ github.workspace }}
```
This runs Claude in *read‑only* mode to ensure code style compliance before merging.

---

<a name="workflow"></a>
## 3️⃣ Core Workflow
### 3.1 Feature Development
1. **Create a branch**: `git checkout -b feature/<name>`.
2. **Enter Plan Mode**: ` /plan ` – Claude will ask for a feature description, then propose a plan.
3. **Approve the plan** – Claude creates a task list automatically.
4. **Work task‑by‑task**:
   - For each task, Claude will `Read` the relevant files, `Edit` them, and optionally run a `Bash` test.
   - Mark the task `in_progress` → `completed` using the Task UI or `TaskUpdate`.
5. **Run `simplify`** before committing to catch stray dead code.
6. **Commit**: `git add . && git commit -m "feat: <short description>"` – Claude can generate the commit message if you ask.
7. **Open PR** – either manually or via the UI; Claude can auto‑populate the PR description with the plan summary.

### 3.2 Bug Fixing
| Step | Command / Tool | Example |
|------|----------------|---------|
| Locate the defect | `Grep` | `Grep {"pattern":"TODO fix","glob":"src/**/*.py"}` |
| Open the file | `Read` | `Read {"file_path":"src/utils.py"}` |
| Apply the fix | `Edit` | `Edit {"file_path":"src/utils.py","old_string":"# TODO fix","new_string":"return result","replace_all":false}` |
| Verify | `Bash "pytest tests/test_utils.py -q"` |
| Clean up | `/simplify` |

### 3.3 Large‑Scale Refactor (e.g., moving to async)
1. Start **Plan Mode** – let Claude draft a multi‑phase plan (analysis, migration, tests, rollback).
2. Use the **Explore agent** to locate all sync‑API calls (`Grep` for `requests.get` across the repo).
3. For each module, Claude creates a sub‑task: edit, run tests, document changes.
4. After each phase, run a **worktree** to keep the refactor isolated:
   ```bash
   /worktree create refactor‑async
   # do work …
   /worktree exit keep   # keep branch for later PR
   ```
5. When the whole plan finishes, run `simplify` and a full test suite.

---

<a name="advanced"></a>
## 4️⃣ Advanced Toolchain
### 4.1 Built‑in Skills (the “one‑click” power tools)
| Skill | What It Does | Example Usage |
|-------|--------------|---------------|
| `simplify` | Cleans up changed code, removes dead imports, applies idiomatic patterns. | Run after a massive rename: `/simplify` |
| `loop` | Schedules recurring execution of any slash command. | `/loop 5m /babysit-prs` (checks PR health every 5 min). |
| `claude‑api` | Generates SDK scaffolding for the Claude API (Python, Node, raw HTTP). | `/claude-api` → paste into `src/claude_client.py`. |
| `review‑pr` *(custom skill you can add)* | Auto‑reviews a PR, comments on style issues, suggests test coverage. | `Skill {"skill":"review-pr", "args":"123"}` |

### 4.2 Sub‑Agents & Custom Agents
| Agent Type | Typical Prompt | When to Use |
|------------|----------------|------------|
| **Explore** | `"Search for all FastAPI routes in src/**/*.py"` | Quick discovery of patterns across many files. |
| **General‑purpose** | `"Research best practices for JWT rotation in a distributed system, then output a step‑by‑step implementation plan."` | Complex research that requires multiple tool calls. |
| **Claude‑code‑guide** | `"Explain how to configure a status‑line hook in Claude Code."` | When you need meta‑information about Claude Code itself. |
| **Custom Agent** (e.g., `docker‑builder`) | `"Given a Dockerfile, build the image, run unit tests inside the container, and report the result."` | Projects that need specialized tooling not covered by built‑in agents. |

**Creating a custom agent** (once, stored in the repo):
1. Add a file `agents/docker_builder.agent.yaml` with the appropriate front‑matter (model, description, tools).<br>
2. Invoke via `Agent` tool: `subagent_type: "docker_builder"`.

### 4.3 Plugins & External Integrations
| Plugin | Functionality | Integration Steps |
|--------|--------------|-------------------|
| **GitHub Pull‑Request Bot** | Auto‑opens a PR after a plan finishes, uses the plan summary as description. | Add a GitHub Action that runs `claude-code --action create-pr` after merge. |
| **Slack Notifier** | Sends a message to a Slack channel when a task list completes. | Create a webhook URL, store in `.env`, then use `Bash "curl -X POST ..."` within a skill. |
| **Jira Issue Creator** | Auto‑creates a Jira ticket from a task description. | Use the `jira` CLI inside a skill: `Skill {"skill":"jira-create", "args":"<task-id>"}`. |
| **VS Code Extension** | Enables inline AI suggestions directly in the editor. | Install the Claude Code VS Code extension and point it at the same repo path. |

---

<a name="tasks"></a>
## 5️⃣ Task Management & Planning
### 5.1 Plan Mode Walk‑through
1. **Trigger**: `/plan`.
2. Claude asks for a concise description, e.g., *"Add password‑reset flow to the auth service"*.
3. Claude returns a **plan** with:
   - High‑level phases (design, implementation, testing, docs).
   - A **task list** (auto‑generated, visible via `/task list`).
   - Dependencies (`addBlockedBy`).
4. **User approval** – you type *yes* or edit the plan.
5. Claude starts the first **pending** task, marking it `in_progress`.
6. When you finish a task, you mark it `completed`; Claude automatically unblocks the next one.

### 5.2 Task List Best Practices
- **One actionable verb** per task (e.g., *"Implement password‑reset endpoint"*).
- **Include acceptance criteria** in the description.
- **Link to docs**: `[Design Doc](docs/password_reset.md)`.
- **Use tags** inside the task description (`#backend #security`) – searchable with `TaskList` filters.

### 5.3 Exporting & Sharing
```bash
# Export the current task list to markdown for a sprint report
Bash "claude-code --export-tasks tasks_report.md"
```
The exported file can be attached to a sprint email or uploaded to Confluence.

---

<a name="collab"></a>
## 6️⃣ Collaboration Patterns
### 6.1 Pair‑Programming with Claude Code
1. Open a shared terminal (e.g., tmux session) where both the developer and Claude have access.
2. Use **Plan Mode** together – the human defines the high‑level goal, Claude proposes the plan.
3. Alternate: human writes a prompt, Claude *edits* the code, human reviews, then commits.
4. End the session with a **review** (`/simplify` + `git diff`) and a PR.

### 6.2 Automated Code Review
- **Skill**: `review‑pr` (custom) – runs after each push.
- Workflow:
  ```bash
  # In a CI job after tests pass
  claude-code --skill review-pr --args ${{ github.pull_request.number }}
  ```
- The skill leaves comments on the PR with suggestions (e.g., missing type hints, duplicated logic).

### 6.3 Knowledge Sharing via Memory
- Store reusable snippets in `memory/` (e.g., `memory/jwt_template.md`).
- At the start of a new project, run `Read` on the memory file to seed the AI.
- Keep a **knowledge‑base** file `MEMORY.md` with links to each snippet for quick navigation.

---

<a name="prod"></a>
## 7️⃣ Production‑Ready Practices
| Area | Recommendation |
|------|----------------|
| **Git hygiene** | Always create **new commits** (never amend) unless explicitly told. Use **feature branches** and **pull‑request reviews**. |
| **Worktree isolation** | For risky refactors, run `EnterWorktree` → work → `ExitWorktree keep` to keep the branch for a PR. |
| **Secret handling** | Keep API keys in `.env` or secret managers. Add `.env*` to `.gitignore`. Claude never reads files marked ignored. |
| **Pre‑commit hooks** | Add a hook that runs `claude-code --skill simplify` on staged files to enforce clean code before commit. |
| **Performance monitoring** | Log the latency of Claude calls (`/model` default) and set alerts if they exceed a threshold (e.g., 2 seconds). |
| **Backup memory** | Periodically copy `memory/` and `CLAUDE.md` to a secure backup bucket. |

---

<a name="examples"></a>
## 8️⃣ Real‑World Example Projects
### 8.1 FastAPI + PostgreSQL Service
```
my‑api/
├─ src/
│  ├─ api/
│  │   └─ v1/
│  │        ├─ auth.py   # ← will be edited by Claude
│  │        └─ users.py
│  └─ db/
│      └─ models.py
├─ tests/
│   └─ test_auth.py
├─ memory/
│   └─ jwt_template.md
└─ CLAUDE.md
```
**Typical workflow**:
1. `git checkout -b feature/password-reset`.
2. `/plan` → Claude produces a plan with 5 tasks (design DB schema, add endpoint, write email sender, tests, docs).
3. For *Task 2* (`Add endpoint`), Claude runs:
   - `Read` `src/api/v1/auth.py`.
   - `Edit` – inserts `@router.post("/reset")` route.
   - `Bash "pytest tests/test_auth.py::test_password_reset -q"`.
4. After all tasks, run `/simplify` → dead imports removed.
5. Commit, push, PR – Claude auto‑generates the PR description from the plan.

### 8.2 React Front‑End (TypeScript)
- **Skills used**: `simplify` (after component rename), `loop` (run `npm test --watch` every 2 min during dev), `claude‑api` (to fetch design tokens from a central service).
- **Memory snippets**: `memory/react_hooks.md` containing common `useEffect` patterns.

### 8.3 Data‑Science / Airflow Pipeline
- **Agent**: custom `airflow‑dag‑builder` (creates DAG files from a high‑level description).
- **Task flow**:
  1. Describe pipeline: *"Ingest CSV, clean, aggregate, store in BigQuery"*.
  2. Claude runs the custom agent → generates `dags/etl.py`.
  3. Run `Bash "airflow dags list"` to verify registration.
  4. Use `simplify` to tidy imports.

---

<a name="faq"></a>
## 9️⃣ FAQ & Troubleshooting
| Question | Answer |
|----------|--------|
| *Claude keeps suggesting the same edit over and over.* | Make sure the file was **read** before the edit. If the `old_string` isn’t unique, expand the context or use `replace_all:true`. |
| *My `Bash` command fails because the tool permission is disabled.* | Update `.claude/settings.json` → `"toolPermissions": {"bash": true}` and restart the session. |
| *I see a lot of “TODO” comments after a plan.* | Run the `simplify` skill; it removes dead TODOs and can even auto‑fill simple ones. |
| *How can I see what Claude actually did internally?* | Use `TaskList` to view each task, then `TaskGet <id>` for the full transcript. |
| *Can I run Claude offline?* | Yes, by installing the open‑source Ollama version and pointing Claude Code to the local model (`--model ollama/claude‑opus`). |

---

<a name="cheatsheet"></a>
## 🔟 Reference Cheat‑Sheets
- **Quick Commands** (see `CLAUDE_CHEATSHEET.md`).
- **Memory Snippets** – keep in `memory/` and reference with `Read`.
- **Common Regex for Grep** – locate imports, TODOs, API routes, etc.
- **Worktree Workflow** – isolate risky changes.

---

*This guide is intended for seasoned developers and teams that rely on Claude Code for daily development, code review, and automation. By following the patterns and examples above, you can turn Claude Code into a reliable, collaborative teammate that scales with your organization.*
