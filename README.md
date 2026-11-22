# corpus

Agent Plugins v1 package for [Corpus](https://corpuslaw.us): live US legal
research and business-formation handoff.

The **content** (the two skills + the MCP URL) is vendor-neutral. The **package
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

```bash
hermes plugins install teakesdev/corpus-hermes --no-enable
hermes plugins enable corpus     # portable packages install disabled
hermes plugins doctor corpus --ci
```

Portable packages install **disabled**. Enable is a separate consent step.

After enabling, start a fresh Hermes chat and ask it to call `law.list_coverage`
and list the two Corpus skills. Plugin MCP servers use a namespaced runtime ID, so
`hermes mcp test corpus` only tests a separately configured standalone server.

From a local checkout instead: `hermes plugins doctor .`, then
`cp -R . ~/.hermes/plugins/corpus` and `hermes plugins enable corpus`.

## Limits

No secrets belong in `mcp.json`. Research is free to start: 250 searches a month
without a key (per IP), 1,000 a month at 30/min with a free self-serve key from
https://corpuslaw.us/settings, and 5,000 a month at 60/min once a company has been
formed through Corpus. Formation tools never use research credits. Ask the agent to
call `account.status` for the live numbers.

To use a key, add it as a header with `hermes mcp add --auth header` (not in this
file), and paste the key when Hermes prompts for the API key / Bearer token:

```bash
hermes mcp add corpus --url "https://corpuslaw.us/api/mcp" --auth header
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
