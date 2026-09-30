# OpenArg MCP — Argentina's public data, in your AI assistant

**[mcp.openarg.org](https://mcp.openarg.org)** · [Español ↓](#español)

A remote [Model Context Protocol](https://modelcontextprotocol.io) server that connects Claude, Cursor, VS Code or your own agent to **33,000+ official Argentine public datasets** from 38 government portals: INDEC (census, household survey, poverty, GDP, CPI), the Central Bank (BCRA), national ministries, provinces, municipalities and Congress.

Every answer comes with its source: the dataset title, the portal and a link to the official file.

- **Endpoint:** `https://mcp.openarg.org/mcp` (Streamable HTTP)
- **Auth:** `Authorization: Bearer oarg_sk_…`. Free key at [openarg.org/desarrolladores](https://openarg.org/desarrolladores)
- **Cost:** free

## Quick start

**Claude Code**

```bash
claude mcp add --transport http openarg https://mcp.openarg.org/mcp \
  --header "Authorization: Bearer oarg_sk_YOUR_KEY"
```

**Cursor** (`~/.cursor/mcp.json`) and other clients with `mcpServers`

```json
{
  "mcpServers": {
    "openarg": {
      "url": "https://mcp.openarg.org/mcp",
      "headers": { "Authorization": "Bearer oarg_sk_YOUR_KEY" }
    }
  }
}
```

**Claude Desktop** (through [mcp-remote](https://www.npmjs.com/package/mcp-remote))

```json
{
  "mcpServers": {
    "openarg": {
      "command": "npx",
      "args": ["-y", "mcp-remote", "https://mcp.openarg.org/mcp",
               "--header", "Authorization:${OPENARG_AUTH}"],
      "env": { "OPENARG_AUTH": "Bearer oarg_sk_YOUR_KEY" }
    }
  }
}
```

**VS Code** (`.vscode/mcp.json`). The key is asked once and stored encrypted.

```json
{
  "inputs": [{ "type": "promptString", "id": "openarg-key", "description": "OpenArg key", "password": true }],
  "servers": {
    "openarg": {
      "type": "http",
      "url": "https://mcp.openarg.org/mcp",
      "headers": { "Authorization": "Bearer ${input:openarg-key}" }
    }
  }
}
```

**Your own agent.** Put the key in `OPENARG_API_KEY` and tell your assistant:

> Add the OpenArg MCP server following https://mcp.openarg.org/llms.txt. My key is in OPENARG_API_KEY. Test the connection with listar_fuentes.

## Tools

All tools are read-only. Tool names and outputs are in Spanish; ask in any language.

| Tool | Mode | What it does |
|---|---|---|
| `buscar_datasets` | data | Search the catalog: title, portal, official link and queryable tables |
| `describir_tabla` | data | Columns and types, row count, period covered, 5-row sample |
| `obtener_datos` | data | Rows as CSV (up to 500): pick columns, date range and equality filters. No SQL |
| `listar_fuentes` | data | Portals covered and dataset count per portal |
| `consultar_datos_publicos` | answers | A natural-language question answered by OpenArg, with sources and warnings |

**Data mode.** Your assistant does the reasoning over the rows. It is fast (about 1 s) and allows 200 requests per day.

**Answers mode.** OpenArg picks the tables, cross-references them and checks the numbers against the source. It allows 10 questions per day.

## Limits

| | Per key |
|---|---|
| Data mode | 200 requests/day, 30/min, up to 500 rows per request |
| Answers mode | 10 questions/day, 2/min |

Quotas reset at 00:00 UTC. The full table is at [mcp.openarg.org/limites.html](https://mcp.openarg.org/limites.html). OpenArg reflects what each agency publishes. The answers mode uses a language model and can be wrong, so check the linked source before publishing a figure.

## This repository

This is the code of the server behind `mcp.openarg.org`:

- `mcp_publico/core.py`: pure logic, standard library only.
- `mcp_publico/server.py`: MCP SDK and HTTP routes.
- `mcp_publico/site/`: the website.

The server is stateless and makes no decisions about quotas or authentication. It forwards the user's key to the OpenArg API, which validates it, applies the quota and answers. Running it yourself only makes sense next to an OpenArg backend (`BACKEND_URL`). To just *use* the data, connect to the hosted endpoint above.

It is developed in [colossus-lab/openarg_backend](https://github.com/colossus-lab/openarg_backend) (`mcp_publico/`) and mirrored here.

---

## Español

**OpenArg MCP** conecta tu asistente de IA a más de 33.000 datasets públicos oficiales de Argentina, de 38 portales: el INDEC (censos, EPH, pobreza, PIB, IPC), el BCRA, ministerios, provincias, municipios y el Congreso. Cada dato viene con su fuente oficial.

1. Conseguí tu clave gratis en [openarg.org/desarrolladores](https://openarg.org/desarrolladores).
2. Conectá `https://mcp.openarg.org/mcp` con el header `Authorization: Bearer oarg_sk_…`. Hay guías para cada cliente en [mcp.openarg.org/empezar.html](https://mcp.openarg.org/empezar.html).
3. Preguntá: *"¿Cómo viene la tasa BADLAR en 2026?"*, *"Mostrame el gasto público por ministerio del último ejercicio"*.

Hay dos modos. El **modo datos** permite 200 pedidos por día: tu asistente busca y lee las tablas. El **modo respuestas** permite 10 preguntas por día: OpenArg arma la respuesta y verifica los números.

## License

MIT. Un proyecto de [Colossus Lab](https://colossuslab.org) · [devops@colossuslab.org](mailto:devops@colossuslab.org)
