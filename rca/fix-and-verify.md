# Fix and Verify Protocol

Reference for FIX and VERIFY phases. Loaded by SKILL.md.

## FIX

1. **Write failing test** capturing root cause. No test infrastructure? Standalone reproduction script instead.
2. **Verify RED** — run test, confirm failure matches expected reason (root cause present), not typo or setup error.
3. **Minimal fix** — address root cause from INVESTIGATE. One change, not a refactor.
4. **Verify GREEN** — run test, confirm pass.
5. **Full suite** — confirm no regressions.

**No test infrastructure fallback:**
- Use standalone reproduction script
- Run before fix (fails) and after fix (passes)
- Note in CLOSE summary that automated coverage was not added

**Loop-back rule:**
If fix doesn't resolve or introduces regressions — DO NOT try another fix.
- Record failure as new evidence
- Update hypothesis scores (failed "root cause" drops)
- Return to INVESTIGATE with updated priors
- Prevents fix-thrashing

## VERIFY

Verification gate — three steps:

1. Run ALL relevant verification commands fresh (tests, build, lint, types — whatever the project uses)
2. Read full output, check exit codes, count failures
3. No claims without evidence

**Red flags** — if you're about to say any of these, STOP:
- "Should work now"
- "Looks correct"
- "I'm confident this fixes it"

Evidence required. Run the command. Read the output. Then claim.

**If verification fails:**
- Treat failure as new evidence
- Update hypothesis scores
- Loop back to INVESTIGATE
- Track loop count in RCA State block
