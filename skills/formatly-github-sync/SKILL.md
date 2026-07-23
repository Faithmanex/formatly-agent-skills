---
name: "formatly-github-sync"
description: "Pull the latest from Formatly's website and backend repos, report changes."
---

# Formatly GitHub Sync Skill

> Standard Operating Procedure for keeping local clones of Formatly's repositories up to date.
> Runs daily at 7:30 AM WAT (UTC+1).

## Trigger

Daily cron: `daily-github-sync` — fires at 7:30 AM WAT.

## Repositories

| Repo | Local Path | Remote |
|------|-----------|--------|
| Website (Next.js) | `/data/workspace/website` | `github.com/Faithmanex/formatly-v15-latest` |
| Backend (Python FastAPI) | `/data/workspace/backend` | `github.com/Faithmanex/formatly` |

## Procedures

### 1. Pull Both Repos

```bash
cd /data/workspace/website && git pull origin main
cd /data/workspace/backend && git pull origin main
```

### 2. Detect Changes

For each repo, check what changed:

```bash
# After pull, check git log for new commits
git log --oneline HEAD@{1}..HEAD --no-decorate
```

| Change Type | Examples | Action |
|-------------|----------|--------|
| Backend code changes | Formatting engine, API routes | No action (Coderabbit reviews regressions) |
| Website UI changes | Components, pages, styling | No action |
| Blog content changes | `BLOG_CONTENT_FORMATTING.md`, blog posts code | Note for awareness |
| Config / build changes | `package.json`, config files, env changes | Flag if breaking |
| Example asset changes | `lib/blog-example-assets.ts`, `public/blog-assets/` | Note for awareness — may need skill sync |
| No changes | — | Stay quiet |

### 3. Sync to Agent Skills Repo

When `BLOG_CONTENT_FORMATTING.md` or `blog_planning_strategy.md` change in the website repo:

```bash
cp /data/workspace/website/BLOG_CONTENT_FORMATTING.md /data/workspace/skills-repo/references/
cp /data/workspace/website/blog_planning_strategy.md /data/workspace/skills-repo/references/
cd /data/workspace/skills-repo && git add -A && git commit -m "Sync references: {summary of changes}" && git push
```

This keeps the canonical docs in `formatly-agent-skills` in sync with the live website repo.

### 4. Notify EchelNet

Only notify if something meaningful changed:

```
🔁 GitHub Sync — {Date}

Website: {commit count} new commit(s) — {short summary of notable changes}
Backend: {commit count} new commit(s) — {short summary of notable changes}

{Any sync actions taken, e.g., "References synced to agent-skills repo"}
```

If nothing changed, do NOT notify.

## Scope Limitations

- **Do not review code for regressions** — Coderabbit handles that automatically via PRs.
- **Do not deploy.** Pull only — changes go live through EchelNet's deployment process.
- **Do not modify files** unless a skill or reference doc needs syncing.

## Prerequisites

- Git remotes configured (already set up — GitHub tokens embedded)
- Write access to `Faithmanex/formatly-agent-skills` for reference syncs
- Both repos cloned at the paths above

---

## References

- `TOOLS.md` §Repositories for path aliases
- `skill_workshop` for updating Formatly skills when the upstream codebase changes
