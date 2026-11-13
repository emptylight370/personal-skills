# Personal Skills

[中文](README.md)
| **English**

A personal collection of Skills accumulated from my day-to-day work. Each Skill lives in its own folder, named after the Skill itself, and typically contains a `SKILL.md` (Skill definition and instructions) plus auxiliary directories such as `references/` and `scripts/`.

Design documents for all Skills are kept under [docs/](./docs) (file names follow the corresponding skill name) and are not distributed with the skills themselves.

## Getting a Skill

### npx skills (recommended)

Install with a single command using the [skills CLI](https://github.com/vercel-labs/skills) (requires Node.js ≥ 22.20). Supports Claude Code, Cursor, Codex, OpenCode, and 75+ other agents:

```bash
# Browse the skills available in this repo
npx skills add emptylight370/personal-skills --list

# Interactively pick skills and target agents (installs into the current project)
npx skills add emptylight370/personal-skills

# Install a specific skill globally (available across projects); use -a to pick an agent, e.g. -a claude-code
npx skills add emptylight370/personal-skills --skill siyuan-skill -g -y
```

When installing into a project with a single agent, the skill is copied into that agent's skill directory (e.g. `.claude/skills/`); with multiple agents, skills are stored canonically in `.agents/skills/` and symlinked from each selected agent directory (add `--copy` to copy into every directory instead). Ongoing maintenance:

```bash
npx skills update   # Update installed skills
npx skills remove   # Remove installed skills
```

### Sparse checkout

Fetch only the Skill folders you need via Git's `sparse-checkout` instead of cloning the whole repo. Using `siyuan-skill` as an example:

```bash
# 1. Clone the repo (--sparse enables cone-mode sparse checkout; --depth 1 fetches only the latest commit)
git clone --filter=blob:none --sparse --depth 1 git@github.com:emptylight370/personal-skills.git
cd personal-skills

# 2. Specify the Skill folders to check out (multiple allowed; file contents are downloaded on demand at this step)
git sparse-checkout set siyuan-skill

# 3. To add more skills: set is overwriting, so list existing folders together with the new ones
git sparse-checkout set siyuan-skill another-skill
# Or append with add (Git 2.37+) without affecting already-checked-out folders
git sparse-checkout add another-skill
```

To update, pull the latest changes inside the repo directory; the `sparse-checkout` configuration is preserved:

```bash
cd personal-skills
git pull
```

To adjust the checked-out Skill folders later, re-run `git sparse-checkout set <folder...>` (overwriting — list all folders you want to keep); to only append without affecting existing folders, use `git sparse-checkout add <folder>` (Git 2.37+).

### Copying into a project's skill directory

If your project already has a skill directory (usually `<project>/.agents/skills/`, or `<project>/.claude/skills/` for Claude Code) and you want to place a plain Skill folder there (no `.git`, no nested repo), clone part or all of this repo first, then copy the desired Skill folder over.

```bash
# 1. Clone the repo (partial: only sparse-checkout the skills you need; drop --sparse to clone everything)
git clone --filter=blob:none --sparse --depth 1 git@github.com:emptylight370/personal-skills.git /tmp/personal-skills

# 2. Specify the skills you need (skip when cloning the whole repo)
git -C /tmp/personal-skills sparse-checkout set siyuan-skill

# 3. Copy into the project's skill directory (for Claude Code use <project>/.claude/skills)
mkdir -p <project>/.agents/skills
cp -r /tmp/personal-skills/siyuan-skill <project>/.agents/skills/
```

The copied result is a plain content folder (without `.git`) that the project can use directly. On Windows, use `$env:TEMP\personal-skills` as the clone directory and replace `cp -r` with `Copy-Item -Recurse -Force`.

For later updates, repeat the steps above: run `git -C /tmp/personal-skills pull` to update the temporary repo, then copy over the project's skill directory again.

## Skill List

### [cnb-pipeline](./cnb-pipeline)

Write, modify, and review `.cnb.yml` pipeline configuration for the CNB cloud-native build platform: branch/event triggers, Pipeline/Stage/Job structure, triple-chain semantics (stages/failStages/endStages), built-in tasks (`type`), plugin tasks (`image` + `settings`), environment variables, caching, crontab schedules, self-hosted runner scheduling, and Monorepo orchestration patterns. A pure authoring skill with no dependency on CNB platform credentials or CLI; platform facts (fields/events/variables/plugin parameters) are always looked up online in the official docs (docs.cnb.cool) via the routing table in `references/doc-index.md`, with no locally maintained field snapshots that would go stale.

### [issue-triage](./issue-triage)

Triage and handle GitHub issues for a repository: fetch and digest the issue list, produce summaries with preliminary classification suggestions, present decision cards, let the user openly decide how to proceed (self-fix with the agent's suggestions, or direct the agent to implement), then execute (code changes / replies / labels / closing), with cross-session progress tracking. All externally visible write operations (comments, labels, milestones, closing, transferring, PRs) always require user review first; project-level configuration (label lists, commit strategy, wrap-up strategy, etc.) is injected through a context card (`project-context.md`) generated by agent research and reviewed by the user. The context card and progress files live in a data directory next to the skill installation (e.g. `.agents/issue-triage/`), so they stay out of git and are unaffected by `skills update`.

### [requirement-clarify](./requirement-clarify)

Clarify and confirm user requirements before implementation: the agent infers the user's actual intended requirement, restates it for confirmation, and surfaces ambiguities that need user confirmation (preferably via the built-in questioning tool), iterating over multiple rounds until the user explicitly confirms; each question is tracked in one of three states (resolved / explicitly deferred / agent-set default pending confirmation). After confirmation, remaining unresolved questions are asked once more before implementation begins, and the delivery approach (e.g. modular incremental or one-shot) is decided by explicit user requests or stored user preferences; during implementation, evidence contradicting confirmed requirements is reported back with evidence for reconfirmation; follow-up requirements consistent with existing logic are folded in directly with regression checks, while unclear or conflicting parts are reconfirmed.

### [siyuan-skill](./siyuan-skill)

Operate SiYuan Note via the command-line tool (`siyuan`), supporting management of notebooks, documents, blocks, SQL queries, search, import/export, assets, attributes, bookmarks, tags, history, refs, repo snapshots, sync, and more.
