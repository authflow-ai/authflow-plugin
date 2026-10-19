---
name: configure-tools
description: Set what each tool of your MCP on Authflow costs in credits per call, who can call it, and per-user limits, with Authflow's configure_tools workflow - review, save in test mode, dry-run, and enforce only after approval. Use when the user wants to change a tool's credits, make tools free or subscriber-only, add rate limits, see refused calls or the settings history, or undo a tool settings change.
---

# Configure tool access, credits, and limits

## 1. Connect and run the workflow

Connect the Authflow MCP (`authflow`) as the `onboard` skill describes, then call `authflow_list_workspaces`. `slug` is required: take it from the user or from `authflow_list_resources`. Call `authflow_get_prompt` with `{"name": "configure_tools", "arguments": {"slug": "<slug>"}}` and follow the returned steps.

## 2. Rules that hold whatever the workflow says

- Read the settings with `authflow_get_resource_tools` first, and change only what the user asked for.
- Save with `authflow_update_resource_tools` in mode `test`, then dry-run each changed tool with `authflow_test_resource_tool_call`, as a subscriber and as a non-subscriber, before enforcing. Pass the saved `version`: without it the dry run evaluates the enforced version. `authflow_get_resource_tools_activity` lists the last 30 days of refused calls, including what a version under test would have refused.
- Enforce only after the user explicitly approves: `authflow_enforce_resource_tools_test` for the version under test, or `authflow_update_resource_tools` in mode `enforce`. Enforced settings reach consumers within 30 seconds, without verifying or republishing. `authflow_stop_resource_tools_test` ends a test and leaves the enforced version as it is.
- To undo a change, find the earlier version with `authflow_list_resource_tools_versions` and enforce it again with `authflow_restore_resource_tools_version`.
- `rules_apply` `false` means a paid `origin_reported` MCP: it keeps the default, and its origin reports usage itself.
- The monthly price, credits per period, and metering mode are the MCP's offer, not tool settings. Changing them takes the MCP out of live: use `authflow_update_resource`, then the `publish` skill.
- Never ask for a key or token in chat.
