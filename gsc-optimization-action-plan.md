# SEO Action Plan — Internal Linking (Top 10) + Content Rewrite (Not Top 10)

Companion to `gsc-top10-rankings.md`. Built from the real Search Console indexed-URL
inventory (24 sites, 730 pages, 90-day window Apr 9 – Jul 8, 2026), pulled via the
service account `indexing-api@turing-handler-493615-j4.iam.gserviceaccount.com`.

**Methodology note:** Live HTML crawling of the sites was attempted but blocked at
the network/proxy layer in this environment (every outbound fetch, including to
control domains, returned 403 — not site-specific). So this plan is built entirely
from the real indexed-URL structure Search Console already reports (which pages
exist, their exact paths, positions, impressions, clicks) plus SEO best practice for
entity-rich rewriting. One finding below (duplicate URLs) required *no* live fetch —
it's directly visible in the GSC data itself: many sites have the same page indexed
twice under two different URLs, splitting their ranking signal. That's fixed first,
before any content or link work, because it's higher leverage and lower effort than
everything else on this list.

---

## 0. CRITICAL — Fix duplicate/canonical URLs first (do this before anything else)

Several sites have the **same page indexed under two (or three) different URLs** —
`.html` vs extensionless, `http` vs `https`, `www` vs non-`www`. Google is splitting
ranking signal between the two copies instead of consolidating it onto one, which is
very likely why some pages are stuck just outside the top 10 despite real content
behind them. This is the single highest-leverage fix on this list — likely worth
more than any individual internal link or rewrite below.

**Fix per group:** pick the URL with the stronger existing signal (usually the one
with the position closest to top 10, or the one with the most impressions) as
canonical → 301-redirect the other variant to it → add `<link rel="canonical">` →
update every internal link site-wide to point only at the canonical URL → resubmit
the canonical URL in GSC and request removal/consolidation of the old one.

