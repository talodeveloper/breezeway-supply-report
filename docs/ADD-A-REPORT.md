# Adding a new report type

The tool is a single `index.html` driven by a `REPORTS` registry. A new report
(payroll, cost, summary, damage, inspection, …) is **one config object appended to
that array**. Upload, routing, and parsing never change.

---

## Fast path — hand this prompt to Claude

Copy the block below, fill in the four blanks from a real export file, and give it
to Claude Code (or any capable AI) with `index.html` in context.

> Add a new report type to `index.html` in this repo. It follows the existing
> `REPORTS` registry pattern — append **one object**, change nothing else. Use the
> commented-out template in the file as the starting shape.
>
> **Report:** _\<dropdown label, e.g. "Payroll (weekly)"\>_
> **Source file:** _\<real filename, e.g. `breezeway-task-payroll-export.csv`\>_ —
> so `sourceMatch` should be a regex like `/payroll-export/i`.
> **Columns** (logical field → the exact header text as it appears in the file;
> list every acceptable variant, first match wins):
> - _\<field\>_ → _\<"Header A", "Header B"\>_
> - _\<field\>_ → _\<"Header C"\>_
> - …
> **Normalization rules:** _\<e.g. strip `· config` suffix from property; parse
> hours as a number; dates → YYYY-MM-DD; drop rows with no cleaner\>_
> **Output on the page** (in order): _\<describe each table / card section, its
> subheading counts, any footer line, and which sections sit side-by-side\>_
> **Excel downloads:** _\<button label → filename, and which columns each sheet has\>_
>
> Reuse the shared helpers (`esc`, `t`, `stripConfig`, `extractDate`, `parseQty`,
> `downloadSheet`). Match the existing visual style. The amber accent is reserved
> for "to buy" / shopping-style output only. Escape all user text. Then test it
> with a small synthetic CSV and show me the result.

---

## What Claude (or you) actually edits

One object in the `REPORTS` array in `index.html`:

```js
{
  id: "payroll-weekly",
  label: "Payroll (weekly)",
  sourceMatch: /payroll-export/i,
  sourceHint: "breezeway-task-payroll-export.csv",
  columns: {
    cleaner:  ["Completed by", "Assignees"],
    property: ["Property"],
    date:     ["Completed date", "Due date"],
    hours:    ["Hours", "Duration"],
    rate:     ["Rate", "Pay rate"]
  },
  render: function(rows){
    // 1. normalize: rows are objects keyed by the logical fields above, RAW values
    // 2. aggregate
    // 3. build a DOM element (tables / cards), wire up download buttons
    // 4. return the element
  }
}
```

### Shared helpers

| Helper | Does |
|---|---|
| `esc(v)` | HTML-escape before inserting into markup — **always** for user data |
| `t(v)` | Trim to string (null-safe) |
| `stripConfig(v)` | `"Beach House · Standard clean"` → `"Beach House"` |
| `extractDate(v)` | Date object **or** string → `"YYYY-MM-DD"` (`""` if none) |
| `parseQty(v)` | Strip non-numeric chars → number (`0` if none) |
| `downloadSheet(aoa, cols, sheetName, fileName)` | Build a one-sheet `.xlsx` and trigger download. `aoa` = array of rows; `cols` = `[{wch:30}, …]` widths |

### Don't reintroduce these bugs

- Read CSV as UTF-8 text (`type:"string"`), not bytes — otherwise accented /
  bilingual names mangle.
- `extractDate` uses UTC getters so dates don't slip a day.
- It's `XLSX.writeFile`, not `XLSX.utils.writeFile`.

---

## Test → deploy

1. **Test locally:** open `index.html` in a browser. Make a synthetic CSV named to
   match your `sourceMatch`, drop it in, pick the new report, verify every table,
   count, and download.
2. **Update `Handoff.md`** with what changed.
3. **Commit and push to `main`:**
   ```bash
   git add -A && git commit -m "Add <report name> report" && git push
   ```
4. GitHub Pages republishes automatically in ~1 minute at
   https://talodeveloper.github.io/breezeway-supply-report/ — hard-refresh to see it.
