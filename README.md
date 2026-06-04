# MCP Demo

A minimal example of building an LLM agent that talks to external tools over the **Model Context Protocol (MCP)**. It runs an interactive terminal chat where a Groq-hosted model can drive a real browser through a Playwright MCP server — with built-in conversation memory.

## How it works

```
You ──▶ MCPAgent (LangChain + Groq)  ──▶  MCP client  ──▶  Playwright MCP server  ──▶  Browser
            ▲ conversation memory                                                          │
            └──────────────────────────── results ◀──────────────────────────────────────┘
```

- **`app.py`** — an async REPL built on [`mcp-use`](https://github.com/mcp-use/mcp-use). It wires a `ChatGroq` model (`qwen-qwq-32b`) to an `MCPAgent` with memory enabled, so the agent can plan up to 15 steps and remember context across turns.
- **`browser_mcp.json`** — the MCP server config; here it launches `@playwright/mcp`, giving the agent browser-automation tools.

Type a request, the agent decides which MCP tools to call, and streams back the result. `clear` resets memory; `exit` / `quit` ends the session.

## Setup

**Prerequisites:** Python 3.11+, Node.js (for the Playwright MCP server via `npx`), and a [Groq API key](https://console.groq.com/).

```bash
# Install dependencies
pip install mcp-use langchain-groq python-dotenv

# Add your key
echo "GROQ_API_KEY=your_key_here" > .env

# Run the chat
python app.py
```

The Playwright MCP server is started automatically by `npx` using `browser_mcp.json` — no separate install needed.

## Extending it

Add more capabilities by registering additional servers in `browser_mcp.json`:

```json
{
  "mcpServers": {
    "playwright": { "command": "npx", "args": ["@playwright/mcp@latest"] },
    "filesystem": { "command": "npx", "args": ["@modelcontextprotocol/server-filesystem", "/path"] }
  }
}
```

---
<sub>A learning demo exploring MCP for connecting LLM agents to real-world tools.</sub>
