# TOOLS.md - Local Notes

## 🔄 Sync Rule

**Whenever SOUL.md, IDENTITY.md, TOOLS.md, AGENTS.md, or any skill file is updated, immediately commit and push to `github.com/Faithmanex/formatly-agent-skills`.**

The repo is the single source of truth for identity and procedures. Workspace files mirror it, not the other way around.

## Repositories (Cloned)

| Repo | Local Path |
|------|-----------|
| Website (Next.js) | `/data/workspace/website` |
| Backend (Python FastAPI) | `/data/workspace/backend` |

## Supabase

- Project URL: `https://chuxhdvazicuwdqwhtot.supabase.co`
- Project Ref: `chuxhdvazicuwdqwhtot`
- Region: us-west-1
- Tables: `profiles`, `documents`, `blog_posts`, `custom_styles`, `notifications`, `billing_addresses`, `waitlist`, `customer_surveys`

## Cron Jobs

| Name | Schedule | Purpose |
|------|----------|--------|
| `monitor-supabase-health` | Daily 8 AM WAT | Business health: errors, signups, known issues |
| `feedback-monitor` | Every 30 min | Poll customer_surveys → notify EchelNet of new feedback. Bugs → create implementation plan for approval.
| `daily-github-sync` | Daily 6:30 AM UTC (7:30 AM WAT) | git pull both repos, report changes |

## Feedback Monitor

- **Table**: `customer_surveys` (types: `satisfaction_survey`, `feature_request`)
- **State file**: `/data/workspace/memory/feedback-monitor-state.json`
- **Action on feedback**:
  - Structured notification with user, type, feedback, timestamp
  - Rating ≤ 2 or frustration → urgent escalation
  - Technical issues → implementation plan sent for approval

## Timezone

- Nigeria (WAT, UTC+1, no DST)
- Blog post target: early morning Nigeria time

## Brand Colors (Formatly)

- Dark Navy: `#002a57`
- Primary CTA: `#006ee5`
- Secondary/Accent borders: from DESIGN_SYSTEM.md

## Blog Writing

- **Skill**: `formatly-blog-writer` — read before writing any blog post
- **Supabase table**: `blog_posts` (78 posts published as of July 5, 2026)
- **Blog data map**: `lib/blog-data.ts` → `mapDbPostToBlogPost()`
- **Cover SVGs**: Store in Supabase Storage at `blog/covers/` (no SVGs exist yet for the 50 batch posts — need generation)
- **n8n pipeline**: `/data/workspace/website/n8n-workflows/formatly-blog-pipeline.json` (topic generation → DeepSeek write → SVG generation → Supabase publish)
- **Seed SQL**: `/data/workspace/scripts/50-blog-posts.sql` (50 posts already inserted via n8n pipeline)
- **Blog strategy**: `/data/workspace/website/blog_planning_strategy.md`
- **Scratch data (reference)**: `/data/workspace/website/scratch_blog_data.ts`

## Competitors to Monitor

- PERRLA ($79.95/yr)
- Zotero / Mendeley
- Scribbr ($50-$150+)
- Microsoft Word templates
- Generic AI (ChatGPT, Gemini)

## Key Differentiators

1. Full document formatting (not just citations/bibliography)
2. ~30 second processing
3. Tracked changes output (auditable)
4. Interactive AI feedback engine
5. Cross-style conversion
6. Privacy-first (no document logging, zero training)
