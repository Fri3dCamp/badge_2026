# MCP-server

De **fri3d-badge-mcp**-server is een [Model Context Protocol](https://modelcontextprotocol.io/)-server die AI-assistenten (zoals Claude, GitHub Copilot, Cursor, …) gestructureerde toegang geeft tot de documentatie die je nodig hebt bij het programmeren van de Fri3d Camp 2026-badge.

In plaats van documentatie handmatig in een chatvenster te plakken, verbind je de MCP-server eenmalig en kan de AI zelf MicroPython-, MicroPythonOS- en Fri3d badge-documentatie opzoeken.

## Wat de server biedt

| Tool | Wat het doet |
|---|---|
| `search_micropython_docs` | Zoeken in de MicroPython-documentatie |
| `get_micropython_page` | Een MicroPython-pagina ophalen via pad of URL |
| `list_micropython_modules` | Overzicht van veelgebruikte MicroPython-modules |
| `search_fri3d_badge_docs` | Zoeken in alle Fri3d badge_2026-pagina's |
| `get_fri3d_badge_page` | Een Fri3d badge_2026-pagina ophalen |
| `list_fri3d_badge_pages` | Alle bekende Fri3d badge_2026-pagina's oplijsten |
| `search_micropythonos_docs` | Zoeken in alle MicroPythonOS-pagina's |
| `get_micropythonos_page` | Een MicroPythonOS-pagina ophalen |
| `list_micropythonos_pages` | Alle bekende MicroPythonOS-pagina's oplijsten |

## Snel aan de slag — gebruik de gehoste server

De eenvoudigste optie: gebruik de server die al op Vercel staat. Geen installatie nodig.

```
https://fri3d-badge-mcp.vercel.app/mcp
```

Voeg dit adres toe als MCP-server in je AI-tool (zie de secties hieronder).

## Snel aan de slag — lokaal uitvoeren met npx

Als je de server liever op je eigen computer draait (vereist [Node.js](https://nodejs.org/) 18+):

```bash
npx fri3d-badge-mcp
```

De server start en luistert via **stdio** — je MCP-client start hem automatisch op.

## Je AI-tool instellen

### Claude Desktop

Bewerk `~/Library/Application Support/Claude/claude_desktop_config.json` (macOS) of `%APPDATA%\Claude\claude_desktop_config.json` (Windows):

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

Herstart Claude Desktop nadat je het bestand hebt opgeslagen.

### VS Code (GitHub Copilot)

Open de VS Code-instellingen (`Ctrl+,` / `Cmd+,`), zoek naar **MCP** en voeg de server toe aan `mcp.servers`:

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

Of gebruik de lokale npx-variant:

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

Open **Settings → MCP** en voeg een nieuwe server toe:

- **Type**: `stdio`
- **Command**: `npx`
- **Args**: `fri3d-badge-mcp`

Of wijs het naar het gehoste HTTP-eindpunt `https://fri3d-badge-mcp.vercel.app/mcp`.

## Broncode

De server is open-source: [github.com/DrSkunk/fri3d-badge-mcp](https://github.com/DrSkunk/fri3d-badge-mcp)
