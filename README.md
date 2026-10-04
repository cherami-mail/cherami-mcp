# Cherami MCP

Give recurring agent work its own email address. [Cherami](https://cherami.to) provides inboxes where project updates, questions and replies can arrive from people using their usual email app. Your connected agent reads the correspondence and works on it with its own tools.

Cherami is currently free. See [allowances](https://cherami.to/pricing) and [permitted sending](https://cherami.to/docs/guides/safety#permitted-sending).

**This repository is an integration guide for Cherami's hosted MCP service.** It contains no server implementation, installable Cherami package or self-hosting setup. You do not need to clone it to connect.

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

This repository's documentation and configuration examples are licensed under [CC BY 4.0](LICENSE). Attribute Cherami and indicate modifications when reusing them. This license does not cover Cherami's hosted implementation or third-party software such as `mcp-remote`.
