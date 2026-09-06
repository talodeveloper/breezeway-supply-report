# Breezeway Supply Report

A browser-based reporting tool for Breezeway vacation-rental cleaning exports.
Drop your Breezeway CSV/XLSX exports in and get clean supply lists, per-property
breakdowns, and a consolidated shopping list — all rendered on the page and
downloadable as Excel.

**Live:** https://talodeveloper.github.io/breezeway-supply-report/

## How to use

1. Open the live link (or open `index.html` in any browser — it works offline).
2. Drag in your export file (e.g. `breezeway-task-supplies-export.csv`). Up to 10 files.
3. Pick the report from the dropdown. Read the tables, or use the **Download … as Excel** buttons.

## Privacy

Everything runs in your browser. No file is ever uploaded to a server. Nothing is
stored — closing the tab clears it. The page uses no cookies and no local storage.

## Reports available

| Report | Source file | What you get |
|---|---|---|
| **Supply lists (weekly)** | `breezeway-task-supplies-export.csv` | Supply detail table, requested-by-property cards, overall "to buy" breakdown, two Excel exports |

## Adding a new report type

The tool is built around a `REPORTS` registry in `index.html`. Adding a report
(payroll, cost, summary, …) means appending **one config object** — no changes to
the upload or parsing code. See **[docs/ADD-A-REPORT.md](docs/ADD-A-REPORT.md)** for a
fill-in-the-blank prompt and the full checklist.

## Development

No build step, no dependencies to install. Edit `index.html`, open it in a browser,
done. [SheetJS](https://sheetjs.com/) is loaded from a CDN for reading/writing spreadsheets.

## Deploying

GitHub Pages serves `index.html` from the `main` branch root. **Every push to `main`
auto-publishes** in about a minute. No manual deploy step.
