# GitHub Authentication: Always Use lucos-agent Bot

When making GitHub API calls, **always authenticate as the `lucos-agent` GitHub App** rather than using personal credentials (`gh api` directly). This ensures that all issues, comments, and pull requests appear under the `lucos-agent[bot]` account with its associated avatar, keeping bot activity clearly distinct from human activity.

## Use `gh-as-agent` instead of `gh api`

The script `lucos_agent/gh-as-agent` (relative to the workspace root `/Users/lucas/antigravity-repos`) is a wrapper around `gh api` that handles token generation automatically. Use it in place of `gh api` for all GitHub API interactions.

### Full path to the script

```
/Users/lucas/antigravity-repos/lucos_agent/gh-as-agent
```

### Basic usage

The script accepts the same arguments as `gh api`:

```bash
/Users/lucas/antigravity-repos/lucos_agent/gh-as-agent repos/lucas42/{repo}/issues \
    --method POST \
    --input /tmp/gh-payload.json
```

To authenticate as a different app persona, pass `--app <app-name>` as the **first** argument:

```bash
/Users/lucas/antigravity-repos/lucos_agent/gh-as-agent --app lucos-issue-manager \
    repos/lucas42/{repo}/issues \
    --method POST \
    --input /tmp/gh-payload.json
```

## Handling request bodies with text content

When the request body contains Markdown (e.g. issue titles/bodies, PR descriptions, comments), **write the JSON payload to a temporary file first** and pass it via `--input`. This prevents backticks and other special characters in Markdown from being misinterpreted as shell command substitution (which would trigger a manual-approval prompt).

```bash
# Step 1: write the payload to a file using the write_to_file tool, e.g. /tmp/gh-payload.json:
# {"title": "Issue title", "body": "Issue body with `code` and **markdown**"}

# Step 2: call gh-as-agent with --input
/Users/lucas/antigravity-repos/lucos_agent/gh-as-agent repos/lucas42/{repo}/issues \
    --method POST \
    --input /tmp/gh-payload.json
```

For requests with no body or simple flag values (e.g. adding labels), `-f` flags are fine:

```bash
/Users/lucas/antigravity-repos/lucos_agent/gh-as-agent \
    repos/lucas42/{repo}/issues/{number}/labels \
    --method POST \
    -f labels[]="agent-approved"
```

## Credentials

The script reads credentials from a `.env` file in the `lucos_agent/` directory. If the `.env` file is missing, pull it down from lucos_creds:

```bash
scp -P 2202 "creds.l42.eu:lucos_agent/development/.env" /Users/lucas/antigravity-repos/lucos_agent/
```

## Summary

| Do | Don't |
|----|-------|
| `/Users/lucas/antigravity-repos/lucos_agent/gh-as-agent repos/…` | `gh api repos/…` |
| Write Markdown payloads to a file and use `--input` | Embed Markdown in `-f body="…"` |
| Default app (`lucos-agent`) for general use | Use personal GitHub credentials |
