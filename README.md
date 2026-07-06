# My Site

Project developed and maintained by Naser Aljed as part of MSc research in securing AI-integrated CI/CD pipelines.

This project uses simulated vulnerabilities for testing purposes only. No real credentials or secrets are used.

## What this repo does

This repo has two GitHub Actions workflows that run automatically on every push to main.

### 1. security.yml (Security Check)

Scans the repo for hardcoded secrets and sensitive files before anything else runs.

It checks for:
- Patterns like `password=`, `api_key=`, `token=`, `secret=`, `BEGIN PRIVATE KEY`, `sk-`
- Sensitive files: `.env`, `*.pem`, `*.key`

If it finds any of these, the workflow fails and blocks the next step (docs.yml) from generating documentation.

Limitation: this scan only checks the current file tree. It does not check git history, so a secret committed and later removed would still exist in an old commit and would not be caught. It also cannot detect encoded content, like base64.

### 2. docs.yml (Generate Docs)

Runs only after security.yml finishes. Uses the OpenAI API (gpt-4o-mini) to generate a developer guide from `index.html`.

- If security.yml passes, it generates `docs/developer-guide.md` with a project overview, language used, and a Mermaid flowchart of the site's user flow.
- If security.yml fails, it writes a blocked message instead: "Documentation generation blocked due to security policy violation."

## How to run or review this

1. Push a change to main.
2. Check the Actions tab to see security.yml run first.
3. If it passes, docs.yml runs next and updates `docs/developer-guide.md`.
4. If you want to test the block, add a fake secret like `api_key=12345` to any file and push. security.yml should fail and docs.yml should either not run or write the blocked message.

## Files

- `index.html` - the site
- `.github/workflows/security.yml` - secret scanning
- `.github/workflows/docs.yml` - AI documentation generation
- `docs/developer-guide.md` - auto-generated developer guide
