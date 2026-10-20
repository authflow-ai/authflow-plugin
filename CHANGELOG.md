# Changelog

## 0.6.2

- A live MCP can move to your own hostname, or back to its Authflow address, without going out of live and without losing subscribers, credits or installed connectors: the `operate` skill no longer says a hostname is fixed after the first publish, and names `authflow_use_authflow_address` for moving back.
- Explain `primary_serving: false`: a lapsed primary hostname stops only itself, and the MCP keeps serving at its other addresses meanwhile.
- Requires an Authflow release that includes resource addresses (ADR-014).
- Manifests: version 0.6.2.

## 0.6.1

- Start installation with one prompt to your chat or coding agent. Keep client-specific setup and update commands in expandable instructions.
- Prefer Claude Code’s single install command where supported, with an older-version fallback. Explain that connecting a plugin source does not publish anything.

- Reserve **Authflow MCP** for Authflow's own connection. Describe your server as **your MCP**, **your MCP on Authflow**, or its name across the plugin descriptions, setup guidance, and skills.
- Clarify which connection you and your assistant use and which MCP your customers connect to. Technical identifiers and connection URLs are unchanged.
- Manifests: version 0.6.1.

## 0.6.0

- First official release from `authflow-ai/authflow-plugin`.
- Connects to the production Authflow MCP at `https://rails.authflow.ai/management/mcp`.
- Includes six skills for onboarding, publishing, diagnosis, tool access and credits, operations, and feedback.
- Provides shared skills with client manifests for Claude Code, Codex, Cursor, and Gemini CLI.

Earlier preview releases are superseded by this official distribution.
