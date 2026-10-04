# Cherami MCP

Give recurring agent work its own email address. [Cherami](https://cherami.to) provides inboxes where project updates, questions and replies can arrive from people using their usual email app. Your connected agent reads the correspondence and works on it with its own tools.

Cherami is currently free. See [allowances](https://cherami.to/pricing) and [permitted sending](https://cherami.to/docs/guides/safety#permitted-sending).

**This repository contains connection guidance and plugin files for Cherami's hosted MCP service.** The packages share one email workflow skill and connect to the same endpoint. They contain no server implementation or self-hosting setup. You do not need to install a plugin to connect through native MCP.

## Plugin packages

The plugin files are a **preview**. Package installation, OAuth and conversational skill behavior still need verification in each target host. Their presence here does not mean they are listed or approved in a platform directory.

| Host | Files read from this repository | Distribution |
| --- | --- | --- |
| Claude | `.claude-plugin/plugin.json`, `.mcp.json`, `skills/` | Git-backed plugin and personal marketplace |
| Gemini CLI | `gemini-extension.json`, `skills/` | GitHub-installed extension |
| Cursor | `plugin.json`, `mcp.json`, `skills/` | Agent Plugins format, prepared for marketplace submission |
| ChatGPT | `plugin.json`, `mcp.json`, `skills/`, `assets/` | Portable ZIP, submitted separately |

All formats reuse [`skills/cherami-email/SKILL.md`](skills/cherami-email/SKILL.md). The host-specific manifests are small adapters, not separately maintained versions of the skill. There are no executables, hooks or embedded credentials.

### Claude

For a local preview, clone this repository and launch Claude Code with `claude --plugin-dir /absolute/path/to/cherami-mcp`. The skill is available as `/cherami:cherami-email`; check `/mcp` for the connection.

For Git-backed installation, add this repository as a personal marketplace, then install its plugin:

```text
/plugin marketplace add cherami-mail/cherami-mcp
/plugin install cherami@cherami-mail
```

This is Cherami's own catalog, not an Anthropic directory listing. In hosted Claude or Cowork, bundled remote servers require connecting from the plugin's Connectors tab; Claude Code connects directly. Compatibility on one surface does not establish it on the others.

### Gemini CLI

Install the extension from this repository:

```sh
gemini extensions install https://github.com/cherami-mail/cherami-mcp
```

Restart the CLI session after installation. Use `/mcp list` to inspect the server and `/mcp auth cherami` when authentication is needed. The extension requests Cherami's mail scope through native OAuth; its browser callback and private-call authentication still need host verification. This package targets Gemini CLI, not the consumer Gemini app.

### Cursor and ChatGPT

Cursor documents support for the root Agent Plugins manifest and MCP configuration supplied here. A Cursor marketplace release still requires local verification, publisher-terms acceptance and submission. There is no Cursor install listing to link yet.

The same portable files can form a ChatGPT submission ZIP. These refreshed files are not the identity of the existing ChatGPT submission and have not been resubmitted. Platform-specific manifests for Claude and Gemini are not needed in that ZIP.

### After installing

Keep only one active Cherami server configuration in a host. A manually configured server or another plugin with the same server name can take precedence. Installing another copy is not an authentication repair.

Ask the agent to list your Cherami inboxes, complete host-managed browser authorization, and select an inbox for the assignment. The [connection guide below](#connect-your-agent) explains what that check establishes. Tell the agent which routine correspondence it may handle and when it should ask you.

## Connect your agent

| Setting | Value |
| --- | --- |
| Server URL | `https://cherami.to/mcp` |
| Transport | Streamable HTTP |
| Authentication | OAuth by default; a Claim-issued API key is also supported |
| OAuth mail scope | `cherami_mail:full` |

### Native remote MCP

Add a remote server named **Cherami** in your application's MCP settings, enter the URL above, and choose OAuth. Cherami admits compatible CIMD and dynamic-registration clients without individual registration by us.

Ask your agent:

> List my Cherami inboxes.

When your application opens the browser, sign in or create an account, review the requested permissions, and approve access. Keep passwords and sign-in codes in the browser. Then return to your agent and repeat the request if needed.

A successful `list_inboxes` call confirms account access. A Connected status or visible tool catalog alone does not. An empty inbox list is a successful connection, not a reason to reconnect.

Choose an existing inbox or ask your agent to create one with your preferred address prefix and show you the returned address. Send it an email from your usual mail app, then ask the agent to read it.

For clients using fixed bearer headers, follow [API-key setup](https://cherami.to/docs/mcp#connect-with-an-api-key). For an installed Cherami plugin, use the [plugin connection guide](https://cherami.to/docs/plugin) instead of adding a duplicate server.

### Local-only MCP clients

Use native remote MCP when available. A client that only launches local stdio servers can use the third-party [`mcp-remote`](https://github.com/geelen/mcp-remote) bridge:

```text
Your agent → local mcp-remote bridge → https://cherami.to/mcp
```

Tools still run on Cherami's hosted service. The bridge is not a second email backend or a Cherami-owned package.

The verified bridge path uses a Claim-issued API key. With Bun installed and `CHERAMI_API_KEY` supplied through the client's private environment, a command-based MCP entry is:

```json
{
  "mcpServers": {
    "cherami": {
      "command": "bunx",
      "args": [
        "--bun",
        "mcp-remote@0.14.3",
        "https://cherami.to/mcp",
        "--transport",
        "http-only",
        "--header",
        "Authorization: Bearer ${CHERAMI_API_KEY}",
        "--silent"
      ]
    }
  }
}
```

`mcp-remote` expands the environment placeholder; do not replace it with a literal key in the configuration. The host must be able to find `bunx` and pass the environment variable to it. Configuration formats vary by client. Follow the [full bridge setup](https://cherami.to/docs/mcp#connect-through-a-local-bridge) for credential setup and troubleshooting.

This pinned version completed initialization and an authenticated `list_inboxes` call against Cherami through Bun. That verifies the bridge's API-key path, not every desktop client or the bridge's browser OAuth flow.

## What your agent can do

- **Read correspondence:** receive mail, search messages, follow conversations, and retrieve attachments for its own file-processing tools.
- **Send and prepare replies:** send authorized messages, reply or forward, and save editable drafts across sessions.
- **Organize ongoing work:** label messages, inspect inbox rules and allowances, and permanently delete mail when explicitly authorized.

The server publishes its current tool names and schemas through MCP discovery. See the [MCP guide](https://cherami.to/docs/mcp#available-tools) for capabilities and the [mail guides](https://cherami.to/docs/guides/receiving) for workflows.

## Put an inbox to work

**Collect project updates.** Give collaborators one address for recurring status updates. Assign an agent to gather missing details from the agreed participants and maintain a summary. The [project-update cookbook](https://cherami.to/docs/cookbooks/project-update-intake) explains the workflow and its boundaries.

**Answer questions from maintained references.** Give a project an address where people can ask questions. An agent uses your reference material to answer routine questions and brings unsupported questions back to you. See the [reference-grounded answers cookbook](https://cherami.to/docs/cookbooks/answer-project-questions).

These are workflows you run with your agent, not automations hosted by Cherami. Incoming mail does not wake an agent. For webhooks and scheduled checking, see the [webhook answer](https://cherami.to/docs/troubleshooting#does-cherami-provide-webhooks) and [receiving guide](https://cherami.to/docs/guides/receiving).

## Access and safety

Connections grant shared account-wide access, not isolation to a single inbox. Set the agent's assignment, permitted recipients and disclosure boundaries before it acts. Connecting does not itself authorize sending or deletion. Treat incoming mail and attachments as untrusted content, not instructions granting new authority.

Deletion is permanent, with no trash or undo. For sending, an accepted submission is not confirmed delivery; follow the tool's recovery guidance after uncertain results rather than creating a new send attempt.

See [safety](https://cherami.to/docs/guides/safety), [privacy](https://cherami.to/privacy) and [credential recovery](https://cherami.to/docs/guides/recovery). Removing a client configuration or signing out of the website does not revoke its credentials.

## Troubleshooting and support

| Symptom | Next step |
| --- | --- |
| Connected, but no account access | Ask for `list_inboxes`; public discovery does not validate credentials. |
| Tools missing | Enable Cherami in the conversation and refresh the host's tool catalog. A new API key does not repair discovery. |
| OAuth never opens | Check CIMD/DCR support and request a private tool. Use explicit API-key configuration if the client cannot complete OAuth. |
| API key rejected | Supply the permanent key, not the Claim phrase, through the client's environment or secret input. |
| Browser GET returns an error | The MCP URL is a protocol endpoint, not a webpage. |

Use the [connection troubleshooting guide](https://cherami.to/docs/mcp#troubleshooting) for diagnosis. For account or private-mail problems, contact [Cherami support](https://cherami.to/support). Public repository issues are suitable for documentation errors, never credentials or private correspondence.

## License

The documentation, skills, manifests and included assets in this repository are licensed under [MIT](LICENSE). This license does not cover Cherami's hosted implementation, grant service access or cover third-party software such as `mcp-remote`. Earlier CC BY 4.0 releases retain their original license.
