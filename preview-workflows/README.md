# Preview workflows for nightly branches (handoff for Charles)

Written 2026-10-20 by the nightly push. The GitHub connector Claude uses cannot write files under `.github/workflows/` (403, no "workflows" permission), so most repos give nightly branches NO preview URL — only `radar` (Pages git-integrated) does.

Fix, once per repo (GitHub web editor, ~1 min each): copy `<repo>.yml` from this folder to `.github/workflows/preview-nightly.yml` in that repo, on `main`. It only fires on `nightly/**` branches and always deploys with `--branch=<alias>`, so production is never touched. Uses the existing CLOUDFLARE_API_TOKEN / CLOUDFLARE_ACCOUNT_ID secrets.

Caveats:
- Pages *preview* environments have their own variables/secrets. Pages that need login or API keys may show errors on previews until the same values are added for the Preview environment (Olivier's call — not done by Claude).
- linalysis: its production deploy also builds the extension zip and writes .assetsignore; for a pure UI preview the plain `pages deploy .` is enough.
- gershonAI (gershon.ai hub) and nexashare (a Worker) are not covered: the hub's Pages project is direct-upload with no CI at all, and NexaShare previews need a Worker preview environment.
- Alternative to all of this: grant the Claude GitHub App the "Workflows: read & write" permission so Claude can install these itself.
