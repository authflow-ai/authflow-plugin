---
name: publish
description: Verify and publish your paid or free MCP on Authflow, or answer whether it is ready to go live, with Authflow's publish_mcp workflow, then test it as a consumer through its gateway URL, including paid checkout. Use when the user asks to publish, go live, or whether an MCP is ready to go live, or wants to confirm a published MCP works end to end. Never publishes without the user's explicit approval.
---

# Verify, publish, and test your MCP

## 1. Connect and run the workflow

Connect the Authflow MCP (`authflow`) as the `onboard` skill describes, then call `authflow_list_workspaces`. `slug` is required: take it from the user, or from `authflow_list_resources` when the workspace has one MCP. Call `authflow_get_prompt` with `{"name": "publish_mcp", "arguments": {"slug": "<slug>"}}` and follow the returned steps.

## 2. Rules that hold whatever the workflow says

- Run `authflow_doctor` with the `slug` and `depth` `full` first, and never publish while a blocking check fails (the `doctor` skill).
- Publishing needs a passing verification less than 24 hours old; verify again if it is older.
- Before `authflow_publish_resource`, summarize what goes live (name, paid or free, price and credits, metering, gateway URL), ask, and wait for an explicit yes in this conversation. A question such as "is it ready?" is not a yes.
- `409 resource_not_ready` means requirements are still pending: show `pending_requirements` exactly as returned, and do not retry.
- Share only the `gateway_url` with consumers, never the origin URL.
- Never ask for a key or token in chat.

## 3. Test as a consumer

After publishing:

1. Run `authflow_doctor` with the `slug` and `client` set to the client the test will use (`claude`, `chatgpt`, `codex`, `cursor`, or `all`), and fix what it reports first.
2. Add the `gateway_url` as a remote MCP server in a separate client or profile, ideally signed in as someone other than the creator. It gets a 401 and starts sign-in.
3. Paid: sign-in shows Stripe Checkout, which the person testing completes in the browser themselves (with one of Stripe's published test cards on a sandbox account). Never enter payment details for them. Free: sign-in completes with no paywall and the tools list loads.
4. Call a few tools, then check `authflow_get_usage` (each paid call at its configured credits) and `authflow_get_earnings` (keep currencies separate; when `history_complete` is false, do not call the totals all-time).

A `403 invalid_origin_identity` means the client is pointed at the origin, not the gateway URL. A 404 on the gateway URL means the MCP is a draft or disabled. If sign-in fails although the doctor's `clients` checks pass, report it once as `client_compat` with the `feedback` skill, naming the client and its version.
