---
name: operate
description: Run paid and free MCPs already on Authflow day to day - usage and earnings with Authflow's earnings_report workflow, a health sweep, renaming, offer and metering changes, and disabling. Use for questions about your live MCPs on Authflow, such as what they earned, how much they were used, or changing or turning one off.
---

# Operate live MCPs

Connect the Authflow MCP (`authflow`) as the `onboard` skill describes, then call `authflow_list_workspaces`.

## Usage and earnings

Call `authflow_get_prompt` with `{"name": "earnings_report"}` (it takes no arguments) and follow the returned steps. Keep currencies separate, and when `history_complete` is false, say the totals cover only the history shown.

## Everything else

- Health sweep: the `doctor` skill, without a slug.
- Who can call each tool, its credits per call, and per-user limits: the `configure-tools` skill.
- Rename: `authflow_update_resource` with only `display_name` keeps the MCP live; `authflow_rename_workspace` renames the workspace.
- Price, credits per period, metering, origin, or domain: changing any of them takes the MCP out of live until it is verified and published again. Say so before calling `authflow_update_resource`, then use the `publish` skill. A custom hostname cannot change after the MCP first went live.
- Disable: `authflow_disable_resource` stops serving the MCP to every consumer. State the impact, and call it only after the user explicitly confirms.
- After an error or an interrupted write, read `authflow_get_resource` before trying again. Never ask for a key or token in chat.

When an Authflow error, doc, or missing tool got in the way, report it once with the `feedback` skill, then continue.
