# Flight Log — project notes for Claude

Public repo. Keep this file free of personal details, names, booking codes,
IDs, and anything secret. Private notes live in the private notes repo.

## What it is
- Page: https://twoseam.github.io/flight-log/ (GitHub Pages, this repo).
- Data flow: Gmail → Apps Script (daily trigger, ~6am) → Google Sheet `trips` tab
  → page fetches the Sheet live (gviz JSON).
- The Sheet is the source of truth. `TRIPS_FALLBACK` in `index.html` is only used
  if the live fetch fails. Not merged with live data.
- Mileage is computed in the page (haversine from `AIRPORTS` lat/lon). The Sheet
  stores no miles.
- Dedup: the script skips confirmation codes already in the Sheet (column F).

## Apps Script
- Lives in Google, not in this repo. Local copy via `clasp` (file is `Code.js`).
- Publish: `clasp push -f`. Refresh: `clasp pull`.
- After a push, close and reopen the editor tab. Cmd+R alone doesn't refresh
  the Run dropdown.

## When Michael says "not updating"
- FIRST check the Apps Script Executions page for 0 runs. The trigger has
  vanished before. Re-add: Day timer, 6–7am, function `scanGmail`.
- Then debug code, not before.

## New destination not showing
- Add the airport to `AIRPORTS` in `index.html`.
- Add the city to `CITY_TO_AIRPORT` in the script.
- Unknown codes show a red notice (`#unknownNote`) instead of crashing.

## Gotchas (already hit — don't re-debug)
- Apps Script functions ending in `_` don't appear in the Run dropdown.
- Sheets mangles some text on `setValues()` (en-dashes became blank). Columns
  B/C are forced to plain text in `getOrCreateSheet_()`.
- gviz date cells: read `.f` (formatted), not `.v`. `rowsToTrips()` handles it.
- `clasp run` can't run functions needing Gmail scope. Use the editor's
  Run ▶ button (not Debug).
- `clasp` needs periodic re-login (`invalid_grant`). Michael runs `clasp login`.
- Mac shell is zsh. `read -s -p` fails; use `read -s "VAR?prompt"`.
- Total miles drift ~20 from the old hand count. Expected.

## Working rules
- Don't re-architect. It's built to be zero-maintenance.
- "Hands off" means minimize Michael's clicks: front-load one-time consent
  steps, then don't come back for routine things.
- Commit and push before ending any session.
