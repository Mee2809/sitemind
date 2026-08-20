# SiteMind — public forms

Static forms served by GitHub Pages at **https://mee2809.github.io/sitemind/**.
Superintendents and foremen at Prospect Waterproofing use these on their phones to
request and manage rental equipment.

> **This repo is public.** Keep infrastructure detail out of it — no Airtable IDs,
> no workflow IDs, no access codes, no credentials. Internal documentation lives in
> the private **`Mee2809/sitemind-ops`** repo, which also holds the fuller working
> guide, the runbook, and the n8n workflow snapshots. Read that one first.

## Files

| File | Purpose |
|---|---|
| `request.html` | new rental request → AI review step → confirm |
| `manage.html` | cancel, reschedule, call off rent, log a weather day |
| `quote.html` | shop a quote across several rental companies |
| `index.html` | landing page |
| `opt-in.html`, `privacy.html`, `terms.html` | required for SMS compliance — do not delete |
| `robots.txt` | keeps the three forms out of search results |

## How they work

Each form is a single self-contained HTML file — styles and JavaScript inline, no
build step, no framework, no dependencies. They collect input, POST it to a webhook,
and render whatever comes back. **All logic lives server-side**, including access
code validation. Never move that check into the page.

`request.html` is a two-step flow: submit runs an AI pass and returns a summary for
the user to review, then confirm actually saves it. Nothing is written until the
person confirms.

## House rules

**Every input needs `font-size: 16px`.** Anything smaller makes iOS Safari zoom in
when tapped, which throws off the layout and is miserable one-handed on a job site.

**Keep `<meta name="viewport" content="width=device-width, initial-scale=1">`.**
Without it phones render at ~980px and scale down, which is what made these forms
unusable before.

**Buttons need `min-height: 48px`.** These get tapped with work gloves on.

**Mobile breakpoint is 600px**, and multi-column layouts collapse to one column
below it.

**Test at 375px wide before pushing.** Most submissions come from a phone.

## Deploying

```bash
git add -A && git commit -m "..." && git push origin main
```

Pages rebuilds in roughly 45 seconds. Verify against the live URL before calling it
done — the old file is served for a while after the push:

```bash
curl -s -H 'Cache-Control: no-cache' \
  https://mee2809.github.io/sitemind/request.html | grep -c 'the thing you changed'
```

Phones cache hard; a hard refresh or a private tab is usually needed to see changes.
