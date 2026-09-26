# Social media bots, 2016-2026 -- annotated literature review

To open, please go to: [https://quarbby.github.io/bot_review](https://quarbby.github.io/bot_review)

Static site, no build step. `index.html` + `papers.js` + `temporal.js` + `prisma_flow_social_bots.svg`.

## Companion page: bot governance & platform regulation

`governance_review/index.html` is a separate page scoped to the legal/policy literature (111
papers): bot typologies as framed by regulators, and how platform governance has shifted from
self-regulation to co-regulation to hard law (DSA, NetzDG, etc.), 2016-2026, with an expandable
"further details" panel of real cited papers and platform/government primary sources for each era.
Linked from the "Related" line near the top of `index.html`. See `governance_review/README.md`.

## Publish on GitHub Pages
1. Commit this whole folder to your repo (e.g. as `docs/social_bots_review/`), including the
   `governance_review/` subfolder — both pages are self-contained and push together as one folder.
2. Repo Settings -> Pages -> Source: Deploy from branch -> Branch: `main`, folder `/docs`.
3. Site appears at `https://<user>.github.io/<repo>/social_bots_review/` (governance page at
   `.../social_bots_review/governance_review/`).

(If you want it at the repo root instead, move this folder's contents to `/docs/` directly, or to a
separate `gh-pages` branch root, and adjust the Pages source accordingly.)
