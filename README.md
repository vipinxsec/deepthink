# deepthink

A Claude Code skill that makes AI agents verify before they reason. Structured thinking backed by real evidence — not just longer answers.

## About

AI agents are confident but often wrong. They reason about what code *probably* does instead of reading it. They assume functions exist instead of grepping for them. They predict test results instead of running them.

deepthink enforces **verify first, reason second.** The agent must gather evidence with tools before forming an opinion. Assumptions are tracked separately from verified facts. The skill adapts to problem complexity — simple tasks get no overhead, complex tasks get a prescribed sequence of thinking modes selected automatically based on task type.

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

## How it works

Every task gets classified and assigned the right thinking sequence automatically:

| Task | Mode Sequence |
|---|---|
| Bug | investigate → decompose → commit |
| Design | decompose → explore → evaluate → commit |
| Refactor | investigate → decompose → evaluate → commit |
| Research | investigate → explore → evaluate |
| Decision | decompose → explore → evaluate → commit |
| Implement | investigate → decompose → commit |

### Thinking modes

| Mode | What it does |
|---|---|
| **investigate** | Active fact-finding with tools. Grep the codebase, read files, check git history, run tests. Replace assumptions with evidence. |
| **decompose** | Break the problem into parts. Expose hidden assumptions. Identify the minimal working set. |
| **explore** | Generate 3+ structurally different approaches with explicit tradeoffs. Not surface variants — architecturally distinct options. |
| **evaluate** | Weighted criteria comparison. Find the decisive criterion. Stress-test the winner against its worst failure mode. |
| **commit** | Make the decision. State evidence, tradeoffs, and risks. Give the user something to act on immediately. |

### Evidence tracking

After each mode, the agent records:

- **Verified** — facts confirmed with tools (grep, file reads, test runs)
- **Assumed** — beliefs held but not yet checked
- **Surprised** — things that differed from initial expectation

The "surprised" list catches where the agent's mental model was wrong — before that wrong model corrupts the answer.

## License

MIT
