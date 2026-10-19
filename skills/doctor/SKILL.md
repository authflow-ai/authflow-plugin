---
name: doctor
description: Find and fix what keeps your paid or free MCP on Authflow from verifying, connecting, or billing, with Authflow's hosted doctor (authflow_doctor) through Authflow's diagnose_mcp workflow. Use when an Authflow tool returns an error or a pending requirement will not clear, when consumers get 403 invalid_origin_identity, after DNS, deploy, or Stripe changes, before verifying, or when the user asks whether their MCP is healthy.
---

# Diagnose with the Authflow doctor

`authflow_doctor` runs read-only checks on the workspace's MCPs and the Authflow endpoints they depend on, and says who fixes each failure. How to act on it is Authflow's `diagnose_mcp` workflow.

## 1. Connect and run the workflow

Connect the Authflow MCP (`authflow`) as the `onboard` skill describes, then call `authflow_list_workspaces`. Call `authflow_get_prompt` with `{"name": "diagnose_mcp", "arguments": {"slug": "<slug>"}}`, leaving out `slug` to check every MCP in the workspace, and follow the returned steps.

Useful `authflow_doctor` arguments: `depth` `full` adds the origin conformance probe (it needs a `slug`; run it before `authflow_verify_resource`). `groups` re-runs only what changed: `origin` after a deploy, `domain` after DNS changes, `billing` after Stripe, `resource` after verifying; the others are `rail` and `clients`. `client` (`claude`, `chatgpt`, `codex`, `cursor`, or `all`) checks that consumers of that client can sign in. On a 429, wait as long as the error says instead of calling again.

## 2. Report and act

- Report `overall`, `summary`, and each failing check's `observed` and `expected` as returned. Never call an MCP healthy unless `overall` is `healthy`.
- Act on each failure's `fix.actor`. `agent`: fix it, or call `fix.tool` with `fix.arguments`, then re-run only the affected `groups`. `owner`: give the user `fix.summary` word for word (DNS records, Stripe, deploys), with the workspace's `portal_url` from `authflow_list_workspaces` when it is a browser step, and re-run once they say it is done. `authflow`: send `fix.tool`'s ready report once with the `feedback` skill, and do not re-run in a loop.
- `fix.docs` is a docs path with an anchor. Read it with `authflow_get_doc`: the part before `#` as `path`, the anchor as `section`.
- Never publish while a check with `blocking` `true` fails, even if `allowed_actions` includes `publish`.

## 3. When the doctor does not explain it

- An error code the report does not cover, such as a `pending_requirements` code, a custom domain state, an `invalid_origin_identity` `error_code`, a usage endpoint error, or a Stripe status: call `authflow_search_docs` with the exact code and read the hit's section with `authflow_get_doc`. A search with no result is a docs gap; say so instead of guessing.
- A consumer gets `403 invalid_origin_identity`: they are calling the origin directly, and the origin is right to refuse. Give them the MCP's `gateway_url` and suggest locking origin ingress down so only the gateway reaches it. Never weaken identity verification, and never make the origin return 401.
- A paid `origin_reported` MCP's usage reports are refused as unauthorized: the owner needs a new origin key from `authflow_origin_key_action`, as the `onboard` skill describes.
- Authflow MCP tools fail with `not_authorized`: re-authorize the Authflow MCP (`authflow`) with read and write access. `invalid_scope` at sign-in is a client problem: report it as `client_compat` with the `feedback` skill, naming the client.
- Never ask for a key or token in chat.
