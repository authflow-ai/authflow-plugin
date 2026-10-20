# Authflow plugin

Give your MCP server sign-in, paid access, or access just for you with [Authflow](https://authflow.ai). Install the plugin, tell your chat or coding agent what you want, and let it guide the setup.

The plugin connects the **Authflow MCP** and adds workflow skills. You and your assistant use the Authflow MCP to set up and manage **your MCP**; your customers connect to your MCP.

## Install Authflow

Paste this into your chat or coding agent:

> Install the Authflow plugin from https://github.com/authflow-ai/authflow-plugin and help me charge for access to my MCP. Handle the setup and guide me through anything I need to do. Show me the result before publishing.

Change the goal to offer your MCP free to anyone signed in, keep it just for your account, or build a new MCP from an idea.

Your agent handles installation and guides you through sign-in. If your app needs a confirmation or a new chat, follow the agent’s next step.

<details>
<summary>Installation details for agents and manual setup</summary>

### For the agent installing this plugin

1. Use your current app and available installation controls to choose the matching section below. Reuse an installed Authflow plugin; do not add duplicate connections.
2. Handle source registration and plugin installation for the user. Some apps call a plugin source a “marketplace”; registering this source only connects the app to Authflow and does not publish anything. Source registration alone does not install the plugin.
3. Help the user complete Authflow sign-in in their browser. Confirm the connection with `authflow_list_workspaces`; a saved configuration alone does not confirm it is ready.
4. If a reload or new chat is required, give the user the exact next step and a short continuation prompt containing this repository URL and their original goal.
5. Once connected, use the plugin’s onboarding skill and follow the workflow it retrieves. Let that workflow guide the setup and ask for decisions when needed.

If the current app cannot install plugins, use its supported connection route below or explain the next step in a supported app. Some chats cannot edit or deploy a project; guide that handoff when needed. Never ask the user to paste credentials into chat.

Use the instructions for the current app and version. Turn on auto-update where supported.

### Codex

Connect Codex to the Authflow plugin source from a terminal:

```bash
codex plugin marketplace add authflow-ai/authflow-plugin
```

Then install the plugin. If `codex plugin --help` lists `add`, run:

```bash
codex plugin add authflow@authflow
```

Otherwise open `/plugins`, select Authflow, and install it. Confirm that the plugin is installed, not just listed as an available source.

When available, `codex plugin list --marketplace authflow --json` can confirm installation. Complete the Authflow sign-in prompt. Start a new chat if the current chat does not expose the plugin’s tools, then continue the original goal.

If the CLI is needed but missing, follow the [Codex CLI installation instructions](https://developers.openai.com/codex/cli). [Plugin and marketplace documentation](https://developers.openai.com/plugins/build/plugins).

### Claude Code

In Claude Code 2.1.275 or later, install Authflow with:

```text
/plugin install authflow --marketplace https://github.com/authflow-ai/authflow-plugin
```

The command connects the source and opens the installation details. Confirm the install scope when prompted.

From a terminal, Claude Code 2.1.292 or later supports:

```bash
claude plugin install authflow --marketplace https://github.com/authflow-ai/authflow-plugin
```

On older versions, use these two commands inside Claude Code:

```text
/plugin marketplace add authflow-ai/authflow-plugin
/plugin install authflow@authflow
```

Or from a terminal:

```bash
claude plugin marketplace add authflow-ai/authflow-plugin
claude plugin install authflow@authflow
```

Follow the install result: if the plugin is not active yet, use `/reload-plugins` when supported or start a new session. Run `/mcp`, select `authflow`, and authenticate.

Enable auto-update through `/plugin` → **Marketplaces** → **authflow** → **Enable auto-update**. [Claude Code installation documentation](https://code.claude.com/docs/en/discover-plugins#add-a-marketplace-and-install-in-one-command).

### Gemini CLI

Run this from a terminal, outside the Gemini CLI conversation:

```bash
gemini extensions install https://github.com/authflow-ai/authflow-plugin --auto-update
```

Restart Gemini CLI so the extension loads. Then run `/mcp auth authflow` and complete browser sign-in. Continue with the goal you selected before installation. [Gemini extension documentation](https://geminicli.com/docs/extensions/reference/).

### Cursor

Teams and Enterprise workspaces can import this repository as a team marketplace through **Dashboard → Plugins & MCPs → Import from Repo**. Use `https://github.com/authflow-ai/authflow-plugin`, then install Authflow from the available plugins. An administrator may need to import it. Cursor reads the root `plugin.json` and `mcp.json`.

If the plugin is unavailable in your workspace, you can connect the Authflow MCP through Cursor’s MCP settings. Merge this entry into the existing `mcpServers` object in `~/.cursor/mcp.json`; preserve other connections:

```json
{
  "mcpServers": {
    "authflow": { "url": "https://rails.authflow.ai/management/mcp" }
  }
}
```

This fallback connects the MCP without installing the plugin’s skills. Complete sign-in in Cursor’s MCP settings. The agent can then request the hosted onboarding workflow directly with `authflow_get_prompt`, using `name: "setup_mcp"`, and follow the returned instructions for your goal. [Cursor plugin documentation](https://cursor.com/docs/plugins).

### Other chats and agent apps

Use your app’s supported plugin installer when it accepts this repository. If it supports remote MCP connections instead, add `https://rails.authflow.ai/management/mcp` and complete browser sign-in.

An MCP-only connection does not install the plugin’s skills. Connected agents can use the hosted workflows through `authflow_get_prompt`, beginning with `setup_mcp`. If the app lacks plugin or remote MCP support, continue in a supported app.

</details>

## Use it

Open your MCP’s project if you have one and tell your agent what you want:

- “Help me charge for access to my MCP.”
- “Help me share my MCP for free with anyone who signs in.”
- “Help me make my MCP free and available only to my account.”
- “Help me build an MCP from an idea. Start by asking what I want it to do.”

The plugin’s onboarding workflow guides the details. You complete sign-in and any Stripe steps in your browser. Publishing waits for your confirmation.

You host and run your own MCP server. If you are starting from an idea, your agent helps you build it and choose where it runs.

Ask “Is my MCP healthy?” whenever you need help. The doctor skill explains what needs attention and who can handle it.

## Workflows

The detailed steps live on Authflow. Each skill retrieves the current workflow with `authflow_get_prompt`, so setup instructions can stay current without a plugin update.

| Skill | Authflow workflow | What it does |
| --- | --- | --- |
| `onboard` | `setup_mcp` | Set up your MCP with free or paid access |
| `publish` | `publish_mcp` | Verify, publish after your approval, and test your MCP |
| `doctor` | `diagnose_mcp` | Check health and guide fixes |
| `configure-tools` | `configure_tools` | Set tool access, credits and limits |
| `operate` | `earnings_report` | Check usage and earnings and manage existing MCPs |
| `feedback` | `authflow_send_feedback` | Report a problem or send feedback to Authflow |

## Updating

Ask your agent to update the Authflow plugin and follow any reload or sign-in instructions.

<details>
<summary>Update instructions for agents and manual setup</summary>

The plugin identifies its client and version to Authflow so your agent can tell you when an update is available.

- **Codex:** run `codex plugin marketplace upgrade authflow`, then install or refresh Authflow through `codex plugin add authflow@authflow` when supported, or `/plugins`. Start a new chat to use the updated plugin.
- **Claude Code:** run `claude plugin update authflow@authflow`, or use `/plugin` → **Installed** → **authflow** → **Update**. Follow the update result; use `/reload-plugins` or start a new session if needed.
- **Gemini CLI:** run `gemini extensions update authflow`, then restart Gemini CLI. Auto-updated extensions also load after a restart.
- **Cursor:** a team admin refreshes the marketplace, then users reload the Cursor window. An MCP-only connection has no plugin package to update.

</details>

## Feedback

When an Authflow error, doc, or missing tool makes the agent guess or work around something, or you ask it to tell Authflow something, the `feedback` skill sends one report with `authflow_send_feedback` and gives you its `feedback_id`. Reports go to the Authflow team, never include keys, tokens, or anyone's personal data, and are attributed to your workspace. The agent asks you before a report includes your code, configuration, URLs, or words. Nobody replies.

Reports need the Authflow MCP (`authflow`) connected. If signing in is what fails, the agent tells you exactly what failed and sends the report once you are connected.

## Docs

The Authflow MCP serves Authflow's documentation to signed-in agents (`authflow_search_docs`, `authflow_get_doc`, the `authflow://docs/...` resources, and the resource-specific `authflow_integration_plan`). Once you are signed in, ask your agent for any page, for example the onboarding guide, Origin Protocol v1, the origin proxy, or the doctor's checks.

## License

Apache-2.0
