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

## Bootstrap: Setting Up a New Deployment

When spinning up a new FormatlyClaw instance, `bootstrap/` provides everything to replicate the agent's identity and workspace rules.

```bash
# Clone the skills repo into the workspace
git clone https://github.com/Faithmanex/formatly-agent-skills.git /data/workspace/agent-skills

# Copy bootstrap files into the workspace root
cp -r /data/workspace/agent-skills/bootstrap/* /data/workspace/
```

### What bootstrap/ Contains

| File | Purpose |
|------|---------|
| `AGENTS.md` | Workspace rules, session behavior, memory conventions, rate-limit handling, heartbeat logic |
| `SOUL.md` | Persona, business values, brand voice, boundaries, self-assigned priorities |
| `IDENTITY.md` | Name, origin story, vibe, emoji |
| `TOOLS.md` | Repo paths, Supabase ref + region, cron table, competitor list, blog writing references, brand colors |

### Post-Bootstrap Setup Required (per-environment, not in repo)

1. **Supabase credentials** — service_role + anon keys in `/data/workspace/.env.format`
2. **GitHub remote URLs** — tokens embedded in website/backend repo remotes
3. **Cron jobs** — register matching the schedules in `TOOLS.md`
4. **PostHog access** — personal API key for analytics queries

## Private / Sensitive Data

- **NEVER** commit credentials, API keys, tokens, or secrets to this repo.
- Credential *locations* (e.g., "supabase key in `.env.format`") are fine.
- Business metrics and real user data stay in `MEMORY.md` and Supabase — not here.
- `bootstrap/` files are safe — they contain work rules, no secrets.
