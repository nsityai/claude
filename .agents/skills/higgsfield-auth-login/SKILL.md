---
version: 0.1.0
name: higgsfield-auth-login
description: |
  Authenticate with Higgsfield AI. Use when the user needs to log in,
  re-authenticate, fix "Session expired" / "Not authenticated" errors,
  check account status, switch accounts, or log out. Triggers on:
  "login to Higgsfield", "authenticate", "session expired", "not authenticated",
  "higgsfield auth", "log in", "sign in", "logout", "who am I", "my account".
  NOT for generating images or videos (use higgsfield-generate), Soul training
  (use higgsfield-soul-id), or marketplace cards (use higgsfield-marketplace-cards).
argument-hint: "[login|logout|status]"
allowed-tools: Bash
---

# Higgsfield Auth Login

Authenticate the user with the Higgsfield platform via the `higgsfield` CLI.

## Step 0 — Ensure CLI is installed

```bash
higgsfield --version 2>/dev/null || curl -fsSL https://raw.githubusercontent.com/higgsfield-ai/cli/main/install.sh | sh
```

If install fails, report the error verbatim and stop.

## Workflow

### Check current status first

```bash
higgsfield account status
```

- If status returns account info (email, plan, credits) and no error → user is already authenticated. Report their account info and ask if they want to re-login or log out.
- If status returns `Session expired`, `Not authenticated`, or any auth error → proceed to login below.

### Login

`higgsfield auth login` is interactive — it opens a browser or prompts for credentials. Instruct the user to run it themselves in their terminal, then confirm when done:

```
Please run this in your terminal:

    higgsfield auth login

Then come back and let me know when it's complete.
```

Wait for the user's confirmation before continuing.

### Verify after login

Once the user confirms login completed:

```bash
higgsfield account status
```

Report the result:
- Success: show email, plan tier, and available credits.
- Failure: show the error and suggest re-running `higgsfield auth login` or checking network/API URL.

### Logout

If the user wants to log out:

```bash
higgsfield auth logout
```

Confirm logout completed and remind them to run `higgsfield auth login` when they want to use the platform again.

## UX Rules

1. Never print raw JSON blobs. Extract email, plan, and credits and summarise in plain text.
2. Respond in the user's language; CLI flags and commands stay English.
3. Do not run `higgsfield auth login` directly via Bash — it is interactive and will hang. Always ask the user to run it in their own terminal.
4. If the error message mentions a specific API URL mismatch (`Stored credentials are for <url-a> but current environment is <url-b>`), tell the user to re-login so credentials are refreshed for the current environment.

## Common errors

| Error | Fix |
|---|---|
| `Session expired` | Run `higgsfield auth login` |
| `Not authenticated` | Run `higgsfield auth login` |
| `Stored credentials are for … but current environment …` | Re-login to refresh credentials for the current API URL |
| `command not found: higgsfield` | Install the CLI first (see Step 0) |
| Login completes but `account status` still fails | Wait 5s and retry; if persistent, check network or contact support |
