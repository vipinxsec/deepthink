# deepthink

A Claude Code skill that makes AI agents verify before they reason. Structured thinking backed by real evidence — not just longer answers.

## About

Most AI reasoning skills tell agents to "think harder." The result is longer responses, not better ones. The agent spends more tokens reasoning about what code *probably* does instead of just reading it.

deepthink takes a different approach: **verify first, reason second.** Before the agent forms an opinion, it must gather evidence — grep the codebase, read the actual files, check git history, run the tests. Assumptions are tracked separately from verified facts, so the final answer distinguishes what was checked from what was guessed.

The skill adapts to problem complexity. Simple tasks get no overhead. Complex tasks get a prescribed sequence of thinking modes — investigation, decomposition, exploration, evaluation, and commitment — selected automatically based on what kind of problem you're solving. No manual mode selection, no one-size-fits-all process.

## Install

```
npx skills add vipinxsec/deepthink
```

## Usage

Prefix your prompt with `/deepthink`:

**Debugging**
```
/deepthink Why do uploaded files silently fail when they exceed 10MB?
```

**Architecture & Design**
```
/deepthink Should we use WebSockets or SSE for our real-time notifications system?
```

**Refactoring**
```
/deepthink This payment processing module has grown to 2000 lines — how should we break it apart?
```

**Research**
```
/deepthink What are the security implications of storing JWTs in localStorage vs httpOnly cookies for our auth flow?
```

**Decision Making**
```
/deepthink We need to choose between PostgreSQL and DynamoDB for our multi-tenant SaaS. Our team has more SQL experience but we expect unpredictable traffic spikes.
```

**Implementation**
```
/deepthink Add rate limiting to our public API endpoints — we're seeing abuse on /api/search.
```

The skill also activates automatically for complex reasoning, design decisions, debugging, and evaluation tasks.

## The problem

AI agents are confident but often wrong. They reason about code instead of reading it. They assume functions exist instead of grepping for them. They predict test results instead of running them. Thinking harder doesn't help if the thinking isn't grounded in reality.

deepthink fixes this by enforcing **active verification** — the agent must check facts with tools before building on them.

## How it works

### 1. Classify and prescribe

Every task gets classified, and the right thinking sequence is prescribed automatically:

| Task | Mode Sequence |
|---|---|
| Bug | investigate → decompose → commit |
| Design | decompose → explore → evaluate → commit |
| Refactor | investigate → decompose → evaluate → commit |
| Research | investigate → explore → evaluate |
| Decision | decompose → explore → evaluate → commit |
| Implement | investigate → decompose → commit |

Simple tasks skip modes entirely — no forced deep thinking on straightforward work.

### 2. Five thinking modes

| Mode | What it does |
|---|---|
| **investigate** | Active fact-finding with tools. Grep the codebase, read files, check git history, run tests. Replace assumptions with evidence. |
| **decompose** | Break the problem into parts. Expose hidden assumptions. Identify the minimal working set. |
| **explore** | Generate 3+ structurally different approaches with explicit tradeoffs. Not surface variants — architecturally distinct options. |
| **evaluate** | Weighted criteria comparison. Find the decisive criterion. Stress-test the winner against its worst failure mode. |
| **commit** | Make the decision. State evidence, tradeoffs, and risks. Give the user something to act on immediately. |

### 3. Evidence tracking

After each mode, the agent records:

- **Verified** — facts confirmed with tools (grep, file reads, test runs)
- **Assumed** — beliefs held but not yet checked
- **Surprised** — things that differed from initial expectation

The "surprised" list catches where the agent's mental model was wrong — before that wrong model corrupts the answer.

### 4. Pre-delivery verification

Before the final answer, the agent must confirm:
- Every factual claim is backed by evidence gathered with tools
- At least one initial assumption was proven wrong (if nothing surprised you, you didn't investigate hard enough)
- Rejected alternatives have specific, articulable reasons for rejection
- The answer addresses the failure modes identified during orientation

## Design principles

- **Verify, don't reason.** If you can check it, check it. `grep` beats guessing.
- **Minimum effective depth.** Use only the modes this problem needs.
- **Evidence over intuition.** Track what's checked vs what's assumed.
- **Disagree when evidence says to.** Deep thinking includes independent thinking.

## License

MIT
