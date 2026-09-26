# Bot governance & platform regulation — literature review

Companion page to the main [social bots review](../index.html), scoped to the legal/policy
literature: bot typologies as framed by regulators, and how platform governance of bots has
shifted from self-regulation to co-regulation to hard law (DSA, NetzDG, etc.), 2016-2026.

Open `index.html` in this folder directly, or via the "Related" link near the top of `../index.html`.

Source data: `../../governance_review/included_papers.json` (111 papers from the SCOPUS-substitute
+ Semantic Scholar + citation-chaining search documented in `../../governance_review/methodology.md`).

## How this page's data was built

Unlike the main site's OpenAlex-scale scan, `governance_review`'s keyword screen was deliberately
permissive (see `methodology.md`), so roughly a fifth of the 111 papers that passed it are false
positives on stray term matches (e.g. veterinary antibiotic regulation, rideshare economics). Each
of the 111 was re-read (title + abstract) and tagged with:

- **on-topic / excluded** — is this substantively about social media bot governance?
- **bot type(s)** — harmonized into 7 categories used across the legal/policy literature
  (political/propaganda bots, coordinated inauthentic behavior networks, engagement-for-hire bots,
  platform/community-management bots, conversational & companion AI, LLM-generated bots,
  fake/fraudulent accounts)
- **governance model(s)** — 8 categories (statutory/hard law, self-regulation, co-regulation,
  content moderation/enforcement, detection/technical, criminal liability, transparency/disclosure,
  governance theory/free-expression critique)
- **governance actor(s)** — platform, government, civil society

Tags live in `papers.js` (`window.GOV_PAPERS`); year-by-category aggregates for the charts live in
`temporal.js` (`window.GOV_TEMPORAL`). Both are hand-generated data, not a build artifact — there's
no build step, same as the main site.

The "How the governance model changed" timeline on the page has, per era, an expandable
"Further details" panel with the actual papers (author/year, DOI/Semantic Scholar link) and actual
platform/government primary sources (official bill text, EUR-Lex, transparency-center policy pages,
etc.) that era's claim rests on — each link was checked against a live web search, not guessed.

## Publish on GitHub Pages

Same static-site pattern as the parent folder — this directory just needs to travel with it
(`docs/social_bots_review/governance_review/` or wherever the parent lands).
