# Handoff

## 2026-09-06 — initial build + repo/deploy setup

### Done
- Built `index.html`: single self-contained, client-side Breezeway supply
  reporting tool. SheetJS from cdnjs. `REPORTS` registry architecture — new report
  types are one appended config object.
- Implemented the **Supply lists (weekly)** report end to end:
  - source `breezeway-task-supplies-export.csv` (`sourceMatch: /supplies-export/i`)
  - column mapping: property/date/cleaner/item/qty with header variants
  - normalization: `· config` suffix stripped, dates → `YYYY-MM-DD`, qty stripped
    to number, item-less rows dropped
  - output: Supply detail table (full width) + Requested-by-property cards +
    Overall "to buy" breakdown (side-by-side grid); Excel exports
    `supply-detail.xlsx` and `shopping-list.xlsx`
- Verified in browser with synthetic data: routing, header resolution, suffix
  strip, `"4 rolls"` → 4, per-property + overall totals, date range, both exports.
- Fixed 3 bugs found during verification: CSV UTF-8 read, date UTC extraction,
  `XLSX.writeFile` (not `XLSX.utils.writeFile`).
- Added `README.md`, `CLAUDE.md`, `docs/ADD-A-REPORT.md`, `.nojekyll`, `.gitignore`.
- `git init` done locally.

### Deploy status — LIVE
- Repo: https://github.com/talodeveloper/breezeway-supply-report (public)
- Live: https://talodeveloper.github.io/breezeway-supply-report/ (Pages: branch `main`, root)
- Push auth: token embedded in `.git/config` remote URL (same classic PAT as
  talo-guidebook). `credential.helper` is set to `""` for this repo — required,
  because this machine's keychain holds a stale `x-access-token` / `github_pat_…`
  credential (from GitHub Desktop or VS Code) that otherwise gets used and
  rejected. Do not re-enable the helper here.
- Deploy = `git push`. Pages rebuilds in ~1 min.

### Housekeeping (user)
- Rotate the `talo-guidebook` PAT (`ghp_UZgu…`) — it is in plaintext in that repo's
  `.git/config` and was surfaced in the build session. This repo currently reuses
  the same token; swap both when rotating.
- The unused fresh token `ghp_zGIo…` the user generated on 2026-09-06 can be
  deleted (it was never successfully installed — terminal paste corrupted it).

### Next possible work
- Additional report types: payroll (`breezeway-task-payroll-export.csv`), cost,
  summary. Use `docs/ADD-A-REPORT.md`.
