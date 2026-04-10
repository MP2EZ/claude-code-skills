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

#### Usage

There are two ways to trigger the skill:

**1. Slash command**

Type `/rca` directly in your Claude Code prompt. You can optionally pass context or flags:

| Command | What it does |
|---------|-------------|
| `/rca` | Scans the current conversation for errors and asks what you want to investigate |
| `/rca <description>` | Starts an investigation with the context you provide (error messages, stack traces, symptoms) |
| `/rca --resume` | Picks up a previous investigation from where it left off |

**2. Natural language**

You don't need the slash command — just describe the problem and Claude will activate the skill automatically. Examples:

- *"My tests are passing locally but failing in CI"*
- *"Users are getting 502s on the /api/checkout endpoint"*
- *"The build broke after the last merge, can you figure out why?"*
- *"This function is returning null but I don't know why"*

The skill triggers whenever Claude detects something is broken, failing, or behaving unexpectedly. The slash command gives you more control (e.g., `--resume`), but plain English works for starting a fresh investigation.

#### What to expect

Once activated, Claude will:

1. **Triage** the severity — is this actively hurting production, or a stable dev issue?
2. **Reproduce** the error by observing actual output (not just reading code)
3. **Generate hypotheses** with confidence scores (20/40/60/80)
4. **Investigate** each hypothesis, updating scores as evidence confirms or eliminates them
5. **Fix** the root cause with a failing test first, then a minimal fix
6. **Verify** by running the full test suite — no "should work now" claims without proof
7. **Close** with a commit linking the root cause and evidence chain

At each phase, Claude shows a hypothesis scoreboard so you can see what's being considered and why:

```
-- RCA Hypotheses ----------------------------------------
  H1-DbPool       ########.. 80 ^  <- connection timeout in logs
  H2-AuthMW       ##........ 20 v  <- middleware unchanged since March
  H3-RaceCondition ####...... 40 -> <- concurrent requests untested
----------------------------------------------------------
```

## Installation

Copy any skill directory into your Claude Code skills folder:

```bash
# Clone the repo
git clone https://github.com/MP2EZ/claude-code-skills.git

# Copy the rca skill
cp -r claude-code-skills/rca ~/.claude/skills/rca
```

Then use `/rca` or describe a problem in any Claude Code conversation.

## License

MIT
