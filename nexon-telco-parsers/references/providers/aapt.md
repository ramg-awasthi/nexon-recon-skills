# AAPT Provider Runtime Notes

Runtime boundary: the installed `nexon-recon parse --provider AAPT` command.

## Accepted Input

- Complete AAPT invoice ZIP packages.
- Enabled families: `rec001`, `rec004`, `rec005`, and `rec010`.
- Disabled current-scope families: `rec002` and `rec006`.
- Reference-only family: `rec012`.

## Parser Rules

- Require readable `rec001` identity/account/period data and at least one
  `rec005` primary service-charge row. Do not derive invoice identity or
  billing period from filenames or partial packages.
- Include every present `rec004`, `rec005`, and `rec010` charge row in
  parsed accounting. Account for `rec002` and `rec006` as disabled and
  `rec012` as reference-only; none of those three families may create
  matchable charge rows.
- Preserve provider account, the full service identifier, source file, and
  source row/page/sheet traceability. Do not cut an identifier at a dash or
  aggregate it before deterministic billing matching.
- Use the `rec001` billing period as the invoice billing period for every
  parsed charge row. Preserve source charge dates as audit fields when present.
- Keep `rec004` account-level adjustments and discounts in financial and
  report accounting, but exclude them from service-identifier matching. The
  compatibility service value `10000` is a report placeholder, not match
  evidence.
- Preserve individual source charge rows through billing lookup and
  deterministic matching. Refined output may aggregate only rows that already
  share one verified billing identity, and it must retain every contributing
  source-line ID.
- Preserve every `rec010` source row in parsed/raw accounting. Calculate each
  exact source service identifier's net `Charge(ex GST)` from numeric amounts; when that net
  is zero, mark all contributing rows ineligible for billing candidates and
  exclude them from refined financial output. Never use description text for
  this decision.
- Do not add guessed column mappings or infer missing invoice rows.
