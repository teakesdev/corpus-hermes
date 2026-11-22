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
For higher limits, add a header via `hermes mcp add` (not this file); ask the
agent to call `account.status` for the live limits:

```bash
# free self-serve key — https://corpuslaw.us/settings
hermes mcp add corpus --url "https://corpuslaw.us/api/mcp"
```

## What the agent should do

The package ships two skills, mirrored byte-for-byte from the Corpus monorepo
(`packages/skills/`):

- `skills/corpus-business-formation/` — runs an LLC or nonprofit intake:
  `formation.requirements` → `formation.check_name` / `formation.lookup_naics` →
  `formation.handoff`. With the founder's explicit consent to the exact amount,
  `formation.checkout` returns a hosted Stripe link (default) or a USDC pay request
  when the founder asks for it; the agent never pays, never files and never collects
  an SSN or ITIN. It can also run Corpus's free law scan for a business.
- `skills/corpus-legal-research/` — `law.search` before answering what US law says,
  citations verbatim, `law.get_node` / `law.list_coverage` instead of guessing, and
  `account.status` before quoting any limit or price.

Do not edit these files here. Change them upstream, then re-mirror with Corpus's
`scripts/mirror-kit-skills.mjs --hermes <this checkout>`; `skills/UPSTREAM.sha256`
records the mirrored bytes and `node scripts/check-upstream-mirror.mjs` fails on any
local edit.

The actively maintained Hermes package is `plugins/corpus` in
[corpus-agent-kit](https://github.com/teakesdev/corpus-agent-kit)
(`hermes plugins install teakesdev/corpus-agent-kit/plugins/corpus`), which carries
the same skills.

Hosted server: `https://corpuslaw.us/api/mcp` (protocol `2025-06-18`).

## License

MIT
