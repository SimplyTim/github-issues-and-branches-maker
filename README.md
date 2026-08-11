# GitHub Issue & Branch Maker

A dependency-free, browser-only utility that turns a two-column CSV into GitHub issues and branches. The entire application lives in `index.html`; there is no backend and the token is sent only to `api.github.com`.

## Run it

Serve the directory with any static web server. For example:

```powershell
python -m http.server 8000
```

Then open <http://localhost:8000>.

Opening `index.html` directly may work, but a local server is more reliable for browser security and API requests.

## GitHub token

A fine-grained personal access token should be limited to the target repositories and have:

- **Metadata: read** — list accessible repositories
- **Contents: write** — create branches
- **Issues: write** — create issues

The “Remember” checkbox stores the token unencrypted in this browser's `localStorage`. Do not enable it on a shared device. The **Forget** button deletes the saved value.

## CSV format

The file must have exactly these two headers, in this order:

```csv
name of sim,description
coastal-flooding,"Model coastal flooding under a high-tide scenario."
forest-recovery,"Estimate recovery over a ten-year window."
```

`name of sim` becomes both the issue title and branch name. Because it is used unchanged as a branch name, it must follow Git branch naming rules. Blank values, duplicate names, extra columns, and invalid branch names are rejected before creation.

## Important behavior

- The selected repository's default branch is prefilled as the base branch, and can be changed.
- Each row creates the branch first, then the issue.
- GitHub has no transaction covering both operations. If issue creation fails after branch creation, the page reports that partial result and an in-session retry resumes with the missing issue. After a page reload, verify partial results in GitHub before running the same CSV again.
- Existing branches are never overwritten.
- The page processes rows sequentially to reduce the chance of GitHub secondary rate limits.

## Security notes

- Serve this exact file yourself; do not paste a token into an untrusted hosted copy.
- Prefer a short-lived, fine-grained token scoped to only the repositories you need.
- The app does not use cookies, analytics, external scripts, or a server.
