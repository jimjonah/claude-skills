# agent-readiness

A Claude Code skill for auditing and improving how discoverable and readable a website is to AI
agents and answer engines.

It runs the [isitagentready.com](https://isitagentready.com) scan, triages which checks actually
apply to the site in question, and implements the ones that matter.

## Why

Running the scan is the easy part. The parts that waste time are the ones this skill encodes:

- **The report is hard to read correctly.** The results page is client-rendered — curl 404s and
  WebFetch returns the generic explainer, never the results. You have to POST to the API. Results
  are also cached per-domain, so "it still complains after I deployed" is usually a stale page.
- **Most sites fail most checks legitimately.** Roughly half the checks (`apiCatalog`,
  `mcpServerCard`, `a2aAgentCard`, `oauthDiscovery`, `webMcp`, `dnsAid`, the commerce ones) declare
  that agents can *call* something. On a marketing or docs site those would be discovery documents
  pointing at endpoints that don't exist. The skill says so plainly instead of fabricating them —
  and tells you that Level 3 ("Agent-Readable") is the honest ceiling for a content-only site,
  rather than chasing the score.
- **The checks that do matter have sharp edges.** Content negotiation without `Vary: Accept` gets
  poisoned by CDN caching; `_routes.json` exclusions can silently disable your API; converting HTML
  to Markdown at request time is a trap.

## What it covers

Three fixes worth making on any content site, in payoff order:

1. **Content Signals** (`contentSignals`) — one robots.txt edit, usually the only thing blocking
   Level 2. Includes the mental model that resolves the usual confusion (`Allow` governs fetching,
   `Content-Signal` governs use after fetching) and an even-handed brief on the `ai-train=yes/no`
   decision, which the skill treats as the user's call rather than a default.
2. **Markdown for Agents** (`markdownNegotiation`) — guessable `.md` twins *and* real `Accept`
   negotiation. Ships a tested Cloudflare Pages `_middleware.ts` with correct q-value parsing, so
   browsers keep getting HTML and only clients that actively prefer Markdown get Markdown. Notes on
   the Netlify / Vercel / nginx equivalents, and on hand-writing twins vs. build-time conversion.
3. **Link headers** (`linkHeaders`) — `rel="alternate"` in headers and in HTML.

Plus a gotchas section covering `Vary: Accept`, trailing-slash redirects, verifying against the
real runtime (`wrangler pages dev`) rather than a build, and re-scanning before reporting results.

## Install

See the [repo README](../../README.md#install).

## Use

Claude loads it automatically when the task matches — "run an agent-readiness report",
"make this site readable by AI crawlers", "fix this failing isitagentready check", "add llms.txt",
GEO/AEO work. Or invoke it directly:

```
/agent-readiness:agent-readiness https://example.com
```

If you copied the folder into `~/.claude/skills/` instead of installing the plugin, it's
`/agent-readiness`.
