# 15. Debugging discipline

**Phase 1 — build a tight feedback loop (90% of the fix).** Pick the loop type: failing test at seam · curl/HTTP script · CLI + fixture diff · headless browser · replay captured trace · throwaway harness · property/fuzz loop ("sometimes wrong") · bisection harness · differential loop (old vs new) · HITL script. Tighten: faster, sharper signal (assert the exact symptom), deterministic (pin time, seed RNG).

**Why:** debugging speed is feedback-loop speed; everything after is mechanical.

**Phase 2:** Reproduce → minimize to smallest red scenario (every element load-bearing).

**Phase 3:** 3-5 **ranked, falsifiable hypotheses** before testing. Format: "If X causes it, changing Y makes it disappear." Guessing without hypotheses = random walk.

**Phase 4:** Instrument one variable at a time. Debugger > targeted logs > never "log everything." Tag debug logs `[DEBUG-xxxx]` for cleanup.

**Phase 5:** Regression test **before** the fix, at the correct seam. If no correct seam exists, that's the finding — the architecture prevents lockdown.

**Phase 6:** Cleanup — original repro green, regression test passes, debug logs removed, correct hypothesis named in the commit message.