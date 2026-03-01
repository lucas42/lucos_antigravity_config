---
trigger: always_on
---

# GitHub Pull Request Workflow

When assigned or asked to work on a GitHub issue or create a pull request, follow this workflow:

## 1. Post a starting comment

Before beginning any code changes, post a comment on the issue using `gh-as-agent` to say you're starting work and give a brief overview of your approach. Write it in the first person, e.g.:

> I'm going to tackle this by updating the API handler to validate the input before passing it to the database layer, then add a test to cover the new behaviour.

Use the `write_to_file` tool to create the JSON payload and pass it via `--input`.

## 2. Create pull requests using `gh-as-agent`

Pull requests must be created using `gh-as-agent`, exactly like issue comments and any other GitHub API calls — **never** use `gh pr create` directly (which uses personal credentials instead of the correct bot identity).

Write the PR body to a file and pass it via `--input`:

```bash
# Step 1: use the Write tool to create /tmp/gh-payload.json, e.g.:
# {"title": "Fix the thing", "head": "my-branch", "base": "main", "body": "Closes #42\n\n..."}

# Step 2: call gh-as-agent
/Users/lucas/antigravity-repos/lucos_agent/gh-as-agent repos/lucas42/{repo}/pulls \
    --method POST \
    --input /tmp/gh-payload.json
```

## 3. Tag commits and pull requests with the issue

Every commit and pull request made as part of the work should reference the issue number. 

1. **Commits**: In commit messages, include the issue reference (e.g. `Refs #42`).
2. **Pull Requests**: In the pull request body, use one of GitHub's standard closing keywords so the issue is automatically closed when the PR is merged into `main`:

```
Closes #42
```

The full list of supported keywords is: `close`, `closes`, `closed`, `fix`, `fixes`, `fixed`, `resolve`, `resolves`, `resolved` — followed by the issue reference (e.g. `Fixes #42` or `Resolves lucas42/lucos_example#42`).

## 4. Comment on unexpected obstacles

If you hit a significant unexpected obstacle during the work — especially one that risks not being able to finish without further input — post a follow-up comment on the issue explaining what you've encountered. Don't silently get stuck or work around something without flagging it. Use `gh-as-agent` to post the comment.

## 5. Don't close issues yourself

Issues should be closed automatically via the closing keyword in the merged PR. Do not close issues manually using the GitHub API unless explicitly instructed to (e.g. told that an issue is now obsolete).
