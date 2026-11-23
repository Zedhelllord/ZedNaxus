# Project memory

## Product intent & audience

- Telegram group bot with multi-account support; core feature is photo pinning via reply `تثبيت`.
- Additional commands identified: `قصف`, `كاتش`, `ماهوراغا` (exact behaviors unverified).
- Primary user language: Arabic (all commands in Arabic).
- Deployment target: Railway with a single shared port; multiple bot accounts run concurrently.

## Brand personality & voice

- Developer-facing status updates; concise, factual, no marketing tone.
- User communicates in Arabic; assistant responds in Arabic.

## Visual design & UX direction

- No direct UI; interaction is via chat commands in Telegram groups.
- Admin panel exists (referenced in code) for account management.
- Core interaction: reply to a target photo with `تثبيت` to pin it.

## Decisions, constraints & rationale

- Pins stored in memory only; cleared on disconnect; not restored on restart.
- Rationale: avoid stale pins; pins valid only for current session.
- Multi-account engine (`accounts-engine`) exists and runs all accounts.
- Current architecture flaw: all accounts share a single command handler (`handleMessage`/`handleEvent`) and one commands object → all accounts respond to the same command in a group, causing interference.
- User requirement: each account must be fully independent — own command state, no cross-account interference, even with 15 accounts in the same group.
- User requirement: all accounts on same Railway port but each with its own host/endpoint.
- Project build passes locally; dependency install blocked in sandbox (unreachable package download).
- Bot cannot be started from assistant environment; user runs/restarts it.

## Open questions & assumptions

- Unresolved: user has not yet clarified which isolation model they want:
  1. Per-account command state isolation (only one account responds per command in a group).
  2. Per-account separate admin URL/host on same port (e.g. `host/account-1`).
  3. Both combined.
- Assumption: pinning is limited to photos (other media types unverified).
- Assumption: only group context supports pinning (not DMs).
- Unknown: exact behavior of `قصف`, `كاتش`, `ماهوراغا` commands.
- Unknown: whether multiple concurrent pins per group are allowed.
- Unknown: bot framework/language (code referenced but not directly inspected in this session).
- Unverified: pin behavior after restart (manual testing required by user).