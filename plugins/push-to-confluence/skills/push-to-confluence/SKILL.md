---
name: push-to-confluence
description: Push a local Markdown file to a Confluence page, expanding anchor links to full URLs in memory without touching the local file. Pass args as "local_file=<path> confluence_url=<url>".
---

Push a local Markdown file to Confluence. The caller passes two parameters:
- `local_file` — absolute path to the local `.md` file (source of truth)
- `confluence_url` — full URL of the target Confluence page (e.g. `https://acme.atlassian.net/wiki/spaces/PROJ/pages/123456/Page+Title`)

If either parameter is missing, ask the user before proceeding.

## Steps

1. **Parse the Confluence URL** to extract:
    - `cloudId` — the hostname (e.g. `acme.atlassian.net`)
    - `pageId` — the numeric segment after `/pages/`
    - `title` — the URL-decoded slug after the page ID, with `+` replaced by spaces (e.g. `Page Title`)
    - `baseAnchorUrl` — the full URL with a trailing `#` (used for anchor expansion)

2. **Read** the file at `local_file` into a variable. Do not write to it again during this operation.

3. **Transform** the content in memory only — never write the transformed content to disk:

   Replace every internal anchor link by running this substitution:

   ```python
   import re

   BASE = "<baseAnchorUrl from step 1>"

   def to_confluence_anchor(anchor):
       return '-'.join(seg.capitalize() for seg in anchor.split('-'))

   def replace_anchor(m):
       return '](' + BASE + to_confluence_anchor(m.group(1)) + ')'

   result = re.sub(r'\]\(#([^)]+)\)', replace_anchor, content)
   ```

   This turns `[Core Concepts](#core-concepts)` into `[Core Concepts](https://...#Core-Concepts)`. Confluence preserves heading capitalization in anchor IDs; GitHub-style slugs (all lowercase) do not resolve.

4. **Push** with the Atlassian MCP server's `updateConfluencePage` tool. Its full name depends on what the server is called in this session (e.g. `mcp__atlassian__updateConfluencePage`), so look for the tool ending in `updateConfluencePage`. If no Atlassian MCP server is connected, stop and tell the user. Pass:
    - `cloudId`: from step 1
    - `pageId`: from step 1
    - `title`: from step 1
    - `contentFormat`: `markdown`
    - `body`: the transformed content from step 3
    - `versionMessage`: a short description of what changed (ask the user if not obvious)

5. **Verify** the local file is unchanged: reread it and confirm it still contains `](#` (short anchors), not the full Confluence URLs.

6. Report the new Confluence version number.

## What NOT to do

- Do not write the transformed content back to the local file or any other local file.
- Do not push the raw file content — the anchor links will render as dead links in Confluence.
- Do not skip the capitalization step — `#core-concepts` does not resolve in Confluence; `#Core-Concepts` does.
