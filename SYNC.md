# Sync & Contribution Guide

How this repository stays current and how contributors (human or AI) should work with it.

## The Two Data Flows

```
┌──────────────────────┐     ┌────────────────────────────┐
│  Website Repo        │────▶│  agent-skills /references/ │
│  (Faithmanex/        │     │  (BLOG_CONTENT_FORMATTING  │
│   formatly-v15-latest)│     │   + blog_planning_strategy)│
└──────────────────────┘     └────────────────────────────┘
                                        │
┌──────────────────────┐               │
│  Backend Repo        │               │ (no refs from here yet)
│  (Faithmanex/        │               │
│   formatly)          │               │
└──────────────────────┘               │
                                        ▼
                              ┌────────────────────────┐
                              │  agent-skills /skills/  │
                              │  (SOP procedures)       │
                              └────────────────────────┘
```

## When to Sync References

The `references/` directory mirrors select files from the live repos. Sync triggers:

| File | Source | When to Sync |
|------|--------|-------------|
| `BLOG_CONTENT_FORMATTING.md` | `website/` | After `git pull` detects changes |
| `blog_planning_strategy.md` | `website/` | After `git pull` detects changes |

**Mechanism:** The `daily-github-sync` cron automatically detects changes and copies files over. If it misses one, do it manually:

```bash
cp /data/workspace/website/BLOG_CONTENT_FORMATTING.md /data/workspace/skills-repo/references/
cp /data/workspace/website/blog_planning_strategy.md /data/workspace/skills-repo/references/
cd /data/workspace/skills-repo && git add -A && git commit -m "Sync references: {summary}" && git push
```

## When to Update Skills

| Signal | Action |
|--------|--------|
| Codebase adds a new feature (e.g., `BlogExampleAssetPanel` component) | Update the relevant skill with new capabilities |
| Business process changes | Update the relevant skill |
| New cron job added | Add new skill or update existing |
| Bug discovered in a procedure | Fix the skill immediately |

## Contribution Workflow

1. **Pull latest:** `git pull origin main` before starting
2. **Make changes** to the relevant `skills/*/SKILL.md` file(s)
3. **Update README.md** if the structure changes (new skills, removed skills)
4. **Commit with meaningful messages:**
   - `Update: {skill name} — {what changed}`
   - `Add: {new skill name} — {purpose}`
   - `Sync references: {file list} — {what upstream changed}`
5. **Push** — there's no review gate; trust and verify. If you're uncertain, commit to a branch instead.

## Private / Sensitive Data

- **NEVER** commit credentials, API keys, tokens, or secrets to this repo.
- Credential *locations* (e.g., "supabase key in `.env.format`") are fine.
- Business metrics and real user data stay in `MEMORY.md` and Supabase — not here.
- SOUL.md and IDENTITY.md are personality files — not procedures. They stay in the workspace.
