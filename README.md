# Claude Code Skills

Custom skills for [Claude Code](https://docs.anthropic.com/en/docs/claude-code).

## Skills

### `/rca` — Root Cause Analysis

A structured debugging workflow that guides Claude through systematic issue investigation instead of jumping to fixes.

**Flow:** `INTAKE → TRIAGE → REPRODUCE → HYPOTHESIZE → INVESTIGATE → FIX → VERIFY → CLOSE`

- Bayesian-style hypothesis scoring (20/40/60/80) with evidence-based updates
- Severity triage (BLEEDING / DEGRADED / STABLE) to prioritize mitigation vs. investigation
- Loop-back protection — if a fix fails, it returns to investigation instead of thrashing
- TDD-aligned fix phase: write failing test, verify red, fix, verify green
- Resumable state via `/rca --resume`

## Installation

Copy any skill directory into your Claude Code skills folder:

```bash
# Clone the repo
git clone https://github.com/MP2EZ/claude-code-skills.git

# Copy a skill (e.g., rca)
cp -r claude-code-skills/rca ~/.claude/skills/rca
```

Then use `/rca` in any Claude Code conversation.

## License

MIT
