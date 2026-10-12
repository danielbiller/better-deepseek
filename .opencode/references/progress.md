# Progress — Better DeepSeek

## Works (verified 2026-10-12)
- Clean `npm install` (Node v24.21.0, npm 11.19.0) succeeds.
- `npm run build:chrome` succeeds → `dist-chrome/` + `better-deepseek-chrome.zip`.
- Repo at v0.1.15 (`9d19c94`), branch `main`, clean except untracked `.opencode/` + `openspec/`.

## Left to build
- Nothing queued — awaiting user task. Test lanes not yet run in this workspace (unit / e2e / firefox / android).

## Known issues
- 16 npm audit vulnerabilities (3 moderate, 9 high, 4 critical) — unaddressed, needs review before dependency changes.
- Firefox build is experimental; BDS Live unavailable on Firefox (missing browser APIs).
- DeepSeek status API unstable → status tracking unreliable.
- Sandbox bundle large (~1.2MB, gzip ~390kB).

## Decision log
- 2026-10-12: Initialized workspace + created missing memory bank at `.opencode/references/` (6 files).
