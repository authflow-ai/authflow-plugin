---
name: onboard
description: Put an MCP server on Authflow and charge for it with a paid monthly subscription and credits, or offer free sign-in, by running Authflow's setup_mcp workflow to create the MCP, integrate the origin, connect Stripe, verify, and set tool access and credits. Use when the user wants a paywall, subscriptions, credits, usage billing or sign-in for their MCP server, needs the origin key of a paid MCP, or wants to resume an Authflow setup that is partly done.
---

# Put an MCP server on Authflow

Authflow sits in front of the user's MCP server (the **origin**). Consumers connect to an Authflow **gateway URL**, sign in once, subscribe if the MCP is paid, and the gateway forwards each call to the origin with a signed identity header. The setup steps live on the Authflow MCP as the `setup_mcp` workflow, so they stay current without a plugin update. This skill fetches that workflow and follows it.

Use **Authflow MCP** only for Authflow's own connection. Call the user's server **your MCP**, **your MCP on Authflow**, or its actual name when speaking to the user; use **my MCP** in prompts written from their perspective. Keep connection keys, tool names, URLs, and other identifiers unchanged.

## 1. Connect

If no `authflow_*` tools are available, the Authflow MCP (`authflow`) is not connected or not authorized yet. Tell the user to authorize it in their client (Claude Code: `/mcp`, choose `authflow`, authenticate. Gemini CLI: `/mcp auth authflow`. Codex and Cursor: open the MCP settings and sign in to `authflow`). Sign-in uses Google or GitHub in the browser; on first connection they choose **Create my workspace and connect**. Do not continue without these tools.

Then call `authflow_list_workspaces` and use the `workspace_id` it returns everywhere. Its `portal_url` is the user's page for that workspace in the Authflow portal, where they sign in for the browser steps and can follow your progress. If the result has a `client_notice`, tell the user once, in one sentence, and run an update command only if they ask.

## 2. Run the setup workflow

Call `authflow_get_prompt` with `{"name": "setup_mcp", "arguments": {"offer": "<paid or free>", "framework": "<proxy, typescript or python>"}}`:

- `offer`: `paid` when the user wants to charge (a subscription, credits, a paywall, usage billing), `free` when they asked for free access. Otherwise leave it out; when the workflow asks, offer paid first.
- `framework`: `proxy` (the `authflow-origin-proxy` image in front of an unchanged server, in any language), `typescript` or `python`, only when the user named it or the repository makes it plain. A .NET origin, or any language without its own plan, uses `proxy`; Authflow refuses any other value. Otherwise leave it out and the workflow asks.

The result is the workflow's text. Follow its steps in order. They name each tool and say when to stop for the user's decision or browser. If it returns an error, its `detail` names the argument that was missing or refused and what it accepts: correct it and call again. If it still fails, tell the user, then follow the Authflow MCP's own instructions and tool descriptions. For detail, use `authflow_search_docs` and `authflow_get_doc`; never guess package names.

## 3. Rules that hold whatever the workflow says

- Never ask for, accept, print, or store an admin key, origin key, OAuth token, or Stripe secret in chat, in a tool argument, or in a committed file.
- Call `authflow_list_resources` before `authflow_create_resource`. If the MCP already exists, resume it from `authflow_get_resource` instead of creating another.
- Report `status`, `pending_requirements`, and `allowed_actions` exactly as Authflow returns them.
- Only a paid MCP with `metering_mode` `origin_reported` has an origin key, and it reaches the origin only through `authflow_origin_key_action`: give the user its link to the MCP's origin key page in the Authflow portal. There they sign in and confirm a rotation, which revokes the previous key at once, and copy the new key, shown once, into their secret store themselves; opening the link changes nothing. `authflow_integration_plan`'s `secrets` says where the origin reads it. When they are back, read the MCP again with `authflow_get_resource`. Free and gateway-metered MCPs need no key: `authflow_origin_key_action` refuses them with `origin_key_not_required`, so carry on without a key step.
- Publish only after the user explicitly says yes; the `publish` skill covers it.
- Give consumers only the `gateway_url`, never the origin URL.

## 4. Elsewhere

When something fails or a requirement will not clear, use the `doctor` skill. Tool access and credits per call belong to the `configure-tools` skill. When an Authflow error, doc, or missing tool made you guess or work around something, report it once with the `feedback` skill, then continue.
