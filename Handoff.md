# Handoff

## 2026-09-06 (later) — one-file model + pivot builder

### Done
- **Upload is now one file at a time.** `setFile()` replaces the old multi-file array.
- **Report is auto-detected from the filename** via each report's `sourceMatch`. The
  dropdown became a manual override, with a `""` = "no fixed report — pivot only" option.
- **New: "Build your own report" pivot builder**, below the fixed report, on every file.
  Drag fields into Rows / Columns / Value (with `<select>` fallbacks for touch), pick
  Sum / Average / Min / Max / Count / Count unique. Live table with row, column and
  grand totals; exports to `custom-report.xlsx`.
- Verified against the real Breezeway header set: auto-detect, fixed report, pivot
  numbers (row/col/grand totals all reconcile), `H:MM:SS` → decimal hours, the
  unrecognized-file pivot-only path, and both Excel exports.

### Design decisions (settled with the user — do not revisit without reason)
- **Pivot is single-file only, forever.** Cross-file / multi-export pivoting was
  explicitly rejected. Reason: the exports sit at different grains — Summary is one
  row per task, Supplies/Cost are one row per line item, Payroll is one row per
  assignee — and joining them fans out and inflates sums. Concrete example found in
  the real data: payroll repeats the task's `Total time` (`101:51:22`) on all 3
  assignee rows, so summing it triples the number.
- If cross-file is ever revisited, the only safe model is **aggregate each file to a
  shared key (`Task ID` / `Property ID`) first, then align** — never a row-level join.

### Breezeway data notes (from real exports)
- All exports share a ~29-column task backbone; `Task ID` and `Property ID` are stable
  keys. Property *names* are not — the `· <config>` suffix appears inconsistently.
- Header drift: payroll uses `Assignee` (others `Assignees`) and `Property internal ID`
  (others `Property Internal ID`), and omits ~10 backbone columns.
- Item names are not normalized: the bilingual halves flip order between exports
  (`Toilet paper/Papel de baño` vs `Papel de baño/Toilet paper`), and some items have
  a blank `Supply ID`. An alias map would be needed for exact item consolidation.
- All rate/cost fields are currently `0.00` or blank ("No Charge/Internal") — payroll
  and cost reports would render empty until there is real priced data.

### Next possible work
- Reports for summary / payroll / cost exports (hold until priced data exists).
- Optional: a Filters zone in the pivot; an item-name alias map.

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
