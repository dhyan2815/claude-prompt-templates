# 🛠️ Claude Code: The Beginner's Guide to Skills

Welcome to the power-user feature of Claude Code! **Skills** (also known as Agent Skills) are specialized "playbooks" that teach Claude how to perform specific tasks perfectly every time.

---

## 1. What are Skills?
Think of a Skill as a **reusable set of instructions** stored in your repository. 
- **Built-in Skills:** Commands like `/simplify` or `/loop` that come with Claude Code.
- **Custom Skills:** Commands YOU create (like `/new-template` or `/audit-css`) that are specific to your project.

---

## 2. Where are Skills Stored?
Claude Code looks for skills in two places:

1.  **Project-Specific (Best for Teams):** 
    Located at `.claude/skills/<skill-name>/SKILL.md`. These are committed to Git so your whole team can use them.
2.  **Global (Best for You):** 
    Located at `~/.claude/skills/<skill-name>/SKILL.md` (on your machine). These work in *any* project you open.

---

## 3. How to Create Your First Custom Skill
A skill is just a folder containing a file named `SKILL.md`. Here is the step-by-step:

### Step 1: Create the Folder
If you want to create a skill called `code-reviewer`, run:
```bash
mkdir -p .claude/skills/code-reviewer
```

### Step 2: Create the `SKILL.md` File
Create a file at `.claude/skills/code-reviewer/SKILL.md` with two parts: **Frontmatter** (settings) and **Instructions**.

#### Example `SKILL.md`:
```markdown
---
name: code-reviewer
description: Performs a rigorous style and security check on HTML/JS files.
---
When I invoke this skill, please:
1. Check that all IDs in `index.html` follow the `t[NUMBER]` format.
2. Ensure every template card has a "Copy" button.
3. Verify that new CSS variables are added to both `:root` and `[data-theme="light"]`.
4. Check for hardcoded API keys or secrets.

Output your findings in a clear checklist format.
```

---

## 4. How to Use Skills

### Option A: Manual Trigger (Slash Commands)
Once you save your `SKILL.md`, Claude Code automatically creates a new command. Just type:
> `/code-reviewer`

### Option B: Automatic Discovery (The Magic Way)
Because you gave your skill a **description** in the frontmatter, Claude is "aware" of it. If you ask:
> "Hey Claude, can you check my recent changes for any issues?"

Claude will see that the `code-reviewer` skill matches your request and will **automatically load those instructions** to help you.

---

## 5. Advanced Skill Settings (Frontmatter)
You can control how the skill behaves using these options in the top `---` section:

- `disable-model-invocation: true` → Claude will **never** use this skill unless you manually type `/command`.
- `user-invocable: false` → The skill won't show up as a `/` command; only Claude can decide to use it.
- `context: fork` → Runs the skill in a separate "brain" (subagent) with a fresh 200k token window. Great for processing massive files.

---

## 6. Pro-Tips for This Project
For your **Claude Notes** app, try creating these skills:
- `/a11y` – To check accessibility of your prompt cards.
- `/new-card` – To generate the complex HTML for a new template card automatically.
- `/seo-check` – To verify Open Graph and Twitter meta tags.

---

## 7. Troubleshooting
- **Skill not showing up?** Restart your Claude Code session or type `/reload-skills`.
- **Claude ignoring instructions?** Make your instructions more specific and use numbered lists.
- **Too much output?** Tell the skill to "Be concise" or "Only report errors."

---
*Created on March 16, 2026. Happy Prompting!*
