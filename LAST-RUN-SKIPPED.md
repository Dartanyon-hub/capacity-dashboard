# Capacity Dashboard Refresh — Skipped

**Run:** Monday, April 13, 2026
**Status:** Skipped — Craft MCP not connected

## What happened

The scheduled task fired on schedule (Monday). Date check passed.

Data freshness check could not run because the Craft MCP is not available in this session. The task requires `collectionItems_get` to read collection `738dcea8-2583-add2-5863-d7f5f5b640e6`, and the write-back step requires Craft's `blocks_add` for the daily note. Neither tool is exposed.

No git clone, no HTML rewrite, no commit, no push. Nothing touched.

## What to do

One of:

1. Reconnect Craft MCP in Cowork settings, then trigger `/schedule run capacity-dashboard-refresh` manually.
2. If Craft MCP is gone for good, update the scheduled task to read from a different source (exported JSON in Workshop, Obsidian frontmatter, etc.) and adjust the write-back to use a tool that's actually available (Obsidian daily note via `obsidian-mcp-pro`, or Apple Notes).

## Tools I checked for

- `mcp__craft__*` — not found
- `collectionItems_get` — not found
- `blocks_add` — not found

## Tools that WERE available (for reference, if rewriting)

- Desktop Commander (git ops, file ops) ✓
- GitHub MCP (direct repo push without cloning) ✓
- Obsidian MCP Pro (daily note alternative) ✓
- Apple Notes ✓
- Todoist ✓
