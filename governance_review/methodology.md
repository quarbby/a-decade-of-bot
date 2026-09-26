# Search Methodology

**Date run:** 2026-08-13
**Reviewer:** automated search run by Claude Code, per instructions in `from_catherine.md`

## Databases

Catherine's instructions specified SCOPUS and Semantic Scholar. **This environment has no
SCOPUS access.** Per the fallback instructions in `from_catherine.md` ("if you cannot [access
Scopus], use Semantic Scholar to do the portion on Scopus, or use Google Scholar"), both SCOPUS
queries were run against the **Semantic Scholar Graph API** instead. Google Scholar was not used
because it does not offer a stable API and is aggressively bot-blocked, which conflicts with the
"don't get blocked by the websites" instruction — a real API with a supplied key was the more
reliable and reproducible substitute.

All calls used the Semantic Scholar Graph API (`api.semanticscholar.org/graph/v1`) with the API
key provided in `from_catherine.md`, a minimum 3.5-6 second delay between requests, and
exponential backoff retry on HTTP 429 (rate limit) responses, to avoid being throttled/blocked.
Full request log: `run_search.log`.

## 1. SCOPUS-substitute queries (via Semantic Scholar)

Semantic Scholar's relevance-search endpoint does not support boolean `AND`/`OR` syntax — a
single query string containing the full SCOPUS boolean expression returned 0 hits. To
approximate each SCOPUS boolean query's intent, each was decomposed into a "bag of sub-queries"
(one plain-language query per bot-related term, paired with the platform/governance context
terms), each sub-query was run separately (15 results each, 2016-2026 filter), and results were
merged and deduplicated by paper ID.

### Query 1 — bot-related + platform + governance keywords
Original SCOPUS boolean (from `from_catherine.md`, ~200 hits on SCOPUS since 2016):
```
("social bot*" OR "social media bot*" OR "automated account*" OR "sybil" OR "malicious bot*"
OR "spam bot*" OR "coordinated inauthentic behavio*" OR "astroturfing" OR "sockpuppet*"
OR "computational propaganda" OR "troll farm*" OR "state-sponsored troll*"
OR "foreign influence operation*" OR "inauthentic behavior")
AND ("social media" OR "platform" OR "online platform*")
AND ("governance" OR "regulation" OR "regulat*" OR "accountability" OR "self-regulation"
OR "co-regulation" OR "oversight" OR "policy" OR "policies" OR "liability" OR "compliance"
OR "audit*")
```
Decomposed into 14 Semantic Scholar sub-queries (see `raw_results/scopus_query1_bot_platform_governance_subquery_counts.json`).
**Result: 173 unique papers** (raw, pre-screening) → `raw_results/scopus_query1_bot_platform_governance_merged.json`

### Query 2 — specific platform governance laws + bots
Original SCOPUS boolean (~97 hits on SCOPUS):
```
("social media platform*" OR "online platform*") AND ("Digital Services Act"
OR "Online Safety Act" OR "platform liability" OR "intermediary liability" OR "NetzDG")
AND ("bot*" OR "automated account*" OR "coordinated inauthentic behavio*"
OR "computational propaganda" OR "disinformation" OR "manipulation")
```
Decomposed into 9 Semantic Scholar sub-queries (see `raw_results/scopus_query2_platform_laws_bots_subquery_counts.json`).
**Result: 115 unique papers** (raw, pre-screening) → `raw_results/scopus_query2_platform_laws_bots_merged.json`

## 2. Native Semantic Scholar phrase searches

Run as specified: date filter 2016-present, sorted by relevance (S2 API default ranking for
`/paper/search`), top 20 results per phrase, then filtered to fields of study in
{Computer Science, Political Science, Law, Sociology} (papers with no field-of-study tag were
kept for manual review rather than dropped).

| Phrase | Raw hits | After field-of-study filter |
|---|---|---|
| social media bot governance regulation | 20 | 18 |
| coordinated inauthentic behavior platform accountability | 20 | 19 |
| bot regulation self-regulation co-regulation | 20 | 10 |
| Digital Services Act platform liability bots | 20 | 18 |

Files: `raw_results/s2_phrase{1-4}_..._filtered.json`

## 3. Forward and backward citation chaining

- **Seed 1** — Bodó, "Not all who are bots are evil: A cross-platform analysis of automated
  agent governance" (New Media & Society), resolved via DOI `10.1177/14614448221079035` given
  in `from_catherine.md`. 66 backward references + 9 forward citations pulled.
- **Seed 2** — `policyreview.info/concepts/algorithmic-governance`. This is an Internet Policy
  Review **glossary/concept page**, not a discrete indexed paper — it has no DOI and no match in
  Semantic Scholar's paper index, so no citation chaining was possible for it. Title-search
  candidates are saved in `raw_results/seed2_algorithmic_governance_title_search.json` for manual
  follow-up if Catherine wants a specific paper substituted.
- **Seed 3** — Gorwa, "The platform governance triangle: conceptualising the informal regulation
  of online content" (Internet Policy Review), matched in Semantic Scholar
  (paperId `dfb63a8b9b264cf1475948b17ac6eb7763ca0d6c`). 85 backward references + 191 forward
  citations pulled.

## Merge, deduplication, and screening

All results (2 SCOPUS-substitute groups + 4 phrase searches + citation-chain references/citations)
were merged and deduplicated by Semantic Scholar `paperId`:

- Unique papers from direct database search: **323**
- Additional candidate papers from citation chaining (pre-dedup against the above): **351**
- **Total unique papers across all sources: 621**

Screening criteria applied (see `build_report.py`):
- **Inclusion:** publication year 2016 or later (papers with no year metadata kept for manual check)
- **Inclusion:** title/abstract must contain at least one bot-related term (bot, sybil,
  sockpuppet, astroturf, troll farm/factory, inauthentic, computational propaganda, automated
  account, fake account) **and** at least one governance-related term (govern, regulat, polic,
  accountab, liability, compliance, audit, oversight, law, legal, self-regulat, co-regulat).
  Papers with no abstract text were kept for manual screening rather than auto-excluded.

Results:
- Excluded, published before 2016: **50**
- Excluded, off-topic by keyword screen: **460**
- **Included in final annotated bibliography: 111**

This is an automated *first-pass* keyword screen equivalent to title/abstract screening — it is
deliberately permissive (kept ambiguous/no-abstract records) rather than aggressively precise, so
some borderline or off-topic items may remain in `search_results.md` for Catherine's manual
review, and some true positives may have been filtered by `excluded_off_topic` if their
abstract/title didn't literally contain a listed term. `excluded_papers.json` preserves everything
that was cut, for manual audit.

## Files in this folder

- `methodology.md` — this file
- `search_results.md` — annotated bibliography of the 111 included papers, sorted by citation count
- `included_papers.json` / `excluded_papers.json` — machine-readable screening output
- `merged_deduplicated.json` — all 621 unique papers pre-screening, with provenance (`_sources`)
- `run_search.py` / `build_report.py` — the scripts used (rerunnable)
- `run_search.log` — full request log, including rate-limit backoff events
- `raw_results/` — raw JSON from every individual query/phrase/citation-chain call
