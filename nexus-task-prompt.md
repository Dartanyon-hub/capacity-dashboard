# Capacity Dashboard Refresh — Nexus Scheduled Task

## What This Does
Reads the "Weekly Capacity" collection from Craft, generates the capacity dashboard HTML, and pushes it to GitHub Pages.

## Craft Source
- **Collection Block ID:** `738dcea8-2583-add2-5863-d7f5f5b640e6`
- **Page ID:** `af3f4355-523b-4a14-c045-69efcbf8d0f7`
- **Schema:** Day (content/title), Date, AM Focus, PM Focus, Commitments (multiSelect), Capacity (singleSelect)
- **Capacity values:** "1 — At the Wall", "2 — Tight", "3 — Steady", "4 — Room to Breathe", "5 — Wide Open"

## GitHub Target
- **Repo:** `https://github.com/Dartanyon-hub/capacity-dashboard`
- **Live URL:** `https://dartanyon-hub.github.io/capacity-dashboard/`
- **File:** `index.html` in root of repo

## Steps

1. **Read Craft collection** using `collectionItems_get` with collectionBlockId `738dcea8-2583-add2-5863-d7f5f5b640e6`.

2. **Parse each item** into this JSON shape:
   ```json
   {
     "day": "Monday",
     "date": "2026-03-16",
     "am": "Gravity Admin",
     "pm": "Esports Admin",
     "commitments": ["1:1", "Focus Block"],
     "cap": 3
   }
   ```
   - Extract the integer from capacity strings: `"3 — Steady"` → `3`. If empty/missing, use `null`.
   - Sort by date ascending.
   - Take the first 10 entries (2 weeks × 5 weekdays).

3. **Clone or pull the repo:**
   ```bash
   cd /tmp
   if [ -d capacity-dashboard ]; then
     cd capacity-dashboard && git pull
   else
     git clone https://github.com/Dartanyon-hub/capacity-dashboard.git
     cd capacity-dashboard
   fi
   ```

4. **Read the current `index.html`** from the repo clone.

5. **Replace the DATA BLOCK** — everything between `// ── DATA BLOCK` and `// ── END DATA BLOCK` markers:
   - Replace the `CAPACITY_DATA` array with the parsed JSON.
   - Replace the `GENERATED_AT` string with the current timestamp in format: `"Mar 13, 2026 at 6:02 AM PDT"` (Pacific Daylight Time). Use America/Los_Angeles timezone.

6. **Write the updated `index.html`** back to the repo clone.

7. **Commit and push:**
   ```bash
   git add index.html
   git commit -m "refresh: $(date +%Y-%m-%d)"
   git push origin main
   ```

8. **Confirm** by logging the live URL: `https://dartanyon-hub.github.io/capacity-dashboard/`

## Schedule
Run Sunday evenings (so dashboard is fresh Monday morning). Can also be triggered manually anytime.

## Error Handling
- If Craft collection is empty or has fewer than 5 items, log a warning but still generate with what's available (pad remaining slots with null-capacity empty cards).
- If git push fails, log the error. Do not retry automatically.
- If the DATA BLOCK markers aren't found in index.html, abort and log — means the HTML template was modified in an unexpected way.
