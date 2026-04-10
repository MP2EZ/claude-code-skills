# Investigation Protocol

Reference for HYPOTHESIZE and INVESTIGATE phases. Loaded by SKILL.md.

## HYPOTHESIZE

Generate 3-7 hypotheses. Each gets:
- **Tag:** `H1-ShortName` — suspected component/concept
- **Claim:** one-line root cause statement
- **Confidence:** 20/40/60/80
- **Basis:** why this confidence

### Priors

| Factor | Effect |
|--------|--------|
| Error specificity | Points to subsystem → higher |
| Recency | `git log` shows recent changes in area → higher |
| Frequency | Common failure mode for system type → slight boost |
| Symptom match | Closely matches known pattern → higher |

## INVESTIGATE

### Loop

1. **Select:** any at 80, or top 3
2. **Predict:** "If H1-DbPool is correct, we should see [observable]"
3. **Cheapest first** — read code/config/logs before experiments
4. **Score:** confirms (+) → UP, disconfirms (-) → DOWN, neutral (=) → hold. Apply scoring rules from SKILL.md.
5. **Update ALL** hypothesis scores after each evidence step
6. **Display** updated table with arrows

### Evidence Types

| Type | Where to Look |
|------|--------------|
| Errors | Logs, stderr, stack traces, monitoring |
| Boundary | Actual inputs/outputs at the failure point (HTTP responses, return values, IPC messages) |
| Code changes | `git diff`, `git log`, recent commits |
| Config | Config files, env vars, feature flags |
| Runtime | Process status, resources, connections |
| Data flow | Trace inputs → outputs through call chain |
| Environment | OS, versions, deps, platform diffs |
| External | API responses, DB state, third-party status |

### Root Cause Tracing

When evidence points deep — trace backward through call chain. At each level: what called this? what value was passed? Keep going until origin of bad state. Fix at origin, not symptom.

## Convergence

- One at **80** + strong evidence → FIX
- All at **20** → REFRAME: new hypotheses incorporating eliminations
- 3 rounds no movement → "Not converging. Eliminated: [list]. What domain knowledge am I missing?"

## Edge Cases

**Intermittent:** Lower threshold to 60. Prioritize timing/concurrency/environment. Document when issue appears vs. doesn't.

**Multiple causes:** Two at 60-80 → present as compound cause. Ask: fix both, or investigate interaction?

**No access to evidence:** Note gap. Ask user to check and share. Adjust scores for uncertainty.

**Inconclusive (3 rounds):** Output eliminated + remaining with scores. Ask what domain knowledge is missing. Wait for input before new hypotheses.
