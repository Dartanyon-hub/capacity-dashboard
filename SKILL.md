---
name: capacity-dashboard-refresh
description: Weekly refresh of the Capacity Planning Dashboard — reads Craft collection, updates HTML, pushes to GitHub Pages.
---

You are running a scheduled task to refresh the Capacity Planning Dashboard. This task reads data from a Craft collection and pushes an updated HTML file to GitHub Pages.

Before doing anything, run these two checks:

Check 1 — Date in scope: Read the Craft collection (collection block ID: `738dcea8-2583-add2-5863-d7f5f5b640e6`, format: json, maxDepth: 0). Today's date should fall within the date range covered by the capacity matrix. If today is outside that range, stop and write a note to Dartanyon's Obsidian daily note: "Capacity refresh fired on [today's date] but the matrix covers [earliest] – [latest]. Skipping. Check /schedule timing." Do not proceed.

Check 2 — Data freshness: From the same collection read, confirm today's date is within the matrix's date range (i.e. today is not past the latest date in the matrix). If stale, stop and write a note to Dartanyon's Obsidian daily note: "Capacity refresh skipped — Craft matrix hasn't been updated. Dates found: [earliest] – [latest]. Update the collection and trigger a manual refresh." Do not proceed.

You can call out things that look like problems in the final success note (missing capacity values, sparse content, weird dates), but don't stop the run unless freshness fails.

If both checks pass, execute the refresh pipeline. Work directly in `~/Workshop/Projects/capacity-dashboard/` on Dartanyon's machine via Desktop Commander (start_process). The repo is already cloned there with gh credentials available — do not clone to /tmp or work from a sandbox.

1. `git pull` to make sure the local clone is current.
2. Parse each Craft row into this JSON shape:
```json
{"day":"Monday","date":"2026-03-23","am":"Focus text","pm":"Focus text","commitments":["Tag1","Tag2"],"cap":3}
```
* `day` = the content/title field
* `date` = the date property (ISO YYYY-MM-DD)
* `am` = am_focus property (empty string if missing)
* `pm` = pm_focus property (empty string if missing)
* `commitments` = commitments property (array of strings, empty array if none)
* `cap` = parse the leading digit from the capacity property (e.g. "3 — Steady" → 3). Null if missing.
3. Sort all 10 entries by date ascending.
4. Generate a timestamp in PDT (America/Los_Angeles): format `"Mar 23, 2026 at 06:19 PM PDT"`
5. In `~/Workshop/Projects/capacity-dashboard/index.html`, replace the data block between `// ── DATA BLOCK` and `// ── END DATA BLOCK` markers. The replacement should contain:
   * The `const CAPACITY_DATA = [...]` array with all 10 entries
   * The `const GENERATED_AT = "..."` timestamp string
   * Keep the comment lines (DATA BLOCK header and its two description lines) intact above the data.
6. Commit with message `"Weekly refresh: [Mon date] – [Fri date of week 2]"` and push to `main`.
7. Write a 1-line note to Dartanyon's Obsidian daily note: "Capacity dashboard refreshed — [date range]. Live at dartanyon-hub.github.io/capacity-dashboard/"
   * If today's note doesn't exist, use `create_daily_note` with the line as initial content. Otherwise use `append_to_note`.

Tools you'll need: Craft MCP (collectionItems_get), Desktop Commander (start_process for git pull/commit/push and file operations), Obsidian MCP (get_daily_note → create_daily_note if missing → append_to_note).