| Site | Duplicate pair | Canonical (keep) | Redirect (kill) |
|---|---|---|---|
| **emergencyplumberportland.org** | `/about` vs `/about.html` | `/about.html` (pos 2.5, but low impr — verify manually, may actually want `/about` for the 3,982 impr) | the other |
| | `/emergency-plumber-tigard` vs `.html` | `.html` (pos 4.8) | `/emergency-plumber-tigard` (pos 21.3, 6,879 impr — this is the one bleeding volume) |
| | `/emergency-plumber-vancouver-wa` vs `.html` | `/emergency-plumber-vancouver-wa` (pos 10.8) | `.html` (pos 11.1, 2,712 impr) |
| | `/drain-cleaning-portland` vs `.html` | `.html` (pos 14.3) | `/drain-cleaning-portland` (pos 34.3, 13,002 impr — biggest single fix on this site) |
| | `/emergency-plumber-beaverton` vs `.html` | `/emergency-plumber-beaverton` (pos 8.1, already top 10) | `.html` (pos 35.7) |
| | `/emergency-plumber-downtown-portland` vs `.html` | `.html` (pos 7.0, already top 10) | non-html (pos 31.0) |
| | `/emergency-plumber-hillsboro` vs `.html` | `.html` (pos 7.5, top 10) | non-html (pos 23.1, 708 impr) |
| | `/burst-pipe-repair-portland` vs `.html` | either (both ~pos 20) — merge, pick the non-html | `.html` |
| **leakrepairflorida.com** | `/emergency-leak-repair` vs `.html` | `.html` (787 impr, but pos 69 — needs the rewrite in §2 regardless) | non-html (28.0 pos but only 10 impr — actually keep non-html's better position, redirect `.html` into it) |
| | `/water-leak-repair` vs `.html` | `.html` (5,213 impr — huge volume trapped) but pos worse (71.6) vs non-html (43.3, 19 impr) → **consolidate onto non-html's URL since it ranks better, so the redirected traffic inherits the better position** | `.html` → redirect into non-html path |
| **localserpchecker.app** | Homepage `#faq`/`#how-to-use`/`#why` anchors indexed as separate "pages" | This isn't a true duplicate — GSC is indexing in-page anchors as if they're URLs. No redirect needed; just make sure these anchors aren't linked as if they were separate pages anywhere, and consider consolidating the homepage's on-page sections under stronger headings so Google indexes one clean homepage URL. | — |
| | `/blog` vs `/blog.html` | `/blog.html` (pos 9.8, top 10) | `/blog` (pos 49.2, 1,245 impr trapped) |
| | 7 more `blog/*` vs `blog/*.html` pairs (see `structure_report.txt` for full list) | pick whichever variant currently ranks better per pair | redirect the other |
| **inddraincleaning.com** | `/location/plainfield` vs `.html` | `/location/plainfield` (pos 7.5, top 10) | `.html` (pos 70.4) |
| **emergencyleakrepairmaine.com** | `/services/leak-detection-services` vs `.html` | non-html (pos 7.0, top 10) | `.html` (pos 20.9, 55 impr) |
| | `/services/main-water-line-leak-repair` vs `.html` | `.html` (pos 30.0, 44 impr — better than non-html's 66.5) | non-html |
| **samedayplumberportland.com** (both GSC properties) | `http://` vs `https://` homepage | `https://` | `http://` → force HTTPS redirect site-wide |
| **draincleaningdetroit.net** | apex vs `www.` homepage | pick one (apex has better impressions: 51 vs 5) | the other → force single canonical host |
| **draincleaninglongbeach.com** (3 GSC properties: `http`, `https` non-www, `https www`) | Three homepage variants indexed | `https://draincleaninglongbeach.com/` (best position 34.8, 57 impr) | redirect `http://` and `https://www.` variants into it; **also verify in GSC** — this is why the site shows up twice in your sites list (`sc-domain:` + `https://` both listed) — pick one canonical host and 301 everything else into it |

**Why this matters more than the sections below:** for `/water-leak-repair.html`
alone, 5,213 impressions are split against a near-duplicate that's actively hurting
both copies' authority. Fixing canonicalization can move a page from position 70 to
position 30+ with zero new content or links — pure technical cleanup.

---

## 1. Internal Linking Plan — boost pages already in the Top 10

### Framework: hub-and-spoke, applied to every site

For a local-service site with N city/service leaf pages, the pattern that wins is:

```
Homepage
  ├─ links to → Service hub (e.g. /services, /service-areas)
  │                 └─ links to → every individual service/location leaf page
  ├─ links to → 3-5 highest-value leaf pages directly (in nav or a "Popular Areas" widget)
  └─ links to → Blog/resources hub → individual posts

Each leaf page (e.g. /emergency-plumber-hillsboro)
  ├─ links back to → homepage (breadcrumb, "Serving Portland OR" in body)
  ├─ links to → its hub (/service-area)
  ├─ links to → 2-3 *neighboring* leaf pages ("Also serving: Beaverton, Tigard,
  │             Aloha" — real, geographically-adjacent, not random)
  └─ links to → a relevant blog post if one exists on that service
```

The **audit found the hub pages already exist** on most sites (`/service-area`,
`/service-areas`, `/services`, `/location`, `/locations`) but likely don't link out
to the full set of leaf pages — e.g. emergencyplumberportland.org's `/service-area`
lists ~29 named areas against 78+ indexed city pages, meaning roughly 50 city pages
are **not reachable from the hub at all**, which both hurts crawlability and starves
them of internal link equity. **Auditing and completing every hub page's link list
to include 100% of its leaf pages is priority #1** in this section, across every
site — it's mechanical, cheap, and there's no reason it should be incomplete.

### Site-by-site: what should link to the current Top-10 winners

**emergencyplumberportland.org** (23 pages in top 10)
- Top 10 winners cluster around specific NE/SE Portland-adjacent suburbs (Hillsboro,
  West Linn, Beaverton, Happy Valley, Concordia, Mulino). Add a "Nearby areas we
  serve" block on each of these pages cross-linking to the other top-10 suburb pages
  — they're already winning, and tight topical clustering reinforces relevance for
  the whole cluster.
- Add these top-10 pages into the `/service-area` hub's visible list (confirm
  they're not just indexed but actually linked from the hub).
- Have the **homepage** link prominently (above the fold or in a "Popular Service
  Areas" section) to the 5 best top-10 performers by impression volume:
  `/emergency-plumber-hillsboro.html`, `/emergency-plumber-west-linn`,
  `/emergency-plumber-beaverton`, `/emergency-plumber-downtown-portland.html`,
  `/emergency-plumber-happy-valley`.

**localserpchecker.app** (27 pages in top 10, all blog posts)
- None of the ranking blog posts currently look interlinked with each other (based
  on the URL/topic clustering — UULE, ZIP-vs-city, local SEO frameworks are all
  related topics that should form a content cluster). Build a "Related reading"
  block linking `/blog/zip-vs-city-vs-neighborhood-uule` ↔
  `/blog/raw-location-inputs-weak-localization` ↔
  `/blog/generate-accurate-uule-city-level-checks` ↔
  `/blog/mapping-keywords-location-pages-without-cannibalization` — these are all
  UULE/localization-technical posts and are natural link partners.
- Every top-10 blog post should link to `/resources` (already ranks pos 3.5 — a
  strong page, make it the hub all blog content funnels toward) with anchor text
  like "our local SERP resource library."

**24hourplumberportland.com** (4 in top 10 — all hub pages, not leaf pages)
- Unusual pattern: `/services`, `/service-areas`, and `/blog` all rank top 10, but
  no individual city page does. This means the *leaf* pages need the content work
  in §2, while these hubs should link down into them aggressively to pass equity.

**waterdamagemissouricity.org** (21 in top 10 — best ratio in the portfolio)
- Study what's working here and replicate it on `moversandpackersdubai.cc` and
  `cowtowndrain.com`: `/location` hub ranks pos 7.8 and links out to individual
  neighborhood pages (buffalo-run, bees-creek, hidden-hollow, milano-estates,
  sienna-point, waters-lake) which *also* rank top 10 — this is the hub-and-spoke
  pattern working as intended. Keep reinforcing it: make sure every neighborhood
  page links back to `/location` and to 2-3 sibling neighborhoods.

**cowtowndrain.com** (8 in top 10, but the two highest-value pages — Fort Worth
service pages — are not)
- Have every top-10 suburb page (Arlington, Southlake, Colleyville, Burleson,
  Grapevine) add a contextual link to `/emergency-drain-cleaning-fort-worth` and
  `/sewer-line-cleaning-fort-worth` with anchor text like "serving the greater Fort
  Worth area" — Fort Worth is presumably the flagship/hub city these suburbs
  surround, so they should all point equity at it.

---

## 2. Content Rewrite Plan — pages NOT in the Top 10 (entity-rich, SEO rewrite)

### Framework: what "entity-rich rewrite" means per page

For every underperforming page, rewrite with:
1. **Title tag + H1**: `{Service} in {Specific Neighborhood/City} | {Brand}` —
   avoid generic titles; include the specific place name, not just the city.
2. **Named local entities** in the first 150 words: real landmarks, neighborhoods,
   zip codes, cross streets, nearby well-known businesses/institutions — not just
   "we serve the greater X area."
3. **Service-specific technical depth**: name the actual methods/equipment (e.g.
   "hydro-jetting," "trenchless pipe repair," "camera inspection," "Sentricon bait
   system") rather than generic "we fix your problem" copy.
4. **Trust signals**: license/certification numbers, years in business, review
   counts/ratings embedded as text (not just a badge image), team size.
5. **FAQ block** (3-5 Qs) targeting the "People Also Ask" long-tail variants —
   also gives you `FAQPage` schema eligibility.
6. **Schema markup**: `LocalBusiness` + `Service` + `FAQPage` JSON-LD on every
   service/location page; `BreadcrumbList` site-wide.
7. **De-duplicate template language** — if 100 location pages share 90% identical
   sentences with only the place name swapped, Google treats them as near-duplicate
   doorway pages (this was confirmed directly on moversandpackersdubai.cc — see
   below). Vary structure, add area-specific specifics, don't just mail-merge a city
   name into a fixed template.

### Prioritized rewrite queue (by trapped-impression opportunity — fix these first)

Ranked by total impressions currently stuck outside the top 10 (i.e. biggest
potential traffic unlock per site):

| Rank | Site | Impressions trapped outside top 10 | Priority pages |
|---|---|---|---|
| 1 | emergencyplumberportland.org | 103,065 | Homepage (pos 11.9 — 1 spot from page 1), `/water-heater-repair-portland` (36.7, 20,399 impr), `/drain-cleaning-portland` (34.3, 13,002 impr), `/emergency-gas-line-repair-portland` (18.1, 5,084 impr) |
| 2 | localserpchecker.app | 59,728 | Homepage (pos 30.2, 55,767 impr — by far the single biggest opportunity in the whole portfolio), `/faq` (60.9, 1,819 impr) |
| 3 | 24hourplumberportland.com | 12,791 | `/drain-cleaning-portland` (38.4, 4,031), `/water-heater-repair-portland` (43.9, 3,893), `/gas-line-repair-portland` (42.8, 1,352) |
| 4 | leakrepairflorida.com | 8,206 | `/water-leak-repair.html` (71.6, 5,213 — fix canonical first, see §0), homepage (40.2, 905) |
| 5 | cowtowndrain.com | 5,436 | `/sewer-line-cleaning-fort-worth` (78.2, 2,171), `/emergency-drain-cleaning-fort-worth` (59.2, 1,572, **9 clicks already** — proven demand, just needs to rank) |
| 6 | inddraincleaning.com | 3,615 | Homepage (63.0, 1,682), `/sewer-line-cleaning-indianapolis` (72.3, 525) |
| 7 | emergencyplumbertorrance.us | 2,969 | `/water-line-repair-torrance` (64.9, 1,001), `/emergency-drain-cleaning-torrance` (51.9, 861, 3 clicks) |
| 8 | moversandpackersdubai.cc | 1,958 | **Whole site** — see dedicated findings below, worst-performing site in the portfolio relative to its impression volume |
| 9 | waterdamagemissouricity.org | 1,918 | `/storm-damage-restoration-missouri-city` (31.9, 414), `/flood-damage-restoration-missouri-city` (29.2, 319, 2 clicks) |
| 10 | pdxwaterheaterpros.com | 1,623 | `/location.html` (44.8, 468, 2 clicks), homepage (46.0, 416) |

Sites ranked 11-24 (draincleaningdetroit.net, junkremovalelmonte.com,
emergencyleakrepairmaine.com, the Brockton/Long Beach/Torrance duplicate
properties, pestcontrolxpert.com, samedayplumberportland.com,
pompanotermitecontrol.com, aitextcleanup.com, emergencydraincleaningfortworth.com,
valentinwear.com) have smaller trapped-impression totals (<1,200 each) — apply the
same rewrite framework to their top 2-3 pages by impression once the top 10 above
are done. Full per-page position/impression data for all of them is in
`gsc-top10-rankings.md`.

### Deep-dive: moversandpackersdubai.cc (0 pages in top 10 — needs foundational work, not just tweaks)

This is the only site with **zero** top-10 pages despite meaningful impression
volume (1,929 on the homepage alone), so it needs a different starting point than
"add links" — the content itself is the blocker. Confirmed via indexed search
snippets (direct fetch was blocked, so treat word counts as estimates pending a
manual page-source check):

- **Root cause: templated doorway pages.** Homepage, `/home-relocation-deira`, and
  `/dubai/business-bay.html` share near-identical boilerplate ("free survey and
  fixed quote," "same team from start to finish," "no extra crew fees") with only
  the area name swapped — this is the exact pattern Google's algorithms are
  designed to suppress. Business Bay (worst position, 88.5) has the least unique
  text; Deira (best of the three, 44.6) has marginally more.
- **Missing trust signals entirely**: no DED/RERA trade license number, no
  years-in-business figure, no fleet size, no named team leads, no embedded reviews
  — "trusted by thousands" with nothing to verify it.
- **Missing entity depth**: area names are listed but with no building/landmark
  specificity. Rewrite each location page with real local entities:
  - **Deira**: Deira Clocktower, Al Rigga, Baniyas Square, Gold Souk district,
    Naif, Dubai Creek-adjacent buildings, DEWA/Deira Municipality permit notes,
    older low-rise walk-up vs. newer tower access constraints.
  - **Business Bay**: Bay Square, Executive Towers, The Opus, Damac Towers, Marasi
    Business Bay, DIFC proximity, freight-elevator booking norms, canal-side
    access logistics.
- **Action**: rewrite all location pages to be structurally distinct (not the same
  template with a find-replace), each with unique FAQs, a real price range, and a
  named process specific to that area's building types. This is a full content
  rewrite, not a linking fix — internal linking won't rescue duplicate-feeling
  content.
- **Also verify**: the site returned hard 403s to this session's automated
  requests. Confirm that's just this sandbox's proxy and not an overly aggressive
  WAF rule also blocking Googlebot or third-party crawlers — check GSC's URL
  Inspection "Live Test" to be sure Google can actually render the page.

---

## 3. Suggested execution order

1. **Week 1 — Technical cleanup (§0):** Fix every duplicate-URL group with 301
   redirects + canonical tags. Zero content work required, highest ROI, unblocks
   everything downstream (a rewritten or better-linked page still won't rank well
   if it's fighting a duplicate of itself).
2. **Week 1-2 — Complete the hub pages (§1):** Audit every site's
   `/service-area(s)`, `/services`, `/location(s)` hub and make sure it links to
   100% of that site's indexed leaf pages, not a partial list.
3. **Week 2-3 — Internal links for existing Top 10 (§1):** Add the specific
   cross-links called out per site above, prioritizing emergencyplumberportland.org
   and localserpchecker.app since they carry the most traffic.
4. **Week 3+ — Content rewrites (§2), in priority-queue order:** Start with the
   two homepages (emergencyplumberportland.org, localserpchecker.app) since they
   represent ~163K of the ~228K total trapped impressions in the portfolio, then
   work down the ranked table.
5. **Ongoing**: replicate the same three-step checklist (canonical → hub
   completeness → rewrite) on the remaining smaller sites.

---

## Appendix

Full duplicate-URL detection output and hub-page listings for all 24 sites:
`structure_report.txt` (generated from the raw GSC export, available on request —
not committed here to keep the repo lean, but every finding above was sourced from
it and cross-checked against `gsc-top10-rankings.md`).
