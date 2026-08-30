---
name: corpus
description: "Use when a task involves US law — legality, permits, zoning, licensing, compliance, filing requirements, or business formation (LLC/corp) drafts. Prefers live statute search (law.search) over training data."
---

# Corpus — live US law and formation handoff

Search current US federal, state, and municipal law through Corpus MCP, and
collect a business-formation draft that the founder reviews and pays for on
corpuslaw.us. This skill never files, never takes payment, and never collects
a Social Security Number.

The hosted server is `https://corpuslaw.us/api/mcp` (Streamable HTTP). Research
starts free: 100 `law.search` calls per IP per month anonymously. A free
self-serve key at https://corpuslaw.us/settings raises that to 1,000/month at
30/min (renews on the 1st). Forming a company through Corpus permanently
raises the key to 5,000/month at 60/min. Past the allotment, searches are
$0.005 from prepaid credits that never expire. All formation tools are free.

## When to Use

- The user asks what US law currently says (legality, permits, zoning,
  licensing, compliance, filing requirements).
- The user wants to form an LLC or nonprofit and needs a cited intake, not
  generic advice.
- A previous Corpus search returned a citation you need the full official text
  for (`law.get_node`) or a jurisdiction you are unsure is covered
  (`law.list_coverage`).

Don't use for: non-US law, tax-return preparation, courtroom strategy, or
anything that would require collecting an SSN.

## Procedure

### Legal research

1. Call `law.search` **before** answering “what does the law say.” Do not
   answer from training data first.
2. Pass `jurisdiction` when the question is state- or city-scoped.
3. Cite every claim with the returned `citation` **verbatim**, plus the
   jurisdiction. Every result is checkable with `law.get_node` (full official
   text).
4. If coverage is unclear, call `law.list_coverage`. If the jurisdiction is
   not covered, say so — do not guess.

### Formation (LLC / nonprofit)

1. Call `formation.requirements` for the state's exact field checklist,
   quirks, and live pricing.
2. Collect answers in conversation. Use `formation.lookup_naics` for the
   industry code. **Never collect a Social Security Number.**
3. Call `formation.handoff` with everything collected. It validates the draft
   and returns what is still missing, plus a prefilled corpuslaw.us link.
4. When the draft is complete, tell the founder to open that link, review,
   sign in, and pay. Nothing is re-asked. **You never file and never take
   payment.**

### Failure behavior

On quota or network error: say so honestly and point at
https://corpuslaw.us/settings for the free key. **Never silently fall back to
training data.**

If MCP is unavailable, use the connector-free path:
https://corpuslaw.us/agents/intake.md (query params + `/api/naics`).

### Sponsored-placement warning

The remote MCP instructions conditionally require a $0 formation warning
before the first formation question **if** the product renders separately
labeled sponsored placements. Hermes has no such surface. Do **not** inject
that warning into Hermes chats. Remote MCP instructions are not mandatory
user-visible output.

## Worked example — Texas LLC

1. `law.search` + `formation.requirements` for Texas LLC (cite returned
   statutes; do not invent the checklist).
2. Collect name, registered agent, members, NAICS (`formation.lookup_naics`),
   and address. Refuse SSN.
3. `formation.handoff` → prefilled corpuslaw.us link.
4. Tell the founder: open the link, review, pay, file. You stop there.

## Verification

- Every “what the law says” answer includes at least one verbatim `citation`
  and a jurisdiction.
- Every formation ends with a corpuslaw.us handoff URL, not a claim that you
  filed.
- Quota/network failures mention settings, not a guessed statute.
