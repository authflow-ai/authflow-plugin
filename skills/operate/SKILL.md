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
- Price, credits per period, metering, or origin: changing any of them takes the MCP out of live until it is verified and published again. Say so before calling `authflow_update_resource`, then use the `publish` skill.
- Hostname: a live MCP can move to the user's own hostname, or back to its Authflow address, without going out of live and without losing subscribers, credits, or installed connectors. `authflow_set_custom_domain` adds the hostname as a new address; give the user the DNS records in `custom_domain`, check with `authflow_refresh_domain`, and once it is `ready` the new address is the `gateway_url` while every earlier address keeps serving. `authflow_use_authflow_address` moves back to the Authflow address. An MCP holds at most 3 addresses over its life, and a hostname that has ever been its address stays its own, so confirm a new hostname with the user before setting it. Before the first publish, a hostname change still needs a new verification.
- If `authflow_get_resource` shows `primary_serving: false` on a live MCP, its primary hostname's DNS or certificate has lapsed: the MCP keeps serving at its other addresses and `gateway_url` names one of them meanwhile. Run the `doctor` skill for the fix; the primary serves again once its records are back and `authflow_refresh_domain` sees them.
- Disable: `authflow_disable_resource` stops serving the MCP to every consumer. State the impact, and call it only after the user explicitly confirms.
- After an error or an interrupted write, read `authflow_get_resource` before trying again. Never ask for a key or token in chat.

When an Authflow error, doc, or missing tool got in the way, report it once with the `feedback` skill, then continue.
