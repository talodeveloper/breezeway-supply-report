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

### Pending (needs the user)
- Repo not yet created on GitHub. Target: `talodeveloper/breezeway-supply-report`, **public**.
- Fine-grained PAT (Contents: read/write, this repo only) to be provided for the first push.
- After first push: enable GitHub Pages — Settings → Pages → Deploy from a branch →
  `main` / root.
- Rotate the exposed `talo-guidebook` PAT (`ghp_UZgu…`) — unrelated to this repo
  but surfaced in the session.

### Next possible work
- Additional report types: payroll (`breezeway-task-payroll-export.csv`), cost,
  summary. Use `docs/ADD-A-REPORT.md`.
