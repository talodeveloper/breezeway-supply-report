# CLAUDE.md — Breezeway Supply Report

## What this is

A single self-contained `index.html` — a client-side reporting tool for Breezeway
vacation-rental cleaning exports. No backend, no build step, no data storage.
[SheetJS](https://sheetjs.com/) (xlsx) is loaded from cdnjs for reading and writing
spreadsheets.

## Deploy

GitHub Pages, "deploy from branch": `main` branch, root folder. **Pushing to `main`
is the deploy** — it publishes to https://talodeveloper.github.io/breezeway-supply-report/
in ~1 minute. There is no workflow file and no build. `.nojekyll` stops Pages from
running Jekyll over the repo.

## Architecture — the `REPORTS` registry

Everything is driven by the `REPORTS` array in `index.html`. Each entry:

```js
{
  id:          "unique-key",
  label:       "Dropdown text",
  sourceMatch: /regex/i,          // uploaded file name (or a sheet name) must match
  sourceHint:  "expected-file-name.csv",
  columns: {                       // logical field -> [accepted header variants]
    property: ["Property"],        // case-insensitive, first match in the data wins
    date:     ["Completed date", "Due date"],
    // ...
  },
  render: function(rows){          // rows: objects keyed by logical field, RAW values
    // normalize + build DOM + wire download buttons
    return htmlElement;
  }
}
```

The generic engine (`collectRows`) routes files by `sourceMatch`, resolves each
logical field to a real header from the first data row, and hands raw rows to
`render()`. **Adding a report = append one object. Nothing else changes.** There is
a commented-out `payroll-weekly` template in the file showing the shape.

Shared helpers available to any `render()`: `esc`, `t` (trim), `stripConfig`
(drops `· <config>` suffix), `extractDate` (→ `YYYY-MM-DD`), `parseQty`,
`downloadSheet(aoa, cols, sheetName, fileName)`.

## Gotchas already fixed (don't reintroduce)

- **CSV must be read as UTF-8 text** — `XLSX.read(text, {type:"string", cellDates:true})`
  after `reader.readAsText()`. Reading CSV bytes as an array made SheetJS assume
  CP1252 and mangled `·` and accented / bilingual item names.
- **Date cells land at UTC midnight** — `extractDate` uses `getUTCFullYear/Month/Date`,
  not local getters, or dates slip a day in US timezones.
- **`XLSX.writeFile`, not `XLSX.utils.writeFile`** — the latter doesn't exist in the
  CDN build and throws.
- Item names are bilingual (`Toilet paper/Papel de baño`) — keep them verbatim, never split.

## Testing a change

Open `index.html` in a browser (or the Claude Code browser-preview tools). Build a
small synthetic CSV whose name matches the report's `sourceMatch`, drop it in, and
check: header resolution, normalization, the on-page tables, and that each
**Download … Excel** button produces a correct sheet.

## Session discipline

At the end of every working session: update `Handoff.md`, commit, and push to
`main` (which also deploys). Machine-death recovery is the goal — the repo alone
should be enough to resume.
