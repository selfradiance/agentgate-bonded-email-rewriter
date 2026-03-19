# Agent 003: Email Rewriter

A bonded email rewriter agent. Rewrites emails using Claude, then asks a human to approve or reject the result. Approve = bond released. Reject = bond slashed. Built on [AgentGate](https://github.com/selfradiance/agentgate).

## What This Is

The agent takes an email and a rewrite instruction, calls the Claude API to rewrite it, posts a bond through AgentGate, shows the human both versions, and resolves based on their judgment.

This is the first bonded agent with **human-in-the-loop verification** — no hash, no script, just a person deciding whether the output is good enough. Agent 001 used deterministic hash verification. Agent 002 used command-based verification. Agent 003 puts a human in the loop.

## How It Works

1. You give it an email file and a rewrite instruction
2. Claude rewrites the email
3. A bond is posted to AgentGate (the agent puts up collateral)
4. Both versions are displayed side by side in the terminal
5. You type `approve` or `reject`
6. Approve → bond released. Reject → bond slashed.

## Prerequisites

- Node.js 20+
- A running [AgentGate](https://github.com/selfradiance/agentgate) instance (local or remote)
- An Anthropic API key (for Claude)
- An AgentGate REST key

## Setup

```bash
git clone git@github.com:selfradiance/agent-003-email-rewriter.git
cd agent-003-email-rewriter
npm install
cp .env.example .env
# Fill in your ANTHROPIC_API_KEY and AGENTGATE_REST_KEY
```

## Usage

```bash
npx tsx src/cli.ts <email-file> "<instruction>"
```

Example:

```bash
npx tsx src/cli.ts examples/sample-email.txt "make this more professional"
```

## Sample Output

```
  Agent 003 — Email Rewriter
  File:         examples/sample-email.txt
  Instruction:  make this more professional
  AgentGate:    http://127.0.0.1:3000

Rewriting email via Claude...
Rewrite complete.

Connecting to AgentGate...
Identity: id_379e0b07-3342...
Bond locked: bond_217cecb7-29cb... (100 cents, 300s TTL)
Action started: action_e5235c31-70b7...

────────────────────────────────────────────────────────────
  ORIGINAL EMAIL:
────────────────────────────────────────────────────────────
hey bob,

can u send me the report asap?? i need it for the meeting
tmrw. also can u check if the numbers in section 3 look
right cuz i think there might be a mistake.

thx
james
────────────────────────────────────────────────────────────
  REWRITTEN EMAIL:
────────────────────────────────────────────────────────────
Subject: Request for Report Review and Delivery

Dear Bob,

Could you please send me the report at your earliest
convenience? I need it for tomorrow's meeting.

Additionally, I would appreciate it if you could review
the numbers in section 3, as I believe there may be an
error that requires correction.

Thank you for your assistance.

Best regards,
James
────────────────────────────────────────────────────────────
Do you approve this rewrite? (approve/reject): approve

════════════════════════════════════════
  RESULT
════════════════════════════════════════
  Verdict:   approve
  Bond:      released
  Action:    action_e5235c31-70b7...
════════════════════════════════════════
```

## Tests

```bash
npm test
```

11 tests across 4 test files. Integration tests require `ANTHROPIC_API_KEY` and `AGENTGATE_REST_KEY` in `.env`.

## Project Structure

| File | Purpose |
|------|---------|
| `src/cli.ts` | Entry point — orchestrates the full rewrite → bond → verdict → resolve flow |
| `src/rewriter.ts` | Calls the Claude API to rewrite an email |
| `src/agentgate-client.ts` | AgentGate API client with Ed25519 signed requests and identity persistence |
| `src/prompt.ts` | Terminal prompt for human approve/reject |
| `examples/sample-email.txt` | Sample email for testing |
| `test/` | 11 tests (rewriter, AgentGate client, full lifecycle approve/reject) |

## Related Projects

- [AgentGate](https://github.com/selfradiance/agentgate) — the bond-and-slash accountability layer for AI agents
- [Agent 001 — File Transform](https://github.com/selfradiance/agent-001-file-transform) — bonded CSV-to-JSON transformation with hash verification
- [Agent 002 — File Guardian](https://github.com/selfradiance/agent-002-file-guardian) — bonded file watcher with command-based verification

## License

MIT
