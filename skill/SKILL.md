---
name: deepthink
description: "Deep structured reasoning with active verification. Use when the task requires complex reasoning, design decisions, debugging, or evaluation. Combines structured thinking with active verification — every claim is checked, not just reasoned about. If the task is simple and the user has not manually invoked this skill, do not use it."
---

# DeepThink

Think better, not more. Every claim must be grounded in evidence you gathered, not assumptions you reasoned about.

## 1. Orient

Answer silently:

- **Goal**: What outcome does the user actually need?
- **Type**: `bug` | `design` | `refactor` | `research` | `decision` | `implement` | `writing` | `strategy`
- **Failure modes**: Top 3 ways this goes wrong without careful thought.
- **Complexity**: Simple (1-2 moving parts) or complex (3+ interacting concerns)?

**Simple tasks**: Skip modes entirely. Check the relevant material and act.

**Complex tasks**: Follow the prescribed sequence:

| Type | Sequence |
|---|---|
| bug | investigate → diagnose → commit |
| design | decompose → explore → evaluate → commit |
| refactor | investigate → decompose → evaluate → commit |
| research | investigate → explore → evaluate |
| decision | decompose → explore → evaluate → commit |
| implement | investigate → decompose → commit |
| writing | decompose → explore → evaluate → commit |
| strategy | investigate → decompose → explore → evaluate → commit |

## 2. Execute

Work through each mode in sequence. Carry forward insights between modes. Track evidence: what you **verified** (checked with tools/sources) vs **assumed** (believed but didn't check).

---

### investigate

Replace assumptions with evidence. Do, don't just think.

- **Read before assuming.** Read the actual source — file, doc, page — before forming opinions.
- **Search before guessing.** If your plan depends on something existing, verify: grep the code, search the web, check the docs. If search returns nothing, your plan has a false premise.
- **Check history.** For code: `git log`, `git blame`. For everything else: changelogs, prior decisions, version history. Understanding WHY prevents "fixing" what's intentional.
- **Verify before claiming.** If you say it's true, confirm it. Run the test, check the source, read the document.
- **Trace the flow.** Follow the actual path from input to output — don't trust your mental model.

Look for: dependencies, established patterns, existing work that already solves the problem, contradictions between sources.

---

### diagnose

Turn symptoms into competing hypotheses. Eliminate with evidence.

- **Frame it.** Expected behavior, observed behavior, exact difference, what changed recently.
- **3+ hypotheses.** Each names a specific mechanism, not a category. "Nginx client_max_body_size rejects uploads" not "configuration issue."
- **Discriminate.** For each: what would confirm it? What would rule it out? What's the cheapest distinguishing test?
- **Run the test.** Don't reason about what it would show — actually run it.
- **Narrow and repeat.** Eliminate contradicted hypotheses. If all eliminated, generate new ones.

Hypothesis families: state mismatch, boundary/limit, environment difference, timing/ordering, hidden dependency, input assumption, interaction effect, observation error, misaligned incentive, wrong abstraction.

---

### decompose

Break the problem apart. Expose what's hidden.

- **Map it.** Actual goal vs stated goal. Entities, boundaries, relationships. What's treated as fixed that might not be?
- **Constraints.** Hard (must), soft (should), or assumed (untested)?
- **Reframe if stuck.** Change the unit of analysis, separate proxy from real goal, split by actor/time/failure mode.
- **Minimal working set.** What's the smallest set of changes/decisions that addresses the actual goal? Strip everything else.

---

### explore

Generate structurally different approaches. Not surface variants.

- **3+ approaches.** Each must differ in architecture, data flow, or fundamental tradeoff. For each: what it does differently, key advantage, key risk, what it trades away.
- **Creative operators** (pick 2-3): inversion, constraint removal, analogy, subtraction, scale shift, temporal shift, decomposition, recombination, boundary search.
- **Protect strange ideas.** Extract the useful mechanism before killing unusual approaches. Ask: "what would have to be true for this to be right?"

---

### evaluate

Compare with weighted criteria, not impressions.

- **Define criteria** from the actual goal — don't import a generic rubric.
- **Weight them.** Critical (pass/fail), high (primary drivers), low (tiebreakers). State WHY.
- **Score with evidence.** Cite specific aspects, not vibes. Name the strongest objection to each option.
- **Find the decisive criterion.** The one that actually swings the decision.
- **Stress-test the winner.** Worst failure mode? What assumption, if wrong, makes this the worst choice? Does the runner-up handle any critical scenario better?

---

### commit

Stop analyzing. Decide.

- **State the decision** and the one-sentence reason.
- **Key evidence** — the 2-3 facts that support it.
- **What you rejected** and specifically why.
- **Top risk** — how it could go wrong and how to detect it.
- **Reversal trigger** — what signal means reconsider.

When one option clearly dominates: just state it, the evidence, and the main tradeoff. Don't force a balanced comparison.

---

## 3. Deliver

1. **Answer/Decision** — What to do and why
2. **Evidence** — What you checked that supports this
3. **Tradeoffs** — What you gave up and why that's acceptable
4. **Risks** — What could still go wrong

Surface uncertainty near the claims it limits. Verify, don't reason. Evidence over intuition.
