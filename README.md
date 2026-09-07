# Breezeway Supply Report

A browser-based reporting tool for Breezeway vacation-rental cleaning exports.
Drop your Breezeway CSV/XLSX exports in and get clean supply lists, per-property
breakdowns, and a consolidated shopping list — all rendered on the page and
downloadable as Excel.

**Live:** https://talodeveloper.github.io/breezeway-supply-report/

## How to use

1. Open the live link (or open `index.html` in any browser — it works offline).
2. Drag in **one** Breezeway export (e.g. `breezeway-task-supplies-export.csv`).
3. The report is chosen **automatically from the file name**. Read the tables, or use
   the **Download … as Excel** buttons.
4. Scroll to **Build your own report** to pivot the same file any way you like.

## Privacy

Everything runs in your browser. No file is ever uploaded to a server. Nothing is
stored — closing the tab clears it. The page uses no cookies and no local storage.

## Reports available

| Report | Source file | What you get |
|---|---|---|
| **Supply lists (weekly)** | `breezeway-task-supplies-export.csv` | Supply detail table, requested-by-property cards, overall "to buy" breakdown, two Excel exports |

Any other Breezeway export still works — it just skips the fixed report and goes
straight to the pivot builder.

## Build your own report (pivot)

Below the fixed report, every column of the uploaded file becomes a draggable field.
Drop fields into **Rows** and **Columns**, drop one into **Value**, and pick how to
summarise it (Sum, Average, Min, Max, Count, Count unique). The table builds live,
with row/column totals, and exports to `custom-report.xlsx`.

Two deliberate design choices:

- **One file at a time, always.** The pivot never joins across exports, so it is
  impossible to produce inflated totals from a fan-out join.
- **Watch the aggregation.** Sum is right for per-row measures like `Supply quantity`.
  Fields that repeat across rows of the same task (`Total time`, `Number of people`)
  should use **Average** or **Max** — summing them multiplies by the row count. The
  app shows this as a tip above the table.

`H:MM:SS` values (like `Total time`) are converted to decimal hours so they can be
averaged and summed.

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
