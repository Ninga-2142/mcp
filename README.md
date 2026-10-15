# Strategy Terminal for ChatGPT, Claude and Codex

Connect your AI app to the [Strategy Terminal](https://strategy.veeza.io): write and revise trading strategies, run
backtests, setups and books, and read market data from the chat.

There is nothing to install on your computer and no key to copy. The first time the app uses the terminal, your
browser opens the terminal's sign-in page; choose **Allow** and the app works as you, with the access you allowed.
You can disconnect it any time in the terminal under **Agents ▸ Connect**. Works the same on macOS, Windows and Linux.

Server URL, for any app that asks: `https://strategy.veeza.io/api/mcp`

## ChatGPT

**Plugins ▸ Add ▸ Add custom MCP server**
1. Name: `Strategy Terminal`
2. Server URL: `https://strategy.veeza.io/api/mcp`
3. Authentication: **OAuth**
4. Tick *I understand*, then **Create as a plugin**. Sign in when the browser opens.

This adds the terminal to your ChatGPT account, so every chat can use it: mention **@Strategy Terminal**.

Installing this marketplace's plugin in ChatGPT (**Plugins ▸ Add ▸ Add a marketplace**, `Ninga-2142/mcp`) connects
only its **Codex** tasks, which run on your computer. A regular chat runs in OpenAI's cloud and sees only servers
added as above; with the plugin alone, @Strategy Terminal in a chat finds no tools.

## Claude Code

```sh
/plugin marketplace add Ninga-2142/mcp
/plugin install strategy-terminal@strategy-terminal
```

Then run `/mcp`, choose `strategy-terminal` and **Authenticate**. Without the marketplace, this is the same:

```sh
claude mcp add --transport http -s user strategy-terminal https://strategy.veeza.io/api/mcp
claude mcp login strategy-terminal
```

## Claude Desktop and claude.ai

Claude's apps take connectors rather than plugins: **Settings ▸ Connectors ▸ Add custom connector**, paste
`https://strategy.veeza.io/api/mcp`, choose **Add**, then **Connect**.

## Codex CLI

```sh
codex plugin marketplace add Ninga-2142/mcp
codex plugin add strategy-terminal@strategy-terminal
codex mcp login strategy-terminal
```

## For maintainers

| | Claude | ChatGPT / Codex |
|---|---|---|
| marketplace | `.claude-plugin/marketplace.json` | `.agents/plugins/marketplace.json` |
| plugin manifest | `plugins/strategy-terminal/.claude-plugin/plugin.json` | `plugins/strategy-terminal/.codex-plugin/plugin.json` |
| server | `plugins/strategy-terminal/.mcp.json` (shared) | same file |

The source of truth is `deploy/marketplace/` in the terminal's repository; this repository is its published copy.
Before publishing a change:

```sh
claude plugin validate --strict .
claude plugin validate --strict plugins/strategy-terminal
CODEX_HOME=$(mktemp -d) sh -c 'codex plugin marketplace add "$PWD" && codex plugin add strategy-terminal@strategy-terminal && codex mcp list'
```

Bump `version` in both plugin manifests when the plugin changes, so installed copies update.
