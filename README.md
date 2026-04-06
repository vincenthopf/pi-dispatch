# Dispatch Extension

Dispatch is a **pi** extension that lets you use **multiple ChatGPT Codex OAuth accounts** with the built-in **`openai-codex-responses`** API.

It helps you **maximize usable Codex quota** across accounts:

- **Automatic rotation on quota/rate-limit errors** (e.g. 429, usage limit).
- **Prefers untouched accounts** (0% used in both windows) so fresh quota windows don't sit unused.
- Otherwise, **prefers the account whose weekly window resets soonest**.

## Install (recommended)

```bash
pi install npm:pi-dispatch
```

After installing, restart `pi`.

## Install (local dev)

From this directory:

```bash
pi -e ./index.ts
```

## Quick start

1. Add at least one account:

   ```
   /dispatch-login
   ```

   Optionally provide a label: `/dispatch-login work-account`

2. Use Codex normally. When a quota window is hit, Dispatch will rotate to another available account automatically.

## Commands

- `/dispatch-login [label]`
  - Adds/updates an account in the rotation pool. Label is optional (auto-assigns `account-1`, etc.).
- `/dispatch-use`
  - Manually pick an account for the current session (until rotation clears it).
- `/dispatch-status`
  - Shows accounts + cached usage info + which one is currently active.

## How account selection works (high level)

When pi starts / when a new session starts, the extension:

1. Loads your saved accounts.
2. Fetches usage info for each account (cached for a few minutes).
3. Picks an account using these heuristics:
   - Prefer accounts that are **untouched** (0% used in both windows).
   - Otherwise prefer the account whose **weekly** quota window **resets soonest** (5h window is ignored for selection).
   - Otherwise pick a random available account.

When streaming and a quota/rate-limit error happens **before any tokens are generated**, it:

- Marks the account as exhausted until its reset (or a fallback cooldown)
- Rotates to another account and retries

## Checks

```bash
pnpm run lint
pnpm run tsgo
pnpm run test
```
