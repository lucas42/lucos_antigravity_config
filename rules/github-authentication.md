---
trigger: always_on
---

# GitHub Authentication: Always Use lucos-developer Persona

When making GitHub API calls, **always authenticate as the `lucos-developer` GitHub App** rather than using personal credentials (`gh api` directly). This ensures that all issues, comments, and pull requests appear under the `lucos-developer[bot]` account with its associated avatar, keeping bot activity clearly distinct from human activity.

## Use `gh-as-agent` instead of `gh api`

The script `lucos_agent/gh-as-agent` (relative to the workspace root `/Users/lucas/antigravity-repos`) is a wrapper around `gh api` that handles token generation automatically. Use it with the `--app lucos-developer` flag in place of `gh api` for all GitHub API interactions.

### Full path to the script

```
/Users/lucas/antigravity-repos/lucos_agent/gh-as-agent
```

### Basic usage

The script accepts the same arguments as `gh api`:

```bash
/Users/lucas/antigravity-repos/lucos_agent/gh-as-agent --app lucos-developer repos/lucas42/{repo}/issues \
    --method POST \
    -f title="Issue title" \
    -f body="Issue body with \`code\` and **markdown**"
```

To authenticate as a different app persona, pass `--app <app-name>` as the **first** argument:

```bash
/Users/lucas/antigravity-repos/lucos_agent/gh-as-agent --app lucos-issue-manager \
    repos/lucas42/{repo}/issues \
    --method POST \
    -f title="Issue title"
```

## Handling request bodies

When the request body contains Markdown (e.g. issue titles/bodies, PR descriptions, comments), use `-f` flags to pass the fields directly to the command.

```bash
/Users/lucas/antigravity-repos/lucos_agent/gh-as-agent --app lucos-developer repos/lucas42/{repo}/issues \
    --method POST \
    -f title="Issue title" \
    -f body="Issue body with \`code\` and **markdown**"
```

For fields that are not strings (e.g. simple lists), you can also use `-f`:

```bash
/Users/lucas/antigravity-repos/lucos_agent/gh-as-agent --app lucos-developer \
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
| `/Users/lucas/antigravity-repos/lucos_agent/gh-as-agent --app lucos-developer repos/…` | `gh api repos/…` |
| Use `-f` flags for Markdown payloads | Use persona-neutral `gh` for bot actions |
| lucos-developer persona (via `--app lucos-developer`) | Use personal GitHub credentials |
