# a11y-agent

An MCP (Model Context Protocol) server for accessibility testing with Cloudflare bypass. Uses **Camoufox** (anti-detect browser) and **axe-core** for accessibility audits on protected sites.

## Quick Start

### Install with Claude Desktop

Add to your Claude Desktop config (`~/Library/Application Support/Claude/claude_desktop_config.json` on macOS):

```json
{
  "mcpServers": {
    "a11y-agent": {
      "command": "uvx",
      "args": ["a11y-agent"]
    }
  }
}
```

Then restart Claude Desktop.

> **Note**: On first run, Camoufox will automatically download its browser binary (~100MB).

### Install with Claude Code

```bash
claude mcp add a11y-agent -- uvx a11y-agent
```

Or add manually to `.claude/settings.json`:

```json
{
  "mcpServers": {
    "a11y-agent": {
      "command": "uvx",
      "args": ["a11y-agent"]
    }
  }
}
```

## Installation Options

### From PyPI (recommended)

```bash
# Run directly without installing
uvx a11y-agent

# Or install globally
uv tool install a11y-agent
```

### From GitHub

```bash
# Run directly from repo
uvx --from git+https://github.com/yourusername/a11y-agent a11y-agent

# Or install from repo
uv tool install git+https://github.com/yourusername/a11y-agent
```

### For Development

```bash
git clone https://github.com/yourusername/a11y-agent
cd a11y-agent
uv venv
source .venv/bin/activate
uv pip install -e ".[dev]"
```

## Features

- **Cloudflare Bypass**: Uses Camoufox, an anti-detect browser with C++ level fingerprint injection
- **axe-core Integration**: Industry-standard accessibility testing engine from Deque
- **WCAG Compliance**: Supports WCAG 2.0, 2.1, and 2.2 at levels A, AA, and AAA
- **Multiple Report Formats**: Export as JSON, HTML, or CSV
- **Persistent Sessions**: Cloudflare verification cookies are preserved across scans

## Available Tools

| Tool | Description |
|------|-------------|
| `scan_page(url, wcag_level, wait_for_cloudflare)` | Navigate to URL, bypass Cloudflare, run accessibility scan |
| `scan_element(selector)` | Scan specific element on current page |
| `get_violations()` | Get detailed violations from last scan |
| `get_full_report()` | Get complete axe report (violations, passes, incomplete) |
| `configure_rules(rules)` | Enable/disable specific accessibility rules |
| `set_wcag_level(level)` | Set WCAG level (A, AA, AAA, 21A, 21AA, 22AA) |
| `export_report(format)` | Export as JSON, HTML, or CSV |

## Example Usage

Once configured, ask Claude to:

> "Scan https://www.adobe.com/plans/creativecloud.html for accessibility issues"

Claude will:
1. Use `scan_page` to navigate and bypass Cloudflare
2. Run axe-core accessibility audit
3. Return a summary of violations by severity

For a detailed report:

> "Generate an HTML accessibility report for that page"

Claude will use `export_report("html")` to create a styled report.

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

Camoufox is an anti-detect browser based on Firefox with:

- **C++ level fingerprint injection**: Modifications at the browser engine level, not JavaScript patches
- **Realistic fingerprints**: Uses BrowserForge to generate realistic device profiles
- **Human-like behavior**: Optional mouse movement humanization
- **Persistent sessions**: Cloudflare verification cookies are preserved across scans

The browser automatically handles:
- JavaScript challenges ("Just a moment...")
- Turnstile CAPTCHA (often passes automatically due to realistic fingerprints)
- Browser fingerprint checks

## Requirements

- Python 3.10+ (automatically managed by `uvx`)
- macOS, Linux, or Windows
- ~100MB disk space for Camoufox browser binary

## Troubleshooting

### "Cloudflare challenge timed out"

- The site may have aggressive bot detection beyond standard Cloudflare
- Try running with `wait_for_cloudflare=True` (default)
- Some sites require manual verification on first visit

### Browser fails to launch

Camoufox downloads its browser binary on first run. If this fails:

```bash
# Manually trigger browser download
python -c "import camoufox; camoufox.install()"
```

### axe-core injection fails

The page may have strict Content Security Policy. Try scanning a different page on the site first.

## License

MIT

## Credits

- [axe-core](https://github.com/dequelabs/axe-core) - Accessibility testing engine by Deque
- [Camoufox](https://github.com/daijro/camoufox) - Anti-detect browser
- [MCP](https://modelcontextprotocol.io/) - Model Context Protocol by Anthropic
