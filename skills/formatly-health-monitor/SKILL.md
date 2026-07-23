---
name: "formatly-health-monitor"
description: "Daily business health checks for Formatly — signups, errors, document activity, and issue tracking."
---

# Formatly Health Monitor Skill

> Standard Operating Procedure for monitoring FormatlyApp.com's business health daily.
> Runs every day at 8:00 AM WAT (UTC+1).

## Trigger

Daily cron: `monitor-supabase-health` — fires automatically at 8:00 AM WAT.

## State Tracking

Health state and known issues are tracked in `/data/workspace/memory/known-issues.json`:

```json
{
  "lastCheck": "<ISO-8601>",
  "lastCheckCounts": {
    "totalProfiles": 138,
    "newProfilesSinceLastCheck": 0,
    "unreadErrorNotifications": 6,
    "recentFormattingFailures": 0,
    "failedDocumentsTotal": 86,
    "formattedDocumentsTotal": 278,
    "totalDocuments": 374
  },
  "issues": [
    {
      "id": "signup-drought",
      "type": "watch",
      "title": "No new signups in 5 days",
      "firstSeen": "2026-07-23",
      "lastSeen": "2026-07-23",
      "status": "watching",
      "details": "Zero new profiles since July 18. Only 3 signups in last 7 days per PostHog. Low conversion signals: 2 plan_selected, 1 upgrade_clicked. 3 rageclicks detected."
    }
  ]
}
```

Issue states: `watching` (monitoring, no action yet), `active` (needs attention), `resolved` (done).

## Procedures

### 1. Query Supabase for Health Metrics

Check three key areas:

**Growth:**
```sql
SELECT COUNT(*) FROM profiles WHERE created_at > NOW() - INTERVAL '24 hours';
```
Track trend over last 7 days. If zero for 3+ days, flag as a `watching` issue.

**Errors:**
```sql
SELECT COUNT(*) FROM notifications WHERE type = 'error' AND read = false;
```
If unread errors are high (>5), check `notifications` for specifics. Investigate backend logs via Supabase if recurring.

**Document Activity:**
```sql
SELECT
  COUNT(*) AS total_documents,
  COUNT(*) FILTER (WHERE status = 'completed') AS formatted,
  COUNT(*) FILTER (WHERE status = 'failed') AS failed
FROM documents
WHERE created_at > NOW() - INTERVAL '24 hours';
```
High failure rate (>20%) needs investigation.

### 2. Check PostHog Analytics

Query the PostHog project for key metrics:
- Signups (daily, weekly, monthly)
- Conversion events: `plan_selected`, `upgrade_clicked`
- Rageclicks / user frustration signals
- Active users (DAU/WAU)

### 3. Check Known Issues

Read `/data/workspace/memory/known-issues.json`. Update `lastSeen` for any active issues.

### 4. Decision Framework

| Scenario | Action |
|----------|--------|
| No signups 3+ consecutive days | Add `watching` issue. Prepare a traffic/conversion analysis |
| Error spike >5 unread | Investigate backend. Create implementation plan for EchelNet if fix needed |
| Failure rate >20% | Investigate failed documents. Escalate if pattern emerges |
| Everything nominal | Update state. No notification needed |
| Signup drought >7 days | Escalate to EchelNet with proposal (marketing push, blog topics, pricing review) |

### 5. Notify EchelNet

Only notify for:
- **New issue created** (state change → `watching` or `active`)
- **Escalation needed** (signup drought >7 days, recurring errors, security concerns)
- **Recovery** (issue resolved, back to normal)

Do NOT notify for routine "all clear" results. Format:
```
📊 Formatly Health — {Date}
• Signups (24h): {count} ({trend})
• Errors unread: {count}
• Documents: {formatted} formatted, {failed} failed
• Issues: {issue count} ({status})

{Notable change or escalation if any}
```

## Prerequisites

- Supabase service_role key in `/data/workspace/.env.format`
- PostHog personal API key (ask EchelNet if needed)
- `memory/` directory exists

---

## References

- `MEMORY.md` §Active Cron Jobs for schedule
- `/data/workspace/.env.format` for Supabase credentials
