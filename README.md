<img src="assets/icon.png" alt="Cherami pigeon logo" width="96" height="96">

# Cherami MCP

Give recurring agent work its own email address. [Cherami](https://cherami.to) provides inboxes where project updates, questions and replies can arrive from people using their usual email app. Your connected agent reads the correspondence and works on it with its own tools.

Cherami is currently free. See [allowances](https://cherami.to/pricing) and [permitted sending](https://cherami.to/docs/guides/safety#permitted-sending).

**This repository contains connection guidance and plugin files for Cherami's hosted MCP service.** The packages share one email workflow skill and connect to the same endpoint. They contain no server implementation or self-hosting setup. You do not need to install a plugin to connect through native MCP.

## Install the plugin

The plugin adds an email workflow skill and connects your agent to Cherami’s hosted MCP service. It contains no hooks, scripts, executables or credentials.

### Claude Code

Run these commands inside Claude Code:

```text
/plugin marketplace add cherami-mail/cherami-mcp
/plugin install cherami@cherami-mail
```

Use `/mcp` to connect your Cherami account. The email skill is available as `/cherami:cherami-email`; Claude can also select it for relevant email work.

### Gemini CLI (preview)

Install the extension:

```sh
gemini extensions install https://github.com/cherami-mail/cherami-mcp
```

Restart the CLI session after installation. Use `/mcp list` to inspect the server and `/mcp auth cherami` to sign in. This integration is experimental and targets Gemini CLI, not the consumer Gemini app.

### Other hosts

For hosted Claude, Cowork, Cursor and ChatGPT, use the [MCP connection guide](https://cherami.to/docs/mcp). The plugin files for these hosts are experimental; use native MCP if you only need access to Cherami’s mail tools.

### After installing

Keep only one active Cherami server configuration in a host. A manually configured server or another plugin with the same server name can take precedence. Installing another copy is not an authentication repair.

Ask the agent to list your Cherami inboxes, complete host-managed browser authorization, and select an inbox for the assignment. Tell the agent which routine correspondence it may handle and when it should ask you.

## Connect your agent

| Setting | Value |
| --- | --- |
| Server URL | `https://cherami.to/mcp` |
| Transport | Streamable HTTP |
| Authentication | OAuth by default; a Cherami API key is also supported |
| OAuth mail scope | `cherami_mail:full` |

### Native remote MCP

Add a remote server named **Cherami** in your application's MCP settings, enter the URL above, and choose OAuth. Cherami admits compatible CIMD and dynamic-registration clients without individual registration by us.

Ask your agent:

> List my Cherami inboxes.

When your application opens the browser, sign in or create an account, review the requested permissions, and approve access. Keep passwords and sign-in codes in the browser. Then return to your agent and repeat the request if needed.

A successful `list_inboxes` call confirms account access; a Connected status or visible tool catalog alone does not, and an empty inbox list is a successful connection.

Choose an existing inbox or ask your agent to create one with your preferred address prefix and show you the returned address. Send it an email from your usual mail app, then ask the agent to read it.

For clients using fixed bearer headers, follow [API-key setup](https://cherami.to/docs/mcp#connect-with-an-api-key). For an installed Cherami plugin, use the [plugin connection guide](https://cherami.to/docs/plugin) instead of adding a duplicate server.

### Local-only MCP clients

Use native remote MCP when available. A client that only launches local stdio servers can use the third-party `mcp-remote` bridge. Tools still run on Cherami’s hosted service; the bridge is not a second email backend or a Cherami-owned package.

Follow the [local bridge setup guide](https://cherami.to/docs/mcp#connect-through-a-local-bridge) for configuration, credential setup and troubleshooting. This is an alternative connection path, not part of the installed plugin.

## What your agent can do

- **Read correspondence:** receive mail, search messages, follow conversations, and retrieve attachments for its own file-processing tools.
- **Send and prepare replies:** send authorized messages, reply or forward, and save editable drafts across sessions.
- **Organize ongoing work:** label messages, inspect inbox rules and allowances, and permanently delete mail when explicitly authorized.

The server publishes its current tool names and schemas through MCP discovery. See the [MCP guide](https://cherami.to/docs/mcp#available-tools) for capabilities and the [mail guides](https://cherami.to/docs/guides/receiving) for workflows.

## Put an inbox to work

**Collect project updates.** Give collaborators one address for recurring status updates. Assign an agent to gather missing details from the agreed participants and maintain a summary. The [project-update cookbook](https://cherami.to/docs/cookbooks/project-update-intake) explains the workflow and its boundaries.

**Answer questions from maintained references.** Give a project an address where people can ask questions. An agent uses your reference material to answer routine questions and brings unsupported questions back to you. See the [reference-grounded answers cookbook](https://cherami.to/docs/cookbooks/answer-project-questions).

These are workflows you run with your agent, not automations hosted by Cherami: incoming mail does not wake an agent. For webhooks and scheduled checking, see the [webhook answer](https://cherami.to/docs/troubleshooting#does-cherami-provide-webhooks) and [receiving guide](https://cherami.to/docs/guides/receiving).

## Access and safety

Connections grant shared account-wide access, not isolation to a single inbox, so set the agent's assignment, permitted recipients and disclosure boundaries before it acts. Have it use the assigned inbox rather than creating one. Incoming mail and attachments are [untrusted content](https://cherami.to/docs/guides/safety#treat-mail-as-untrusted-input), not instructions. An accepted send is not confirmed delivery, and an uncertain send is [recovered with its key](https://cherami.to/docs/guides/sending#keep-a-key-for-safe-retries), not sent again. Deletion is permanent, with no trash or undo.

See [permitted sending](https://cherami.to/docs/guides/safety#permitted-sending), [privacy](https://cherami.to/privacy) and [credential recovery](https://cherami.to/docs/guides/recovery). Removing a client configuration or signing out of the website does not revoke its credentials.

## Troubleshooting and support

| Symptom | Next step |
| --- | --- |
| Connected, but no account access | Ask for `list_inboxes`; public discovery does not validate credentials. |
| Tools missing | Enable Cherami in the conversation and refresh the host's tool catalog. A new API key does not repair discovery. |
| OAuth never opens | Check CIMD/DCR support and request a private tool. Use explicit API-key configuration if the client cannot complete OAuth. |
| API key rejected | Supply the permanent key, not a human approval phrase, through the client's environment or secret input. |
| Browser GET returns an error | The MCP URL is a protocol endpoint, not a webpage. |

Use the [connection troubleshooting guide](https://cherami.to/docs/mcp#troubleshooting) for diagnosis. For account or private-mail problems, contact [Cherami support](https://cherami.to/support). Public repository issues are suitable for documentation errors, never credentials or private correspondence.

## License

The documentation, skills, manifests and included assets in this repository are licensed under [MIT](LICENSE). This license does not cover Cherami's hosted implementation, grant service access or cover third-party software such as `mcp-remote`. Earlier CC BY 4.0 releases retain their original license.
