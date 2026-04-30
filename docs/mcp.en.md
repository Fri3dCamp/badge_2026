# MCP Server

The **fri3d-badge-mcp** server is a [Model Context Protocol](https://modelcontextprotocol.io/) server that gives AI assistants (such as Claude, GitHub Copilot, Cursor, …) structured access to the documentation you need when programming the Fri3d Camp 2026 badge.

Instead of copy-pasting docs into a chat window, you connect the MCP server once and the AI can look up MicroPython, MicroPythonOS, and Fri3d badge docs on its own.

## What it provides

| Tool | What it does |
|---|---|
| `search_micropython_docs` | Keyword search across the MicroPython docs |
| `get_micropython_page` | Fetch a MicroPython doc page by path or URL |
| `list_micropython_modules` | Curated list of common MicroPython modules |
| `search_fri3d_badge_docs` | Search across all Fri3d badge_2026 pages |
| `get_fri3d_badge_page` | Fetch a Fri3d badge_2026 doc page |
| `list_fri3d_badge_pages` | List all known Fri3d badge_2026 pages |
| `search_micropythonos_docs` | Search across all MicroPythonOS doc pages |
| `get_micropythonos_page` | Fetch a MicroPythonOS doc page |
| `list_micropythonos_pages` | List all known MicroPythonOS doc pages |

## Quickstart — use the hosted server

The easiest option: use the server that is already deployed on Vercel. No installation needed.

```
https://fri3d-badge-mcp.vercel.app/mcp
```

Add this URL as an MCP server in your AI tool (see the sections below).

## Quickstart — run locally with npx

If you prefer to run the server on your own machine (requires [Node.js](https://nodejs.org/) 18+):

```bash
npx fri3d-badge-mcp
```

The server starts and listens on **stdio** — your MCP client will launch it automatically.

## Configure your AI tool

### Claude Desktop

Edit `~/Library/Application Support/Claude/claude_desktop_config.json` (macOS) or `%APPDATA%\Claude\claude_desktop_config.json` (Windows):

```json
{
  "mcpServers": {
    "fri3d-badge": {
      "command": "npx",
      "args": ["fri3d-badge-mcp"]
    }
  }
}
```

Restart Claude Desktop after saving.

### VS Code (GitHub Copilot)

Open your VS Code settings (`Ctrl+,` / `Cmd+,`), search for **MCP**, and add the server to `mcp.servers`:

```json
{
  "mcp": {
    "servers": {
      "fri3d-badge": {
        "type": "http",
        "url": "https://fri3d-badge-mcp.vercel.app/mcp"
      }
    }
  }
}
```

Or use the local npx variant:

```json
{
  "mcp": {
    "servers": {
      "fri3d-badge": {
        "type": "stdio",
        "command": "npx",
        "args": ["fri3d-badge-mcp"]
      }
    }
  }
}
```

### Cursor

Open **Settings → MCP** and add a new server entry:

- **Type**: `stdio`
- **Command**: `npx`
- **Args**: `fri3d-badge-mcp`

Or point it at the hosted HTTP endpoint `https://fri3d-badge-mcp.vercel.app/mcp`.

## Source code

The server is open-source: [github.com/DrSkunk/fri3d-badge-mcp](https://github.com/DrSkunk/fri3d-badge-mcp)
