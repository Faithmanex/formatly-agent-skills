# Formatly Agent Skills

Agent skills and operational procedures for FormatlyApp.com. These files define how AI agents manage blog content, monitor the platform, triage feedback, sync codebases, and handle day-to-day operations.

## Repository

**`github.com/Faithmanex/formatly-agent-skills`**

Any agent working on Formatly operations should `git pull` this repo before each session to get the latest procedures.

## Structure

```
agent-skills/
├── README.md                                    ← this file
├── SYNC.md                                      ← sync & contribution workflow
├── .gitignore
├── bootstrap/                                   ← Agent identity & workspace defaults
│   ├── AGENTS.md                                ← Workspace rules, session startup, memory
│   ├── SOUL.md                                  ← Persona, voice, brand, priorities
│   ├── IDENTITY.md                              ← Name, origin, vibe
│   └── TOOLS.md                                 ← Repos, Supabase ref, cron jobs, competitors
├── skills/                                      ← OpenClaw skill definitions (SOPs)
│   ├── formatly-blog-writer/SKILL.md            ← Blog writing & publishing
│   ├── formatly-feedback-monitor/SKILL.md       ← Customer feedback triage
│   ├── formatly-health-monitor/SKILL.md         ← Daily business health checks
│   └── formatly-github-sync/SKILL.md            ← Daily repo sync & reference updates
└── references/                                  ← Canonical docs synced from live repos
    ├── BLOG_CONTENT_FORMATTING.md               ← Blog HTML rules (source: website repo)
    └── blog_planning_strategy.md                ← Content strategy (source: website repo)
```

## Quick Start for Agents

1. `git clone https://github.com/Faithmanex/formatly-agent-skills.git`
2. Copy `bootstrap/` files into your workspace root (`/data/workspace/`) to establish identity, rules, and tool references
3. Read the relevant skill (`skills/*/SKILL.md`) before starting a task
4. For blog writing: always read `references/BLOG_CONTENT_FORMATTING.md` first

## Conventions

- `skills/` contains OpenClaw skill definitions (`.md` with `name` and `description` frontmatter).
- `references/` contains canonical documents synced from the source repos (website, backend). These are the single source of truth.
- Skills reference each other via relative paths.
- When the `BLOG_CONTENT_FORMATTING.md` or `blog_planning_strategy.md` changes upstream, they should be copied here and the agent-skills repo pushed.

## Skills Overview

| Skill | File | Schedule | Purpose |
|-------|------|----------|---------|
| Blog Writer | `skills/formatly-blog-writer/SKILL.md` | On-demand / daily | Write, format, and publish blog posts to Supabase |
| Health Monitor | `skills/formatly-health-monitor/SKILL.md` | Daily 8 AM WAT | Check signups, errors, document activity, known issues |
| Feedback Monitor | `skills/formatly-feedback-monitor/SKILL.md` | Every 30 min | Poll customer_surveys, triage bugs and feature requests |
| GitHub Sync | `skills/formatly-github-sync/SKILL.md` | Daily 7:30 AM WAT | Pull repos, detect changes, sync references |

## Repository Bounderies — What Goes Here vs. What Stays in Memory

| In this repo | In MEMORY.md |
|-------------|--------------|
| Repeatable procedures (step-by-step) | Session-specific state (last checked timestamps) |
| Business rules and decision frameworks | Personal preferences and lessons learned |
| Canonical reference docs (synced from source) | Known issue tracking |
| Credential *locations* (not the secrets themselves) | Business context not needed for SOP execution |
