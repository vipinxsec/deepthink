# Diagnose

## Core move

Turn symptoms into competing cause hypotheses. Then find the cheapest test that distinguishes them — and run it.

Diagnosis is not guessing the most likely cause and fixing it. It is generating multiple plausible causes and systematically eliminating them.

## Build the diagnostic frame

Before hypothesizing, establish what you know:

- What is the expected behavior?
- What is the observed behavior?
- What is the exact difference?
- When did it last work correctly? What changed since then?
- How often does it fail? Always, intermittently, under specific conditions?
- What has already been tried?

Use evidence from `investigate` — don't re-derive facts you already gathered.

## Generate competing hypotheses

Produce 3+ plausible causes that explain the symptoms. Each must name a specific mechanism, not just a category.

Bad: "it might be a configuration issue"
Good: "the nginx client_max_body_size is set to 1M, rejecting uploads before they reach the app server"

Useful hypothesis families:

- **State mismatch** — stale cache, out-of-sync replica, migration not run
- **Boundary/limit** — size limit, timeout, off-by-one, integer overflow
- **Environment** — version mismatch, missing env var, different config in prod vs dev
- **Timing** — race condition, ordering dependency, async callback firing late
- **Hidden dependency** — implicit coupling, transitive import, shared global state
- **Input assumption** — unexpected encoding, null where non-null expected, type coercion
- **Interaction effect** — two individually correct components producing incorrect behavior together
- **Observation error** — logging the wrong variable, reading stale output, testing the wrong endpoint

Don't anchor on the first plausible cause. The most obvious explanation is often wrong — that's why the bug wasn't caught earlier.

## Discriminate

For each hypothesis, answer:

1. What would I observe if this cause is real?
2. What would I observe if this cause is NOT real?
3. What is the cheapest check that distinguishes this from the other hypotheses?
4. Would the proposed fix remove the cause or just mask the symptom?

Then run the cheapest distinguishing check. Actually run it — grep, read the config, check the log, trace the code path, run the test with specific input. Don't reason about what the check would show.

## Narrow and repeat

After each check:
- Eliminate hypotheses that the evidence contradicts
- Promote hypotheses that the evidence supports
- If all hypotheses are eliminated, generate new ones — the real cause is something you haven't considered yet

Stop when one hypothesis explains all symptoms and the evidence rules out the alternatives.

## Failure checks

- Explaining a symptom with a renamed symptom ("it fails because it's broken")
- Anchoring on the first plausible cause without generating alternatives
- Proposing a fix before confirming the mechanism
- Ignoring recent changes or environment differences
- Treating correlation as cause
- Gathering evidence without knowing what it would rule out
- Checking easy hypotheses while avoiding the hard-to-test one

## Done when

The root cause is identified with evidence that rules out the competing hypotheses. You can explain the mechanism (not just the symptom), and the proposed fix addresses the cause, not a side effect. Carry forward the confirmed cause, the evidence that confirmed it, and the fix.
