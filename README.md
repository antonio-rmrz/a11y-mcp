# a11y-mcp

[![PyPI version](https://badge.fury.io/py/a11y-mcp.svg)](https://pypi.org/project/a11y-mcp/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Python 3.10+](https://img.shields.io/badge/python-3.10+-blue.svg)](https://www.python.org/downloads/)

A Model Context Protocol (MCP) server for **accessibility testing** with **Cloudflare bypass**. Uses [Camoufox](https://github.com/daijro/camoufox) (anti-detect browser) and [axe-core](https://github.com/dequelabs/axe-core) to perform WCAG accessibility audits on any website, including those protected by Cloudflare.

<a href="https://glama.ai/mcp/servers/a11y-mcp">
  <img width="380" height="200" src="https://glama.ai/mcp/servers/a11y-mcp/badge" alt="a11y-mcp MCP server" />
</a>

## Features

- **Cloudflare Bypass** - Uses Camoufox anti-detect browser with C++ level fingerprint injection
- **axe-core Integration** - Industry-standard accessibility testing engine from Deque
- **WCAG Compliance** - Supports WCAG 2.0, 2.1, and 2.2 at levels A, AA, and AAA
- **Multiple Report Formats** - Export as JSON, HTML, or CSV
- **MCP Protocol** - Works with Claude Desktop, Claude Code, VS Code, Cursor, and other MCP clients

## Quick Start

### Claude Code (One Command)

```bash
claude mcp add a11y -- uvx a11y-mcp
```

That's it! This command:
1. Configures Claude Code to use the `a11y` MCP server
2. Uses `uvx` to automatically download and run the package on-demand from PyPI
3. No separate installation step needed - `uvx` handles everything

### Claude Desktop

Add to your config file:

**macOS**: `~/Library/Application Support/Claude/claude_desktop_config.json`
**Windows**: `%APPDATA%\Claude\claude_desktop_config.json`

```json
{
  "mcpServers": {
    "a11y": {
      "command": "uvx",
      "args": ["a11y-mcp"]
    }
  }
}
```

## Alternative Installation Methods

### Using pip (Manual Installation)

If you prefer traditional installation:

```bash
pip install a11y-mcp
```

Then configure your MCP client to run:
```bash
python -m a11y_agent.server
```

### Standalone with uvx

Run directly without any MCP client:

```bash
uvx a11y-mcp
```

> **Note**: `uvx` (from the [uv](https://github.com/astral-sh/uv) project) automatically downloads packages from PyPI into an isolated environment and runs them. No `pip install` required.

## Configuration for Other Tools

### Claude Code (Manual Config)

If you prefer to edit settings manually instead of using the CLI command:

Add to `.claude/settings.json`:

```json
{
  "mcpServers": {
    "a11y": {
      "command": "uvx",
      "args": ["a11y-mcp"]
    }
  }
}
```

### VS Code with Continue

Add to your Continue configuration (`.continue/config.json`):

```json
{
  "experimental": {
    "modelContextProtocolServers": [
      {
        "transport": {
          "type": "stdio",
          "command": "uvx",
          "args": ["a11y-mcp"]
        }
      }
    ]
  }
}
```

### VS Code with Cline

Add to Cline MCP settings:

```json
{
  "mcpServers": {
    "a11y": {
      "command": "uvx",
      "args": ["a11y-mcp"]
    }
  }
}
```

### Cursor

Add to Cursor's MCP configuration (`~/.cursor/mcp.json`):

```json
{
  "mcpServers": {
    "a11y": {
      "command": "uvx",
      "args": ["a11y-mcp"]
    }
  }
}
```

### Windsurf

Add to Windsurf's MCP configuration:

```json
{
  "mcpServers": {
    "a11y": {
      "command": "uvx",
      "args": ["a11y-mcp"]
    }
  }
}
```

### Zed

Add to Zed's settings (`~/.config/zed/settings.json`):

```json
{
  "context_servers": {
    "a11y": {
      "command": {
        "path": "uvx",
        "args": ["a11y-mcp"]
      }
    }
  }
}
```

## Available Tools

| Tool | Description |
|------|-------------|
| `scan_page(url, wcag_level?, wait_for_cloudflare?)` | Navigate to URL, bypass Cloudflare, run accessibility scan |
| `scan_element(selector)` | Scan specific element on current page |
| `get_violations()` | Get detailed violations from last scan |
| `get_full_report()` | Get complete axe report (violations, passes, incomplete) |
| `configure_rules(rules)` | Enable/disable specific accessibility rules |
| `set_wcag_level(level)` | Set WCAG level (A, AA, AAA, 21A, 21AA, 22AA) |
| `export_report(format)` | Export as JSON, HTML, or CSV |

## Usage Examples

Once configured, ask your AI assistant:

> "Scan https://example.com for accessibility issues"

> "Generate an HTML accessibility report for https://adobe.com"

> "Check WCAG 2.2 AA compliance for this page"

> "What accessibility violations does this website have?"

### Example Workflow

```
1. scan_page("https://example.com", wcag_level="22AA")
   → Navigates, bypasses Cloudflare, returns violation summary

2. get_violations()
   → Returns detailed list of accessibility issues

3. export_report("html")
   → Generates styled HTML report for stakeholders
```

## WCAG Levels

| Level | Description |
|-------|-------------|
| `A` | WCAG 2.0 Level A (minimum) |
| `AA` | WCAG 2.0 Level AA (recommended standard) |
| `AAA` | WCAG 2.0 Level AAA (enhanced) |
| `21A` | WCAG 2.1 Level A |
| `21AA` | WCAG 2.1 Level AA |
| `22AA` | WCAG 2.2 Level AA (latest) |

## How Cloudflare Bypass Works

This server uses [Camoufox](https://github.com/daijro/camoufox), an anti-detect browser based on Firefox with:

- **C++ level fingerprint injection** - Modifications at the browser engine level, not JavaScript patches
- **Realistic fingerprints** - Uses BrowserForge to generate realistic device profiles
- **Human-like behavior** - Optional mouse movement humanization
- **Persistent sessions** - Cloudflare verification cookies are preserved

The browser automatically handles:
- JavaScript challenges ("Just a moment...")
- Turnstile CAPTCHA (often passes automatically)
- Browser fingerprint checks

## Requirements

- Python 3.10+
- macOS, Linux, or Windows
- ~300MB disk space (for Camoufox browser binary, downloaded on first run)

## Development

```bash
# Clone the repository
git clone https://github.com/anthropics/a11y-mcp
cd a11y-mcp

# Create virtual environment
uv venv
source .venv/bin/activate

# Install dependencies
uv pip install -e ".[dev]"

# Download Camoufox browser
python -m camoufox fetch

# Run tests
pytest
```

### Building

```bash
uv build
```

### Publishing to PyPI

```bash
uv publish
```

## Debugging

You can debug the MCP server using the MCP Inspector:

```bash
npx @modelcontextprotocol/inspector uvx a11y-mcp
```

## Troubleshooting

### "Cloudflare challenge timed out"

- The site may have aggressive bot detection beyond standard Cloudflare
- Try with `wait_for_cloudflare=True` (default)
- Some sites require manual verification on first visit

### Browser fails to launch

Camoufox downloads its browser binary on first run. If this fails:

```bash
python -c "import camoufox; camoufox.install()"
```

### axe-core injection fails

The page may have strict Content Security Policy. Try scanning a different page on the site first.

## License

MIT License - see [LICENSE](LICENSE) for details.

## Credits

- [axe-core](https://github.com/dequelabs/axe-core) - Accessibility testing engine by Deque
- [Camoufox](https://github.com/daijro/camoufox) - Anti-detect browser
- [MCP](https://modelcontextprotocol.io/) - Model Context Protocol by Anthropic

## Related Projects

- [mcp-server-fetch](https://github.com/modelcontextprotocol/servers/tree/main/src/fetch) - Web content fetching
- [mcp-server-puppeteer](https://github.com/modelcontextprotocol/servers/tree/main/src/puppeteer) - Browser automation
- [mcp-server-playwright](https://github.com/microsoft/playwright-mcp) - Playwright browser control
