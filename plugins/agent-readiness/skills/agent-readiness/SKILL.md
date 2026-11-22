---
name: agent-readiness
description: Audit and improve how discoverable and readable a website is to AI agents and answer engines. Run the isitagentready.com scan, triage which checks actually apply, and implement the ones that matter — robots.txt Content Signals, Markdown twins with Accept negotiation, and Link alternate headers. Load when asked to run an agent-readiness report, improve GEO/AEO, make a site readable by AI crawlers, add llms.txt or Markdown for Agents, or fix a failing isitagentready check.
---

# Agent readiness

Two separable jobs: **read the report correctly**, then **fix only what applies**.
Most sites fail most checks legitimately — the score is not the goal.

## 1. Run the scan

`https://isitagentready.com/<domain>` is client-rendered. That URL 404s to curl
and WebFetch returns only the generic explainer, never the results. Use the API:

```bash
curl -s -X POST https://isitagentready.com/api/scan \
  -H 'Content-Type: application/json' \
  -d '{"url":"https://example.com"}' --max-time 180 -o scan.json
```

Triage to the non-passing checks:

```bash
python3 -c "
import json
d=json.load(open('scan.json'))
print('level',d.get('level'),d.get('levelName'))
nl=d.get('nextLevel') or {}
print('next:',nl.get('target'),nl.get('name'),'needs:',[r['check'] for r in nl.get('requirements',[])])
for cat,checks in d['checks'].items():
    for name,c in checks.items():
        if c.get('status')!='pass':
            print(f\"  [{c['status']}] {cat}.{name}: {c['message']}\")
"
```

Each check also carries an `evidence` array — the exact requests made and what was
concluded. Read it before believing a failure; it tells you what the scanner
actually looked for (e.g. `dnsAid` queries `_index._agents.<domain>` SVCB/TXT).

**Results are stored per-domain.** The web page keeps serving the previous run
until a new scan is triggered, so a user reporting "it still complains" after a
deploy is almost always looking at a cached result. Re-POST and compare
`scannedAt` before debugging anything.

## 2. Triage — what applies

`neutral` is informational, not a failure. Commerce checks (`x402`, `mpp`, `ucp`,
`acp`, `ap2`) self-neutralize on non-commerce sites.

**Skip for any site without an agent-callable API.** `apiCatalog`,
`mcpServerCard`, `a2aAgentCard`, `oauthDiscovery`, `oauthProtectedResource`,
`authMd`, `webMcp`, `dnsAid` — every one declares that agents can *call*
something. On a marketing or docs site these would be discovery documents
pointing at endpoints that don't exist. Say so plainly rather than fabricating
them; a form handler behind a CAPTCHA is not an agent API.

**Level 4 ("Agent-Integrated") requires exactly those declarations**, so for a
content-only site **Level 3 ("Agent-Readable") is the honest ceiling.** Tell the
user that instead of chasing the number.

**Worth fixing on any content site** — the three below, in payoff order.

## 3. Content Signals (`contentSignals`)

One robots.txt edit; usually the only thing blocking Level 2.

```
User-agent: *
Content-Signal: search=yes, ai-input=yes, ai-train=yes
Allow: /
```

Must sit inside a `User-agent` group. The mental model that resolves most
confusion: **`Allow` governs fetching; `Content-Signal` governs use after
fetching.** Allowing GPTBot while declaring `ai-train=no` is coherent, not
contradictory.

- `search` — index and link back
- `ai-input` — retrieve to ground/cite a generated answer (this is the path that
  converts; keep it `yes`)
- `ai-train` — train or fine-tune on the content

**`ai-train` is the user's call, not a default to assume.** Ask. Arguments for
`yes`: a small or new product is invisible in conversations that don't trigger
retrieval, and marketing copy loses nothing by being memorized. Arguments for
`no`: audience signalling (publishing, journalism, legal), and baked-in weights
go stale — prices and requirements get misquoted years later, where retrieval
always reads live copy. Compliance is voluntary and untested either way; it's a
declared preference, not a control, and reversible by editing a text file
(though `no` doesn't retract what's already ingested).

## 4. Markdown for Agents (`markdownNegotiation`)

`Accept: text/markdown` is a **request** header the agent sends; the server
chooses what to return. The check fails when the same URL always returns HTML.

Two independent halves — do both:

1. **Guessable URLs.** Publish `/about.md` next to `/about`. Many crawlers just
   append `.md`; costs them one request and needs no server cooperation.
2. **Negotiation.** Same URL returns Markdown when `Accept` prefers it. This is
   what the scanner tests.

**Where the Markdown comes from.** Never convert HTML at request time. Either
convert at build time (Turndown/rehype over the built HTML — automatic, never
drifts, but marketing pages yield noisy output full of nav and button text), or
hand-write the twins (far better input for agents, at the cost of a second copy).
For a handful of pages, hand-written wins; curate converter output above ~20
pages. Faithfulness matters more than polish — verify with a sentence-level diff
of built HTML against each `.md`, normalizing for any wordmark markup that splits
words (`<span>Kay<span>it</span></span>` → `Kay it`) before believing a gap.

