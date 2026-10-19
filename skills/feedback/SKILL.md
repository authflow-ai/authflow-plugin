---
name: feedback
description: Send one report to the Authflow team with authflow_send_feedback - a bug, a client compatibility failure, an unclear error, a docs gap, a missing capability, a security concern, or something the user wants passed on. Use when the user says "tell Authflow" or asks to report a bug, a feature request, or a docs problem to Authflow, and after you work around any Authflow error, documentation gap, or missing tool.
---

# Send feedback to Authflow

`authflow_send_feedback` puts a report in front of the Authflow team, who fix the tools, errors, and docs behind it. Its description carries the rules for each field; this skill adds when to send and what to do with the reply. Nobody replies, and a report never replaces telling the user what happened.

## When to send

- The user asks you to tell Authflow something, or to report a bug, a feature request, or a docs problem.
- An Authflow error says the problem is on Authflow's side, or a doctor check's `fix.actor` is `authflow`.
- An Authflow tool error, doc, or missing capability made you guess, retry with different arguments, work around it, or ask the user to do by hand what a tool should do.
- At the end of a setup, ask the user whether to send Authflow a short report on how it went, and send it with `kind` `other` only if they agree.
- The docs are missing something or contradict a tool. An `authflow_search_docs` search that finds nothing is recorded automatically, so send at most one report for it, and only to add what you were trying to do.

Send one report per distinct issue, once you know what happened, then continue. Do not report problems in the user's own code, a validation error you fixed by correcting your own arguments, or checks the owner has to fix.

## Write it

- `kind`: the first that fits, in this order: `security`, `client_compat`, `bug`, `unclear_error`, `docs_gap`, `missing_capability` (a feature the user asks for is this kind too), `user_message`, `other`. For `security`, tell the user right away as well, and never repeat an exposed value.
- `severity`: `blocked`, `worked_around`, or `minor`. Omit it for `user_message`.
- `summary` in one line of at most 200 characters, then `goal`, `details`, `tool_name`, `error_text` word for word, `request_id` copied from the Authflow error, `workaround`, `suggested_fix`, and `resource_slug` when you have them. Set `user_requested` to `true` when the user asked you to send it. Leave out client and version details; Authflow attaches them.
- Send without asking when the report only describes Authflow's tools, errors, or docs. Ask first, showing the draft, when it would include the user's code, configuration values, URLs, or words; a request to pass something on is consent, but show the draft if you reworded it. Never include keys, tokens, secrets, or anyone's personal data.

## After sending

Tell the user the `feedback_id` in one line and follow the reply's `next`. `duplicate` means Authflow already has the same report and counted it: do not send it again. Never promise a reply, a fix, or a date.

## Without the MCP

Reports go only through `authflow_send_feedback`. If the Authflow MCP (`authflow`) is not connected, help the user connect it as the `onboard` skill describes, then send the report. If sign-in itself is what fails, tell the user exactly what failed, and send one `client_compat` report once the MCP connects.
