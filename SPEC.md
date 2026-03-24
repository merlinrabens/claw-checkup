# openclaw-doctor — OpenClaw Health & Self-Improvement Skill

A ClawHub skill that monitors, evaluates, and improves any OpenClaw installation.

## What It Does

### 1. Installation Health Check (`doctor check`)
Scans the running OpenClaw instance and reports a health score (0-100%).

**Checks:**
- Cron health: failed jobs, consecutive errors, overdue runs
- Config completeness: missing recommended settings, deprecated fields
- Skill health: installed vs ready count, broken skills
- Memory/workspace hygiene: oversized files, stale data
- Security basics: auth configured, no exposed secrets in workspace files

**Output:** Health score + actionable findings list.

### 2. Config Audit (`doctor audit`)
Deep-dives into openclaw.json configuration.

**Checks:**
- Missing best practices (e.g., no heartbeat configured, no backup channel)
- Deprecated or unused plugin entries
- Model config optimization (are you using expensive models for simple tasks?)
- Cron schedule conflicts (overlapping jobs)
- Skill config completeness (skills with missing required config)

**Output:** Scored audit report with specific fix suggestions.

### 3. Skill Quality Scoring (`doctor score-skills`)
Evaluates installed skills against quality criteria.

**Scoring dimensions (1-5):**
- Completeness: Does SKILL.md cover all use cases?
- Error handling: Does it handle failures gracefully?
- Clarity: Can the agent understand instructions without ambiguity?
- Efficiency: Does it minimize API calls and token usage?

**Output:** Per-skill scores + improvement suggestions.

### 4. Self-Improving Memory (`doctor learn`)
Tiered memory system that compounds learnings from corrections.

**Architecture:**
```
~/self-improving/
├── memory.md          # HOT: ≤100 lines, always loaded
├── corrections.md     # Last 50 corrections
├── index.md           # Topic index
├── heartbeat-state.md # Decay tracking
├── projects/          # Per-project learnings (WARM)
├── domains/           # Domain-specific (WARM)
└── archive/           # Decayed patterns (COLD)
```

**Rules:**
- Auto-detect corrections from user ("No, that's wrong...", "Stop doing X")
- Log to corrections.md immediately
- Promote to HOT after 3x same correction
- Auto-demote: unused 30 days → WARM, 90 days → COLD
- Never delete without user confirmation

### 5. Heartbeat Integration (`doctor heartbeat`)
Adds health checks to existing heartbeat cycle.

**Weekly decay check:** Scan HOT memory, demote stale entries.
**Cron monitoring:** Alert on consecutive failures.
**Skill drift detection:** Flag skills that changed upstream.

## File Structure

```
skills/openclaw-doctor/
├── SKILL.md              # Main skill instructions
├── _meta.json            # ClawHub metadata
├── README.md             # User-facing docs
├── scripts/
│   ├── health-check.py   # Installation health scanner
│   ├── config-audit.py   # Config deep-dive
│   └── skill-scorer.py   # Skill quality evaluator
├── templates/
│   ├── memory.md         # HOT memory template
│   ├── corrections.md    # Corrections log template
│   ├── heartbeat-state.md
│   └── agents-snippet.md # AGENTS.md integration snippet
└── references/
    ├── scoring-rubric.md # How skills are scored
    └── config-best-practices.md # Known good patterns
```

## Requirements

- `python3` (for scripts)
- `jq` (for JSON parsing)
- No API keys needed
- No network access needed (everything is local file inspection)

## ClawHub Metadata

```json
{
  "name": "openclaw-doctor",
  "version": "1.0.0",
  "description": "Monitor, evaluate, and improve your OpenClaw installation. Health checks, config audits, skill scoring, and self-improving memory.",
  "author": "merlinrabens",
  "license": "MIT",
  "tags": ["health", "monitoring", "self-improving", "config", "audit"],
  "openclaw": {
    "emoji": "🩺",
    "requires": {
      "bins": ["python3", "jq"]
    }
  }
}
```

## Differenzierung

- **vs self-improving (ivangdavila):** They only do memory/learning. We add health checks, config audit, skill scoring.
- **vs compound-learning (ours):** compound-learning is internal + GitHub-issue-focused. Doctor is generic, works for anyone, no GitHub dependency.

## Non-Goals

- No direct file edits to production config (report only, user decides)
- No GitHub integration (keep it universal)
- No network calls (pure local inspection)
- No business-specific logic