Omit fabricated sample data (fake testimonials, demo transcripts) from the
Markdown and say so — agents quote it as fact.

### Cloudflare Pages implementation (tested)

`functions/_middleware.ts`. Sets the content type itself rather than trusting the
asset server's `.md` MIME mapping or `_headers` honouring a `Content-Type`
override:

```ts
const MARKDOWN_TYPE = 'text/markdown; charset=utf-8';
const MARKDOWN_FOR: Record<string, string> = {
  '/': '/index.md',
  '/about': '/about.md',
};

function quality(accept: string, target: string): number {
  const type = target.slice(0, target.indexOf('/'));
  let best = -1;
  for (const entry of accept.split(',')) {
    const [rawMedia, ...params] = entry.trim().split(';');
    const media = rawMedia.trim().toLowerCase();
    if (media !== target && media !== `${type}/*` && media !== '*/*') continue;
    let q = 1;
    for (const param of params) {
      const m = /^\s*q\s*=\s*([0-9.]+)\s*$/i.exec(param);
      if (m && !Number.isNaN(Number.parseFloat(m[1]))) q = Number.parseFloat(m[1]);
    }
    if (q > best) best = q;
  }
  return best;
}

// True only when the client actively prefers Markdown. A browser's
// `text/html,…,*/*;q=0.8` scores HTML 1.0 vs Markdown 0.8 via the wildcard, and a
// bare `*/*` ties — both keep HTML. Ties and absences always fall back to HTML.
function prefersMarkdown(accept: string | null): boolean {
  if (!accept) return false;
  const md = Math.max(quality(accept, 'text/markdown'), quality(accept, 'text/x-markdown'));
  return md > 0 && md > quality(accept, 'text/html');
}

export const onRequest: PagesFunction = async ({ request, next }) => {
  if (request.method !== 'GET' && request.method !== 'HEAD') return next();
  const url = new URL(request.url);
  const path = url.pathname.replace(/\/+$/, '') || '/';   // /about and /about/ are one page
  if (path.startsWith('/api/')) return next();            // leave real endpoints alone

  if (path.endsWith('.md')) {
    const res = await next();
    if (res.status !== 200) return res;
    const out = new Response(res.body, res);
    out.headers.set('Content-Type', MARKDOWN_TYPE);
    return out;
  }

  const mdPath = MARKDOWN_FOR[path];
  if (!mdPath) return next();

  if (prefersMarkdown(request.headers.get('Accept'))) {
    const md = await next(new Request(new URL(mdPath, url), request));
    if (md.status === 200) {                              // missing twin degrades to HTML
      const out = new Response(md.body, md);
      out.headers.set('Content-Type', MARKDOWN_TYPE);
      out.headers.set('Content-Location', mdPath);
      out.headers.set('Vary', 'Accept');
      out.headers.set('Link', `<${mdPath}>; rel="alternate"; type="text/markdown"`);
      return out;
    }
  }

  const res = await next();
  const html = new Response(res.body, res);   // copy so the headers become mutable
  html.headers.set('Vary', 'Accept');
  html.headers.append('Link', `<${mdPath}>; rel="alternate"; type="text/markdown"`);
  return html;
};
```

Scope the middleware off static assets with `_routes.json` in the build output
(`public/_routes.json` for Astro, which copies it to `dist/`):

```json
{ "version": 1, "include": ["/*"], "exclude": ["/_astro/*", "/og.png", "/favicon.svg"] }
```

**Never put `/api/*` in `exclude`** — `_routes.json` controls which paths invoke
the Functions runtime at all, so excluding them stops your API from running.

**Other hosts:** Netlify → an edge function on the page routes; Vercel →
`middleware.ts` with a rewrite; nginx → `map $http_accept` + `try_files`. Same
shape: match `Accept`, rewrite to the `.md`, pin the content type.

## 5. Link headers (`linkHeaders`)

The scanner wants an agent-useful `rel` on the homepage; `alternate` satisfies it.
The middleware above already emits it. Also advertise in HTML for agents that
parse the body instead of headers:

```html
<link rel="alternate" type="text/markdown" href="https://example.com/index.md">
<link rel="alternate" type="text/plain" title="llms.txt" href="https://example.com/llms.txt">
```

And point `llms.txt` page links at the `.md` variants.

## 6. Gotchas

- **`Vary: Accept` is mandatory** on both representations. Without it a CDN
  caches whichever was requested first and serves it to everyone — a human gets
  raw Markdown, or the scanner gets HTML and fails intermittently.
- **Directory-format static sites 308-redirect** `/about` → `/about/`. Negotiation
  still works (middleware intercepts first), but check whether `<link rel=canonical>`
  matches the redirect target.
- **Verify against the real runtime**, not just a build: `wrangler pages dev dist`
  then curl each case — scanner `Accept: text/markdown`, a real browser Accept
  string, bare `*/*`, no Accept header, direct `.md`, trailing slash, and a POST to
  any existing API route to confirm the middleware passes it through.
- **Re-scan after deploying**, then report the pass/fail delta from `scan.json` —
  not the web page.
