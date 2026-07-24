# MEMORY.md — FormatlyClaw's Long-Term Memory

Last updated: 2026-07-23

## 🏢 Business Overview

FormatlyApp.com — AI-powered academic formatting engine (APA, MLA, Chicago, Harvard, Turabian). Operated by EchelNet. I manage daily operations: content, monitoring, support triage, competitive intel.

## 📡 Infrastructure

- **Website**: Next.js at `/data/workspace/website` (git: Faithmanex/formatly-v15-latest)
- **Backend**: Python FastAPI at `/data/workspace/backend` (git: Faithmanex/formatly)
- **Supabase**: `chuxhdvazicuwdqwhtot` (us-west-1), service_role key in `.env.format`
- **Time Zone**: Nigeria WAT (UTC+1, no DST)

## ⚙️ Active Cron Jobs (as of 2026-07-05)

| Name | Schedule | Purpose |
|------|----------|---------|
| `monitor-supabase-health` | Daily 8 AM WAT | Business health checks — errors, signups, known issues |
| `feedback-monitor` | Every 30 min | Poll `customer_surveys` for new feedback; bugs → implementation plan |
| `daily-github-sync` | Daily 7:30 AM WAT | `git pull` both repos, report changes |

## 🔐 Key Credentials Location

- Supabase service_role + anon keys: `/data/workspace/.env.format`
- GitHub tokens embedded in remote URLs (config safe)

## 📋 Known Issues (tracked)

See `/data/workspace/memory/known-issues.json` for active issue tracking.

Historical resolved issues:
- None yet.

## ✍️ Blog Stats

- 78 published posts as of July 5, 2026
- Most recent: "How to Format an APA Abstract Page (7th Edition)" (July 5)
- Users: 132 profiles (June 29: ~28 users, so ~100 new since launch)

## 🔄 Sync Rules

- Whenever SOUL.md, IDENTITY.md, TOOLS.md, AGENTS.md, or any skill file is updated, immediately commit and push to `github.com/Faithmanex/formatly-agent-skills`.
- The repo is the single source of truth for identity and procedures. Workspace files mirror it, not the other way around.
- After any push, verify the commit landed by checking `git log` in `/data/workspace/skills-repo/`.

## 🧠 Lessons Learned

1. Cron isolated sessions need `fallbacks` for model failures — DeepSeek rate limits hit hard
2. Gateway crashes when idle long enough — simple `openclaw gateway &` revives it
3. Supabase `customer_surveys` is the feedback table (types: satisfaction_survey, feature_request)
4. Rate-limit handling: auto-set +5min cron retry timer, don't stack timers
5. Feedback monitor state tracked in `/data/workspace/memory/feedback-monitor-state.json`
6. Formatly backend defaults to Gemini, not DeepSeek — formatting engine may still work even when blog cron fails
7. **Never send emails without explicit green light.** "Let me see" means show me the draft, not send it. Always get a clear "send it" or "go ahead" before dispatching any external communication. Draft-only until approved.
8. **Never offer compensation without authorization.** Don't offer "free formatting", "free months", discounts, or any compensation unless EchelNet has explicitly told you what to offer. I don't know the pricing model or what's appropriate to offer — ask first.
9. **Privacy first in external comms.** Never reference specific document details (title, format type, word count) in user-facing messages. Say "when you uploaded a document" or "when you tried Formatly" — not "your APA thesis." We don't advertise that we inspect content.
10. **Blog posts — read the canonical format guide BEFORE writing or publishing anything.** The Formatly repo's `BLOG_CONTENT_FORMATTING.md` (synced into `/data/workspace/website/BLOG_CONTENT_FORMATTING.md`) is the single source of truth. Read it on every blog-writing turn (cron, request, or proactive). Key rules: <h2> is the first element (no <h1>); no inline `style=""` except callout boxes; links use `class="text-[#006ee5] font-medium underline hover:text-[#0056b8]"`; smart-punctuation HTML entities only (`&ldquo;` `&rdquo;` `&lsquo;` `&rsquo;` `&mdash;` `&ndash;` `&hellip;` `&deg;` `&Prime;` `&`); no `<script>` / `<style>` / `<hr>` / empty tags / base64 images. **Do NOT author** `<script type="application/ld+json">` or `<!-- SEO METADATA -->` blocks inside `content` — the slimmed `app/blog/[slug]/page.tsx` builds JSON-LD and metadata from the post's other columns (`title`, `excerpt`, `slug`, `category`, `author`, `cover_image`, `read_time`, date fields) and injects them into `<head>` at render time. The legacy extract-by-parsing pattern is only a backward-compat safety net for pre-rewrite posts. Treat local `formatly-blog-writer/SKILL.md` as a workflow reference only (tone, templates, SVG cover generation, Supabase publishing mechanics); on HTML it defers to the repo guide. If they conflict, the repo guide wins.

11. **Always create an implementation plan before doing a coding/multi-step task.** Don't run straight into the work. EchelNet wants me to write up a plan first. Get signoff before executing. This avoids wasted effort and over-engineering (see: annotated bibliography download asset fiasco, 2026-07-24).

**Implementation plan format (Claude-style):**

```
## Goal
One-line summary of what we're solving.

## Approach
Strategy and why this way vs alternatives.

## Files to Touch
- `path/to/file.ts` — what changes (create, edit, delete)
- `path/to/other.ts` — what changes

## Step-by-Step Plan
1. Step one
2. Step two
3. Step three

## Rollback Strategy
How to undo each step if something goes wrong (git revert, DB migration revert, etc.)

## Risks & Edge Cases
What could break, what to watch out for.
```

**When to write one:** Any task that touches code, infrastructure, content changes beyond a single file tweak, or anything that would be annoying to undo. Minor one-liners don't need it.
