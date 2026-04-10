---
name: rca
description: Use when encountering any software issue — bugs, deploy failures, CI failures, operational incidents, configuration problems, integration issues — before proposing fixes. Use when something is broken, failing, or behaving unexpectedly in any repo.
---

## Phase Flow

```
INTAKE → TRIAGE → REPRODUCE → HYPOTHESIZE → INVESTIGATE → FIX → VERIFY → CLOSE
                      ^                          |
                      └──────────────────────────┘ (loop on failure)
```

## INTAKE

- `/rca`: Scan conversation for errors/complaints. Present: "Is this about [X], [Y], or something else?"
- `/rca <context>`: Parse provided text for errors, stack traces, symptoms.
- `/rca --resume`: Find latest `## RCA State` block. Found → resume. Not found → fresh start.

## TRIAGE

| Category | Confidence | Criteria | Action |
|----------|-----------|----------|--------|
| BLEEDING | >=80 | Active prod impact, data loss, users affected NOW | Mitigate first |
| DEGRADED | 40-60 | Partial impact, workarounds exist | User decides |
| STABLE | <=20 | No active impact, dev/test failure | Proceed to REPRODUCE |

Mitigation stops the bleeding. Investigation continues after stabilization.

## REPRODUCE

Default-on, skippable. **Code reading is not reproduction.** Reproduction means observing the actual error output — a stack trace, an HTTP response body, a console error.

1. Reproducible: document steps, proceed
2. Intermittent: note conditions, proceed with caution flag
3. Cannot reproduce: ask user for more context

**Method escalation** — if the first method fails, try the next:
1. Logs (server logs, stderr, monitoring)
2. Direct observation at the failure boundary (network responses, CLI output, return values)
3. Local reproduction with real dependencies (not mocks)

**Skip when:** user says "skip repro", self-evident from logs/errors, prod incident where repro is dangerous.

## Scoring Model

Buckets: **20, 40, 60, 80 only.** No decimals. Hypotheses are independent — do not sum to 100.

| 80 | Strong evidence. Ready to act. | 60 | Moderate. Worth investigating. |
|---|---|---|---|
| **40** | **Weak/untested. Unconfirmed.** | **20** | **Evidence against. Low priority.** |

**Updates:** Confirming → UP one. Strong confirming (direct observation) → UP two. Disconfirming → DOWN one. Neutral → no change.

**Arrows:** `^` (up), `v` (down), `->` (no change)

**Display format:**
```
-- RCA Hypotheses ----------------------------------------
  H1-DbPool       ########.. 80 ^  <- [evidence summary]
  H2-AuthMW       ##........ 20 v  <- [evidence summary]
  H3-RaceCondition ####...... 40 -> <- [evidence summary]
----------------------------------------------------------
```

**Shorthand rule:** After initial list, refer by tag only. "Investigating H1-DbPool..." not "Investigating hypothesis 1..."

## Phase References

**HYPOTHESIZE + INVESTIGATE phases:** See investigation.md in this directory.
**FIX + VERIFY phases:** See fix-and-verify.md in this directory.

## CLOSE

Default: commit only. Push on request.

**Commit message format:**
```
fix: [concise description]

Root cause: [H-tag] — [explanation]
Evidence: [key confirming evidence]
```

**Summary:** root cause (tag + score), fix, evidence chain (numbered, H-tag +/-), eliminated hypotheses.

**Watch deploy:** On request, monitor deploy output. Loop to INVESTIGATE on failure.

## Resume

Output at end of each phase:
```
## RCA State [timestamp]
Phase: [current] (round N/3)
Triage: [BLEEDING|DEGRADED|STABLE]
Hypotheses: H1-Tag:score-arrow | H2-Tag:score-arrow
Evidence: [item] (H-tag +/-) ...
Next: [specific action]
```

`/rca --resume` scans for latest `## RCA State`. Found → continue. Not found → fresh start.

## Output Style

**Show:** triage result, hypothesis list, each confidence update with arrows, proposed fix, verification results.

**Don't show:** every file read/grep, internal reasoning about hypothesis selection, routine tool outputs that don't change scores.
