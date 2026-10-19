# Authflow plugin

Put your MCP server on [Authflow](https://authflow.ai) and charge for it. Consumers get one OAuth sign-in and a paid Stripe subscription with credits, or free access. Your coding agent does the setup.

The plugin connects the **Authflow MCP** to your AI assistant so you can set up and manage **your MCP**. The Authflow MCP is for you and your assistant; your MCP is the server your customers connect to. The plugin also adds skills for setup and everyday tasks:

| MCP server | URL | Auth |
| --- | --- | --- |
| Authflow MCP (`authflow`) | `https://rails.authflow.ai/management/mcp` | Sign in with Google or GitHub in the browser |

The steps live on Authflow, not in the plugin. Each skill connects the Authflow MCP (`authflow`), reads Authflow's matching workflow with `authflow_get_prompt`, and follows it, so the steps stay current without a plugin update. The skills keep only the rules that must hold whatever the workflow says.

| Skill | Authflow workflow | What it does |
| --- | --- | --- |
| `onboard` | `setup_mcp` | Create a paid or free MCP, integrate your server, connect Stripe, verify, and set tool credits |
| `publish` | `publish_mcp` | Verify, publish once you say yes, and test through the gateway URL like a real consumer |
| `doctor` | `diagnose_mcp` | Run Authflow's hosted health check, fix what it finds, and explain what is left |
| `configure-tools` | `configure_tools` | Each tool's credits per call, who can call it, and per-user limits, tested before they are enforced |
| `operate` | `earnings_report` | Usage and earnings, renaming, offer changes, disabling, health sweeps |
| `feedback` | `authflow_send_feedback` | Report a bug, docs gap, missing tool, or your own message to the Authflow team |

## Install

Turn on auto-update where your client offers it, so skill fixes reach you without asking. [Updating](#updating) covers updating by hand.

### Claude Code

```text
/plugin marketplace add authflow-ai/authflow-plugin
/plugin install authflow@authflow
```

Then run `/mcp`, select `authflow`, and authenticate.

Turn on auto-update with `/plugin`, **Marketplaces**, `authflow`, **Enable auto-update**. Or declare the marketplace in `~/.claude/settings.json` with auto-update on:

```json
{
  "extraKnownMarketplaces": {
    "authflow": {
      "source": { "source": "github", "repo": "authflow-ai/authflow-plugin" },
      "autoUpdate": true
    }
  }
}
```

### Codex

```bash
codex plugin marketplace add authflow-ai/authflow-plugin
```

Then install `authflow` from `/plugins`, and sign in to the Authflow MCP (`authflow`) when prompted.

### Gemini CLI

```bash
gemini extensions install https://github.com/authflow-ai/authflow-plugin --auto-update
```

Then run `/mcp auth authflow`.

### Cursor

Teams and Enterprise workspaces can import this repository as a team marketplace (**Dashboard → Plugins & MCPs → Import from Repo**). Cursor reads the root `plugin.json` and `mcp.json`.

On other plans, add the server to `~/.cursor/mcp.json` until the plugin is listed in the Cursor Marketplace:

```json
{
  "mcpServers": {
    "authflow": { "url": "https://rails.authflow.ai/management/mcp" }
  }
}
```

## Use it

Open the repository that contains your MCP server and ask:

> Put this MCP server on Authflow as a paid MCP named "My MCP" with slug `my-mcp`, $9 a month with 12 credits. Show me the result before publishing.

Ask for a free MCP instead when consumers should only sign in. The agent never needs a key or token from you. You complete sign-in, consent, and Stripe in the browser, and, for a paid MCP whose server reports its own usage, the one-time origin key reveal in the Authflow portal. Publishing always waits for your confirmation.

Ask "Is my MCP on Authflow healthy?" at any time. The `doctor` skill runs Authflow's hosted checks and tells you what to fix and who has to fix it: the agent, you (DNS, Stripe, deploys), or Authflow.

The same workflows are the Authflow MCP's prompts, which most clients also list in their `/` menu: `setup_mcp`, `publish_mcp`, `diagnose_mcp`, `configure_tools`, and `earnings_report`.

## Updating

The plugin's MCP configs send two request headers, `Authflow-Plugin-Version` and `Authflow-Plugin-Client`, so Authflow can tell users when an update exists. When your plugin is out of date, the Authflow MCP tells your agent once, and the agent passes on how to update. Updates apply in a new session.

### Claude Code

```bash
claude plugin update authflow@authflow
```

Or `/plugin`, **Installed**, `authflow`, **Update**. Then start a new session. With auto-update on, Claude Code fetches new versions itself; they still apply in a new session.

### Codex

1. Quit Codex.
2. Run `codex plugin marketplace upgrade authflow`, then `codex plugin add authflow@authflow`.
3. Start a new thread.

`codex mcp list` should then show one `authflow` row with the Authflow MCP URL.

### Gemini CLI

```bash
gemini extensions update authflow
```

Then restart Gemini CLI. Extensions installed with `--auto-update` update themselves; the new version also applies after a restart.

### Cursor

A team admin refreshes the Authflow team marketplace in the Cursor dashboard. Then reload the Cursor window. A server added by hand to `~/.cursor/mcp.json` has no plugin to update.

## Feedback

When an Authflow error, doc, or missing tool makes the agent guess or work around something, or you ask it to tell Authflow something, the `feedback` skill sends one report with `authflow_send_feedback` and gives you its `feedback_id`. Reports go to the Authflow team, never include keys, tokens, or anyone's personal data, and are attributed to your workspace. The agent asks you before a report includes your code, configuration, URLs, or words. Nobody replies.

Reports need the Authflow MCP (`authflow`) connected. If signing in is what fails, the agent tells you exactly what failed and sends the report once you are connected.

## Docs

The Authflow MCP serves Authflow's documentation to signed-in agents (`authflow_search_docs`, `authflow_get_doc`, the `authflow://docs/...` resources, and the resource-specific `authflow_integration_plan`). Once you are signed in, ask your agent for any page, for example the onboarding guide, Origin Protocol v1, the origin proxy, or the doctor's checks.

## License

Apache-2.0
