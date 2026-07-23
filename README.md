# Formatly Agent Skills

Agent skills and operational procedures for FormatlyApp.com. These files define how AI agents manage blog content, monitor the platform, and handle day-to-day operations.

## Structure

```
agent-skills/
├── README.md              ← this file
├── skills/                ← OpenClaw-compatible skill definitions
│   ├── formatly-blog-writer/
│   │   └── SKILL.md       ← Blog writing & publishing procedure
│   └── voice-transcription/
│       └── SKILL.md       ← Telegram voice transcription procedure
└── references/            ← Canonical docs synced from live repos
    ├── BLOG_CONTENT_FORMATTING.md   ← Blog HTML rules (source: website repo)
    └── blog_planning_strategy.md    ← Content strategy doc (source: website repo)
```

## Convention

- `skills/` contains OpenClaw skill definitions (SKILL.md format).
- `references/` contains canonical documents synced from the source repos. Read these first before any skill runs.
- Skills reference each other via relative paths from the workspace root (e.g., `/data/workspace/website/BLOG_CONTENT_FORMATTING.md`).
