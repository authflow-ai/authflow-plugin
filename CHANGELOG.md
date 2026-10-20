# Changelog

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
