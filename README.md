# corpus

Agent Plugins v1 package for [Corpus](https://corpuslaw.us): live US legal
research and business-formation handoff.

The **content** (this skill + the MCP URL) is vendor-neutral. The **package
wrapper** is Agent Plugins v1 (`plugin.json` + `mcp.json` at the repo root) —
that layout installs directly in Hermes today. Codex/ChatGPT use a different
folder shape; they reuse the same URL.

Manifest `name` is `corpus`. The directory may be called `corpus-hermes`;
nothing in the package depends on that folder name.

## Compatibility

| Harness | Package installs directly | Same MCP URL + skill instructions |
|---|---|---|
| Hermes (Agent Plugins v1) | Yes | Yes |
| Codex / ChatGPT | No (own layout: `.codex-plugin/` + `.mcp.json`) | Yes — `codex mcp add corpus --url https://corpuslaw.us/api/mcp` |
| Any MCP-capable harness | No | Yes — same Streamable HTTP URL |

A Codex folder-adapter is a later package, not this one.

## Install (Hermes)

Local copy (this directory is not a GitHub identifier yet):

```bash
hermes plugins doctor .          # zero diagnostics
cp -R . ~/.hermes/plugins/corpus
hermes plugins enable corpus     # portable packages install disabled
hermes mcp test corpus
```

Once the repo is on GitHub:

```bash
hermes plugins install <owner/repo> --no-enable
hermes plugins enable corpus
```

Portable packages install **disabled**. Enable is a separate consent step.

No secrets belong in `mcp.json`. Anonymous research works without a key.
For higher limits, add a header via `hermes mcp add` (not this file):

```bash
# free self-serve key — https://corpuslaw.us/settings
hermes mcp add corpus --url "https://corpuslaw.us/api/mcp"
```

## What the agent should do

- Call `law.search` before answering what US law currently says.
- Cite returned `citation` verbatim. Use `law.get_node` / `law.list_coverage`
  instead of guessing.
- Formation: `formation.requirements` → `formation.lookup_naics` →
  `formation.handoff`. Never collect an SSN. Never file or take payment.
- On quota (100 anonymous searches/IP/month) or network error, say so and
  point at https://corpuslaw.us/settings — never silently use training data.
- Connector-free fallback: https://corpuslaw.us/agents/intake.md

Hosted server: `https://corpuslaw.us/api/mcp` (`corpus-legal` 1.2.1,
protocol `2025-06-18`).

## License

MIT
