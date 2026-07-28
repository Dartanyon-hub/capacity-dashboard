# capacity-dashboard

Team capacity planning dashboard for Notion embed.

A rolling two-week view. Three static files, no build step, no backend, no scheduled job.

- **Dashboard:** https://dartanyon-hub.github.io/capacity-dashboard/
- **Admin:** https://dartanyon-hub.github.io/capacity-dashboard/admin.html

## How it works

| File | Role |
|---|---|
| `data.json` | The only source of truth. Ten weekday entries plus a timestamp. |
| `index.html` | The embed. Fetches `data.json` on load and renders the cards. |
| `admin.html` | The editor. Runs entirely in your browser; exports a new `data.json`. |

The admin page holds no credentials and writes nothing on its own. You publish by committing the JSON it hands you.

## Weekly update

1. Open the **admin page**.
2. Days carried forward from last week are outlined in amber and tagged **Draft**. Edit or click through each one — touching a field clears the flag.
3. Hit **Copy JSON**.
4. Open [`data.json`](https://github.com/Dartanyon-hub/capacity-dashboard/blob/main/data.json) on GitHub → pencil icon → select all → paste → **Commit changes**.
5. Wait ~60 seconds for Pages to rebuild, then refresh Notion.

**Download data.json** is there if you'd rather commit the file through a local clone. The paste route is faster.

## Rolling window

Dates are computed, never typed. The window is always the current Monday–Friday plus the following Monday–Friday. On Saturday and Sunday it rolls forward, so the weekend already shows you the week ahead.

When the window advances, last week's entries drop off and the incoming week is pre-filled from the week before it as an editable draft. You're trimming a starting point rather than facing a blank grid.

If `data.json` falls more than a week behind, there's nothing adjacent to carry forward and the window starts blank. The admin page says so when that happens.

## Fields

- **AM Focus / PM Focus** — free text. Leave blank to render as `—`.
- **Commitments** — any of: Standup, 1:1, Team Sync, Show Planning, Retro, All Hands, Focus Block. Stored in that order regardless of click order.
- **Capacity** — 1 to 5, or unset. Drives the card's color and badge.
  - 1 At the Wall · 2 Tight · 3 Steady · 4 Room to Breathe · 5 Wide Open
  - Unset renders the card with no badge. A day marked `Off` with no capacity is intentional and displays fine.

A day with no AM, no PM, and no capacity renders dimmed as "Not yet planned".

## Notion embed

Paste the dashboard URL into Notion and choose **Embed**. Notion embeds are fixed-height iframes, so the layout compacts as you shrink it — below roughly 420px tall the commitment tags and timestamp drop away to keep the cards readable. Around 560–620px shows everything comfortably.

The background is transparent so it sits flush on the Notion page. Theme follows the viewer's OS setting; append `?theme=dark` or `?theme=light` to the embed URL to pin it.

Today's card gets a ring around it.

## Local development

`index.html` and `admin.html` both fetch `data.json`, which browsers block over `file://`. Serve the folder instead:

```
python3 -m http.server
```

Then open `http://localhost:8000`. If you do open the admin page straight from disk, use **Load JSON…** to paste your data in by hand — everything else works normally.

## History

Data used to live in a Craft collection, pulled weekly by a Cowork scheduled task that rewrote an inlined data block in `index.html` and pushed the commit. Craft was removed from the stack in July 2026; the scheduled task and its skill were deleted at the same time. This version replaces that whole pipeline with the admin page.
