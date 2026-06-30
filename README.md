# frogbot-monorepo-demo-v2

Public monorepo demo for **Frogbot V2** against `https://tokyoshiftleft.jfrog.io/`.

V2 counterpart of [frogbot-monorepo-demo](https://github.com/Jordanh1996/frogbot-monorepo-demo) — same layout and seeded CVEs, different Frogbot action tag.

Two sub-projects, each with seeded CVEs on `main`:

| Project | Stack | Seeded vulnerability |
|---------|-------|----------------------|
| `frontend/` | npm | `minimatch@3.0.2` (ReDoS, CVE-2022-3517), `mpath@0.7.0` (prototype pollution) |
| `backend/` | pip | `pyjwt==1.7.1` (CVE-2022-29217), `urllib3==1.26.5` (multiple CVEs) |

**Pull-request scan:** open a PR that adds `axios@0.21.0` to `frontend/` (with `package-lock.json`) and
`requests==2.25.0` to `backend/` — Frogbot should report newly introduced issues.

## Frogbot

- `.frogbot/frogbot-config.yml` — monorepo project definitions (`frontend` + `backend`)
- `.github/workflows/frogbot-scan-repository.yml` — full-repo scan on `main` (`jfrog/frogbot@v2`)
- `.github/workflows/frogbot-scan-pull-request.yml` — diff scan on PRs (`jfrog/frogbot@v2`)

V2 reads scanner settings from the repo-local `frogbot-config.yml` (no Config Profile).
Auto-fix is disabled (`skipAutoFix: true`); workflows set `JF_FAIL: "FALSE"` so CI stays green while findings appear in logs/comments.

## GitHub Security

Same integration as the [v3 counterpart](https://github.com/Jordanh1996/frogbot-monorepo-demo) — see
[Frogbot GitHub scan results docs](https://docs.jfrog.com/security/docs/github-1). Workflows set
`JF_UPLOAD_SBOM_TO_VCS` and `JF_UPLOAD_PR_SECURITY_RESULTS_TO_VCS` for Dependency Graph + Security tab.

**V2 on public repos** requires a GitHub **`frogbot` environment** (with reviewers) and
`environment: frogbot` in the PR workflow. The first PR scan may wait for deployment approval.

Dependency graph SBOM upload also requires vulnerability alerts enabled on the repo
(`PUT /repos/{owner}/{repo}/vulnerability-alerts`).
