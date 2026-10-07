# Genchi MCP connector

**Project delivery predictions, from the people doing the work, in your AI assistant.**

[Genchi](https://www.genchi.com) shows engineering leaders a prediction of which projects are heading for a missed deadline and which aren't, from the people doing the work. Each team member casts an anonymous, one-click confidence vote in response to a regular, automated Slack prompt. Those votes are combined by project into a single score, tracked over time.

This connector puts that signal where engineering leaders already are. Instead of opening a dashboard, you ask your assistant:

> "Which of my initiatives should I be worried about?"
>
> "How is the payments migration tracking?"
>
> "What changed on the platform rebuild this week?"

It uses the [Model Context Protocol](https://modelcontextprotocol.io) (MCP). There is one connector, not one per assistant: the same endpoint works in Claude, ChatGPT and Grok, and in any other assistant that supports MCP.

> This repository contains documentation only. The connector is hosted by Genchi; there is nothing to install or run.

## Endpoint

```
https://genchi.com/api/mcp
```

You need a Genchi account. Genchi is free for teams of up to 10 people: [create an account](https://app.genchi.com/create-account).

## Connecting

Every assistant follows the same steps: add Genchi as a connector using the endpoint above, sign in on the Genchi page you're sent to (check the address bar shows genchi.com), and you're returned to your assistant, connected. You only do this once.

**Claude:** Settings → Connectors → Add custom connector, paste the endpoint, and sign in when prompted.

**Claude Code:**

```
claude mcp add --transport http --scope user genchi https://genchi.com/api/mcp
```

Then run `/mcp` inside Claude Code to complete the sign-in.

**ChatGPT:** Genchi is listed in ChatGPT's Plugin Directory. Or go to Settings → Plugins, add a new plugin, choose Server URL, paste the endpoint, and leave authentication as OAuth. Enable Genchi in a conversation with the **+** in the composer or by typing `@Genchi`.

**Grok:** Go to grok.com/connectors, click New Connector, choose Custom, paste the endpoint, and sign in.

To check it worked, ask: *"What am I tracking in Genchi?"*

Each person connects their own account, and each connection returns only what that person can already see in Genchi.

## What you can ask

| You ask | Genchi returns |
|---|---|
| "What initiatives am I on?" | Every initiative you can see, lowest confidence first, with deadlines and your role on each. |
| "How is the payments migration tracking?" | Current confidence, the recent trend, and how many of the team have voted. |
| "What's blocking the platform rebuild?" | Blockers currently raised, with who raised each and when. |
| "Give me this week's update on Project Atlas." | Current confidence against last week's, the trend, and blockers from the past seven days. |
| "Show me everything under the platform programme." | That initiative and everything nested beneath it, as a tree you can click through. |
| "Create an initiative for the Q4 API migration." | A new initiative with you as Owner and a weekly Friday confidence vote. |

## Interactive views

Genchi is an MCP App as well as an MCP server. In assistants that support MCP Apps, including Claude, ChatGPT and Grok, it draws your portfolio rather than describing it:

- **The portfolio:** every initiative as a colour-coded card, red through green by confidence. Initiatives with work nested beneath them are drawn as stacks, coloured by the best and worst score below, so a red project buried in a healthy-looking programme shows before you drill in.
- **The tree:** an initiative and everything beneath it, each with its own score.
- **The detail:** one initiative's confidence chart over the last fortnight, the row of individual votes, current blockers and recent comments.

Assistants without MCP Apps support get the same information as text.

## Tools

| Tool | Type | Purpose |
|---|---|---|
| `genchi_list_initiatives` | Read | Lists the initiatives you can see, with the current confidence score for each. Draws the portfolio, or the tree beneath a named initiative. |
| `genchi_get_confidence` | Read | Current confidence score and trend for one initiative. Draws the confidence chart, individual votes and blockers. |
| `genchi_get_blockers` | Read | Blockers currently raised against one initiative, with attribution. |
| `genchi_get_weekly_summary` | Read | The weekly summary for one initiative, compared with last week. |
| `genchi_create_initiative` | Write | Creates a new initiative with you as Owner and a weekly Friday confidence vote. |
| `genchi_connect_slack` | Read | Reports whether a Slack workspace is connected and how to connect one. |

Initiatives are identified by name, matched case-insensitively and partially. Where a name matches more than one, Genchi lists the candidates and asks which you meant.

## What the assistant can and cannot see

**It can see** initiatives where you are the Owner, a team member or an observer, plus everything nested beneath them: name, goal, deadline, aggregate confidence, trend, participation counts, blockers and recent comments.

**It cannot see** initiatives you're not part of, other people's accounts, billing information, or which person cast which confidence vote.

**It can change exactly one thing:** it can create a new initiative owned by you. It cannot modify or delete initiatives, change team membership, cast votes, or touch billing or account settings.

## Anonymity

Votes are visible but not attributable, through the connector as in the app. Individual vote values are shuffled into a fresh random order on Genchi's servers on every response, so no value can be tied to a person, and no tool returns a name alongside a vote. Blockers and comments are the deliberate exception: they're attributed in Genchi by design.

## Technical details

| Item | Value |
|---|---|
| Server endpoint | `https://genchi.com/api/mcp` |
| Transport | Streamable HTTP |
| Authorization | OAuth 2.1 with PKCE (S256 required) |
| Authorization server | `https://genchi.com` |
| Protected resource metadata | `https://genchi.com/.well-known/oauth-protected-resource/api/mcp` |
| Authorization server metadata | `https://genchi.com/.well-known/oauth-authorization-server` |
| Client identification | Client ID Metadata Documents, or Dynamic Client Registration at `https://genchi.com/api/mcp/register` |
| Scopes | `genchi:read`, `genchi:write`; `offline_access` honoured for refresh tokens |
| Access token lifetime | 1 hour, renewed automatically |
| Refresh token lifetime | 30 days, rotated on each use |
| Interactive views | MCP Apps extension; self-contained HTML with no external network access and an empty content security policy |

## Managing connections

In Genchi, go to **Admin → AI Assistants** to see your connected assistants and disconnect any of them. Administrators can see and revoke every connection in the company. Revocation takes effect on the assistant's very next request.

## Troubleshooting

- **The assistant says it has no Genchi tools.** Check the connector is enabled for the conversation as well as added to your account. Most assistants need both. Starting a new conversation can also help.
- **Sign-in fails.** The connector uses your Genchi email and password. If you signed up with Google or Atlassian and never set a password, use "Forgot password" at app.genchi.com.
- **An initiative is missing.** You'll only see initiatives you're part of; archived and completed ones are excluded.

## Links

- [Genchi](https://www.genchi.com)
- [Full connector documentation](https://www.genchi.com/mcp-integration-info.html)
- [Does asking the team actually work?](https://www.genchi.com/does-a-team-confidence-vote-work.html)
- [Security Policy](https://www.genchi.com/security-policy.html) · [Privacy Policy](https://www.genchi.com/privacy-policy.html)

Questions: [support@genchi.com](mailto:support@genchi.com)
