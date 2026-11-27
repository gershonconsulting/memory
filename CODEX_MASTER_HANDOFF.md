# CODEX MASTER HANDOFF — Fri 2026-11-27 → Mon 2026-11-30

Claude hit 94% of its weekly allowance. Until Monday Claude only does **reports and client information**.
**Codex: you own development until Monday.** Pick up the items below, open PRs, merge what is safe, and log what you did at the bottom of this file.

Owner: Olivier Attia (CTO / decision-maker, does NOT use a terminal, never ask him to run commands, upload or click).
Humans: **Charles** = web admin panels (Cloudflare, GitHub settings, Supabase secrets, uploads). **Ariel** = code/repo/VPS.
Org: https://github.com/gershonconsulting — every repo has a `HANDOFF.txt` at its root: **read it first**.
Pickup index for all 59 repos: https://github.com/gershonconsulting/memory/blob/main/HANDOFF.txt

## House rules (short)
1. Work on a branch `codex/<YYYY-MM-DD>-<topic>`, open a PR, run the repo's tests.
2. **Merge yourself** when tests pass AND the change is low-risk: colours, wording, version bumps, bug fixes with a test, internal reports/emails (to report@ / olivier@ / sales@ / tools@), dashboards, docs. Then check the live site shows the new version.
3. **Do NOT merge — leave for Olivier's OK**: DB migrations or data deletion; anything clients receive (sender, content, new client emails); secrets, logins, permissions, DNS; anything touching money; failing tests.
4. Never put a secret in code. Never fabricate test results. Never send email to clients/prospects.
5. Brand: Gershon.ai red `#FE1B04`, Linalysis design is the master look. Internal senders are "GC <App>".
6. Bump the app version on every change; update that repo's HANDOFF.txt (✅ done / ❌ pending).

## 1. Open PRs — status and what to do
| PR | Status | Action for Codex |
|---|---|---|
| https://github.com/gershonconsulting/radar/pull/59 | Olivier said OK. Blocked on a secret | Wait until Charles creates `DAILY_REPORT_CRON_SECRET` in Supabase Edge Function Secrets + same value in pg_cron job 1. Then rebase on main, tests (16/16), merge, deploy `daily-report`, check next Radar Report arrives. |
| https://github.com/gershonconsulting/radar/pull/42 (draft) | Waiting for Olivier's OK (new data-collection consent) | Rebase onto main (6 conflicts, versions 1.22.2 vs live 1.27.x). Do NOT delete the extension ZIPs until they are published in GitHub Releases. Leave unmerged. |
| https://github.com/gershonconsulting/campaign/pull/7 | Waiting for Olivier's OK (DB migration) | Keep it green against `preview` branch. Do not merge. |
| https://github.com/gershonconsulting/campaign/pull/1 (draft) | Superseded | No action. |
| https://github.com/gershonconsulting/talkingpoints/pull/46 | `[DO NOT MERGE]` test runs | No action. |

## 2. Code work Codex can do now (PR → merge if safe)
1. **radar — shared secret hard-coded in 12 files** (incl. browser `app.html` and the Chrome extension). Move every Supabase function to read env secrets (same pattern as radar #59). Open PR, **do not merge** (secrets) — list the env names Charles must create. Then Charles rotates the old secret.
2. **radar — DB-only function `radar_ext_activity`**: write the missing migration file into the repo (no apply).
3. **radar — LinkedIn warning alert SQL** (`radar_linkedin_warning_alert`: table `radar_li_alerts` + trigger on `activity_log`) is merged (#60, Radar 1.27.2) but NOT applied in Supabase. Leave the SQL ready; applying needs Olivier/Supabase approval.
4. **E2 — `visa_signals/signals.py`** still holds the SFTP password: read it from `E2_SFTP_PASSWORD` (same as E2 #6). Low-risk refactor, merge if pytest passes. Note: production `/opt/e2` is hand-updated by Ariel; `E2_SFTP_PASSWORD` must be in `/opt/e2/.env` BEFORE pulling main.
5. **E2 #4** re-indented 3 nesting blocks from logic — review the PR #4 body and add tests around those blocks: https://github.com/gershonconsulting/E2/pull/4
6. **health.gershon.ai** — source is not in any repo; title still "Gershon Health — Sign in"; data stale since 11-03 (client_platform_matrix filled by hand). Live-feeds plan: `task` repo `docs/health-live-feeds.md`. Codex: implement the live feeds in the repo that owns the matrix; Ariel locates the Cloudflare project.
7. **americanabroad.net** — live build is stale (+163px on phone, JS error, no brand red); repo main is clean. Needs an FTP deploy (Charles) — or add a deploy workflow (put workflow text in the PR; the Claude connector cannot write `.github/workflows`).
8. **Upload-only apps → connect to GitHub** so merges deploy: gauge (`site/`), dash (crm repo), tools (zip e44ab79). Until then Charles uploads current main.
9. **socialtracking / talkingpoints** internal senders already fixed; check **radar handoff SQL 2026-11-09** still sends as "Radar <radar@gershoncrm.com>" → should be "GC Radar".
10. **Scheduled-task scripts** that live in repos (report #10, radar #44 li-health, E2 #2) are merged but wait on secrets: `LI_HEALTH_CRON_SECRET`, `GATE_DB_URL` (radar), report #10 + E2 #2 secrets, pulse `DAILY_REPORT_SECRET`, publish `UNSPLASH_ACCESS_KEY` + `PIXABAY_API_KEY`, finance `XERO_CLIENT_ID/SECRET`.

## 3. For Charles (browser/admin — not code)
- Supabase secrets listed in §2.10 + `DAILY_REPORT_CRON_SECRET` (radar #59).
- GitHub → campaign repo → Settings → **Change visibility → Private** (it is public with campaign screenshots).
- Uploads: gauge `site/`, dash, tools zip, americanabroad FTP.
- Reload Chrome extension **0.28.1** on the collector Chrome (still 0.27.0 → Social collection stopped 11-23→25).
- Re-sign the Sales Navigator session for Linalysis account `cgd.tournage` (fails daily).
- Open the publishing browser + LinkedIn sign-in (posts not going out).
- Rotate: Travelpayouts token, radar shared secret (after §2.1), E2 SFTP password.

## 4. For Ariel (VPS / repo)
- `/opt/e2`: add `E2_SFTP_PASSWORD` to `.env`, then pull main (E2 #6 merged, b5b5204); dry-run first.
- Harvey: VPS pull pending (harvey #17 merged).
- Put health.gershon.ai source in a repo.

## 5. Live and ready for Olivier to test (done this week ✅)
- Radar 1.27.2 + daily report v10.1 — radar #57 #58 #60
- Task 1.3.9 task.gershon.ai — task #25
- Social 4.87.2 + extension 0.28.1 — social #51 #52 #53
- Social Tracking 0.9.2 — socialtracking #8
- Talking Points 1.17.2 — talkingpoints #52 #53
- Publish 0.29.0 — publish #55 · Pulse 5.16.0 — pulse #14

## 6. Scheduled tasks (Claude side) — what keeps running until Monday
Reports keep running (#28 morning summary, #40 6 PM test report, #39 LinkedIn check, Morning brief, Heartbeat).
Token-heavy action tasks should be paused until Monday: **#27 Nightly project push** (tonight 9:15 PM ET), **#35 CC02**, **#43 CC10**, **#21**. Claude's attempt to pause them from chat was refused by a safety check; Olivier toggles them off/on at claude.ai/code/routines.

## 7. Codex log (append below)
| Date | Repo / PR | What | Merged? | Live checked? |
|---|---|---|---|---|
