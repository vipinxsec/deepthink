# deepthink

A Claude Code skill that makes AI agents verify before they reason. Structured thinking backed by real evidence — not just longer answers.

## About

AI agents are confident but often wrong. They reason about what *probably* is instead of checking what actually is — whether that's code, data, a strategy, or a research question.

deepthink enforces **verify first, reason second.** The agent must gather evidence before forming an opinion. Assumptions are tracked separately from verified facts. The skill adapts to problem complexity — simple tasks get no overhead, complex tasks get a prescribed sequence of thinking modes selected automatically based on task type.

Works for code and beyond — debugging, architecture, research, writing, strategy, and any task where being right matters more than being fast.

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

**Writing**
```
/deepthink Write a post-mortem for last week's outage that's honest about root cause without throwing the infra team under the bus.
```

**Strategy**
```
/deepthink Our free tier costs us $80k/month in compute. Should we add usage limits, move to a freemium model, or cut it entirely?
```

The skill also activates automatically for complex reasoning, design decisions, debugging, evaluation, and strategic tasks.

## Before / After

Prompt: *"Why does the `/api/users/:id` endpoint return 403 for admin users?"*

<details>
<summary><b>Without deepthink</b></summary>

> The issue is likely in your role-checking middleware. The admin role probably isn't being recognized — check that your middleware compares roles case-insensitively and that the admin role is spelled correctly in your database. You may also want to verify that the JWT token includes the role claim.

Sounds reasonable. Didn't check anything. Three guesses and a "you may want to."

</details>

<details>
<summary><b>With deepthink</b></summary>

**investigate** — Reads the auth middleware, greps for role checks, checks recent git history:
- Found: `req.user.role === 'admin'` check in `src/middleware/auth.ts:34` — correct, case-sensitive match ✓
- Found: JWT token includes `role` claim — verified in `src/utils/jwt.ts:12` ✓
- Found: `git log` shows CORS middleware was moved last week in commit `a]3f8e2`

**diagnose** — Forms three hypotheses:
1. Role not in JWT → ruled out, verified token includes role
2. Middleware ordering — CORS middleware resets headers on preflight → cheapest test: check middleware order in `server.ts`
3. Role value mismatch in DB → check with `SELECT DISTINCT role FROM users`

Runs cheapest test — reads `server.ts:18-25`:
```
app.use(authMiddleware)    // ← runs first
app.use(corsMiddleware)    // ← strips Authorization header on preflight
```
Preflight request hits auth first with no token → 403.

**commit** — "Move `corsMiddleware` before `authMiddleware` in `server.ts`. The CORS middleware must handle preflight `OPTIONS` requests before auth checks run. This broke in commit `a3f8e2` when middleware order was changed."

Evidence-backed. Root cause identified. One-line fix.

</details>

## How it works

Every task gets classified and assigned the right thinking sequence automatically:

| Task | Mode Sequence |
|---|---|
| Bug | investigate → diagnose → commit |
| Design | decompose → explore → evaluate → commit |
| Refactor | investigate → decompose → evaluate → commit |
| Research | investigate → explore → evaluate |
| Decision | decompose → explore → evaluate → commit |
| Implement | investigate → decompose → commit |
| Writing | decompose → explore → evaluate → commit |
| Strategy | investigate → decompose → explore → evaluate → commit |

### Thinking modes

| Mode | What it does |
|---|---|
| **investigate** | Active fact-finding with tools. Grep the codebase, read files, check git history, run tests. Replace assumptions with evidence. |
| **diagnose** | Form competing hypotheses about the cause. Find the cheapest test that distinguishes them — and run it. Eliminate until one remains. |
| **decompose** | Break the problem into parts. Expose hidden assumptions. Identify the minimal working set. |
| **explore** | Generate 3+ structurally different approaches with explicit tradeoffs. Not surface variants — architecturally distinct options. |
| **evaluate** | Weighted criteria comparison. Find the decisive criterion. Stress-test the winner against its worst failure mode. |
| **commit** | Make the decision. State evidence, tradeoffs, and risks. Give the user something to act on immediately. |

### Evidence tracking

After each mode, the agent records:

- **Verified** — facts confirmed with tools or sources
- **Assumed** — beliefs held but not yet checked
- **Surprised** — things that differed from initial expectation

The "surprised" list catches where the agent's mental model was wrong — before that wrong model corrupts the answer.

## License

MIT
