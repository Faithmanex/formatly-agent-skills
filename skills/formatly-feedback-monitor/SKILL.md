---
name: "formatly-feedback-monitor"
description: "Poll customer_surveys for new feedback and triage issues — bugs create implementation plans, feature requests get recorded."
---

# Formatly Feedback Monitor Skill

> Standard Operating Procedure for monitoring user-submitted feedback through Formatly's Supabase customer_surveys table.
> Runs every 30 minutes.

## Trigger

Cron: `feedback-monitor` — fires every 30 minutes.

## State Tracking

State file: `/data/workspace/memory/feedback-monitor-state.json`

```json
{
  "lastCheckedAt": "<ISO-8601>",
  "lastCheckedId": "<uuid-of-last-processed-row>",
  "notifiedIds": ["<uuid1>", "<uuid2>"]
}
```

## Procedures

### 1. Query New Feedback

```sql
SELECT id, created_at, user_id, survey_type, rating, feedback_text, metadata
FROM customer_surveys
WHERE created_at > '${state.lastCheckedAt}'
ORDER BY created_at ASC;
```

Two survey types:
- `satisfaction_survey` — user satisfaction rating (1–5) + optional text
- `feature_request` — feature ideas and suggestions

### 2. Triage Rules

| Signal | Priority | Action |
|--------|----------|--------|
| Rating ≤ 2 with frustration text | 🔴 Critical | Escalate immediately to EchelNet with full details |
| Rating ≤ 2 (no text) | 🟡 Medium | Note, add to weekly report |
| Feature request with clear use case | 🟢 Low | Record in topic bank or issue tracker |
| Feature request vague / one-liner | ⚪ Info | Acknowledge, archive |
| Rating 4–5 | ✅ All good | Mark notified, no action |
| Explicit bug report (technical issue) | 🔴 Critical | Create implementation plan for EchelNet's approval |

### 3. Notify EchelNet

**Critical (bug / low satisfaction):**
```
🔴 User Feedback — {Type}
• User: {user_id}
• Rating: {rating}/5
• Feedback: "{text}"
• Time: {created_at}

{Action taken: implementation plan created / escalation needed}
```

**Feature request:**
```
💡 Feature Request
• User: {user_id}
• Request: "{text}"
• Context: {metadata useful for prioritization}
```

**General (info only):**
Log to `/data/workspace/memory/feedback-log-{YYYY-MM-DD}.md` — do not notify.

### 4. Update State

After processing, write updated `lastCheckedAt` and `notifiedIds` to the state file.

### 5. Implementation Plans for Bugs

When a technical issue is identified from feedback:

1. Investigate the issue by checking Supabase error logs, backend logs, or document data
2. If root cause is identifiable, propose a fix plan:
   ```
   # Implementation Proposal: {Short Title}
   
   ## Issue
   {What user reported + what I found}
   
   ## Root Cause
   {Technical diagnosis}
   
   ## Proposed Fix
   {Steps to implement — file changes, config updates, etc.}
   
   ## Impact
   {What breaks if not fixed}
   
   ---
   *Requires your approval before implementation*
   ```
3. Send to EchelNet via Telegram — wait for green light before executing

## Decision Rules

- **Never assume auto-fix.** Always send proposal, wait for approval.
- **Never offer compensation** (free months, discounts) without authorization.
- **Never reference document content in feedback messages** — privacy first.

## Prerequisites

- Supabase service_role key in `/data/workspace/.env.format`
- State file at `/data/workspace/memory/feedback-monitor-state.json`
- Feedback log directory: `/data/workspace/memory/`

---

## References

- `MEMORY.md` §Lessons Learned — rules 3, 5, 7, 8, 9
- `/data/workspace/.env.format` for Supabase credentials
