# Competitive + Improvement Analysis — open-gardening-guides

Region-aware, climate-zone-correct, open-licensed food-gardening guidance for new and low-resource
growers. Analysis of `PLAN.md` v0.1.0 (M0–M3, donated lane, low risk with medium/high carve-outs),
grounded in web research with cited sources.

---

## 1. Correctness & completeness review of PLAN.md

The plan is unusually rigorous for a "low-risk" project: it correctly reframes region-correctness as a
*safety property*, mandates a deterministic (no-LLM-in-runtime) engine with `derivedFrom` provenance,
and treats invasive-species screening, toxic/foraging/pesticide carve-outs, and license cleanliness as
hard gates. The honest "no partner secured / `verifiedNeed: false`" posture is good practice. The
following are gaps, risks, or sharpenings — ordered roughly by importance.

**A. Region / climate-zone dependence — the cardinal accuracy issue (mostly right, but with traps).**
The plan's instinct is correct: "plant tomatoes in spring" is useless-to-harmful when "spring" varies
by hemisphere and latitude. But several technical traps are under-specified:

- **USDA hardiness zones are US-only and measure only average annual *extreme minimum temperature*** —
  i.e. cold-hardiness (winter survival of perennials), **not** when to sow annual vegetables. Most of
  this project's value (sowing/transplant/harvest windows for annual food crops) is driven by **frost
  dates, soil/germination temperature, season length, day-length, and heat**, *not* by hardiness zone.
  The plan mostly gets this (germination-temp + last-frost rules in §6), but the data model leads with
  `hardinessZone` and the open questions propose "US USDA zones" as the de-risking launch region. Risk:
  conflating "hardiness zone" with "planting zone." The schema should make explicit that hardiness zone
  is largely irrelevant to annual-vegetable sowing windows and is **undefined outside the US**.
- **USDA zones shifted warmer and are a moving target.** The 2023 USDA map (first update since 2012) is
  **~2.5°F warmer** than 2012 across the contiguous US, with **about half the country moving up a half
  zone**, built on 1991–2020 data and 13,412 stations (vs 7,983 prior)
  ([USDA ARS](https://www.ars.usda.gov/news-events/news/research-news/2023/usda-unveils-updated-plant-hardiness-zone-map/),
  [NPR](https://www.npr.org/2023/11/17/1213600629/-it-feels-like-im-not-crazy-gardeners-arent-surprised-as-usda-updates-key-map)).
  Notably, USDA itself **downplays the climate-change attribution**, citing extreme-minimum volatility
  and methodology changes
  ([Civil Eats](https://civileats.com/2024/01/29/the-usda-updated-its-gardening-map-but-downplays-connection-to-climate-change/)).
  The plan's "climate-shift staleness" risk row (High likelihood) is justified; it should also pin the
  **exact map vintage** (2023) per unit and note that zone boundaries themselves are versioned data,
  not constants.
- **Frost dates are statistical, not deterministic.** Almanac-style "last frost date" is typically a
  30-year average / probability (e.g. 50% or 10% exceedance). The plan's "ranges + uncertainty, no
  false precision" stance is correct but should require **stating the probability threshold** (a 50%
  last-frost date will burn tender seedlings half the time). This is an accuracy nuance worth an ADR-2
  line.
- **Southern Hemisphere + tropics + day-length.** Good that anti-North-bias is a goal (M2 requires a
  non-temperate-North region). But the engine's "after last frost" rule **fails in the tropics**, where
  the limiting factor is the **wet/dry season (rainfall regime), not frost**, and in long-day high
  latitudes where photoperiod governs bolting/bulbing (onions, spinach). The rule set in ADR-2 must be
  **regime-typed** (frost-limited vs rainfall-limited vs heat-limited vs photoperiod-limited), or the
  engine will silently emit nonsense for half the globe. This is the single biggest engine-correctness
  risk and is currently implicit.

**B. Horticultural accuracy / engine.** The golden-calendar determinism tests, source-divergence
precedence (jurisdiction-first), and `approvedBy != reviewedBy` credential bar are strong. Gap: the
plan treats "days-to-maturity" as a crop constant, but DTM is **temperature-dependent** (slower in cool
seasons) and cultivar-dependent — the engine should treat it as a range conditioned on zone, not a flat
number, or harvest-month outputs will drift. Also missing: **succession-planting** and **last-sow-date**
logic (sowing too late to mature before first frost is a classic beginner failure and a real food loss).

**C. Invasive-species + toxic-plant + pesticide safety.** Well-handled in principle. Refinements:
- The invasive screen needs a **named, licensed source per region**, not just "the region's list."
  The IUCN/ISSG **Global Invasive Species Database (GISD)** is free and redistributed via GBIF and the
  CABI data repository ([GISD on GBIF](https://www.gbif.org/dataset/b351a324-77c4-41c9-a909-f30f77268bc4),
  [CABI repo](https://ckan.cabi.org/data/dataset/gisd-global-invasive-species-database)) — a good
  global baseline — but **regulatory** noxious-weed lists are national/sub-national (US APHIS + state
  lists, EU/EPPO, AU state lists). The plan should specify that **legal restriction ≠ ecological
  invasiveness** and that *both* are screened; a crop can be legal but invasive (e.g. mint, some
  brassicas, horseradish, certain squashes) — the guide should warn even where not banned.
- **Toxic-plant caution for *cultivated* crops** is under-covered. The carve-out (no foraging) handles
  wild-plant edibility, but several *deliberately grown* food crops have toxic parts: **rhubarb leaves,
  green/sprouted potatoes (solanine), raw kidney beans (lectins), elderberry, cassava (cyanogenic —
  globally critical for food security), apple/stone-fruit seeds, tomato/potato foliage**. A beginner-
  facing food guide that omits "which parts are NOT edible / require processing" has a genuine
  poisoning gap. Recommend a mandatory `toxicityNotes` field on `CropProfile` (cultivated-crop scope,
  expert-reviewed) rather than relying solely on the foraging carve-out.
- Pesticide stance (IPM/prevention only, link to label + regulator, no rates) is correct and well
  defended.

**D. Expert / extension review + currency.** The credential bar (extension staff / agronomist /
certified Master Gardener for the *region*) is excellent. Two gaps: (1) sourcing region-qualified
volunteer reviewers for **Global-South / Southern-Hemisphere** regions will be much harder than for US
extension — the plan should acknowledge this as a **coverage bottleneck**, possibly partnering with FAO
/ ECHO networks (see §2). (2) The annual re-review cadence is good, but there's no **trigger-based**
re-review for when a source dataset is re-released (e.g. next USDA map, or a new FAO crop-calendar
version) — currency should be event-driven, not just calendar-driven.

**E. Accessibility / offline / scope.** WCAG 2.2 AA + manual AT matrix, offline PWA, print-snapshot,
low-bandwidth budget, zero-telemetry (CSP `connect-src 'none'` + runtime interception E2E) are all
strong and appropriate for low-resource beneficiaries. Scope is well-bounded. Minor: "printable PDF"
for low-literacy / low-resource users would benefit from an explicit **pictographic / low-text** mode
goal (icons for sow/transplant/harvest), not just translated text — currently implicit.

**F. Completeness nits.** (1) No explicit handling of **microclimate / container / urban small-space**
growing, which is exactly the beneficiary context (balconies, raised beds, food-bank plots) — the
"region → zone" model assumes in-ground field conditions. (2) No **seed-sourcing / seed-viability**
guidance, a real barrier for low-resource growers (ties to ECHO seed banks). (3) Success metric
"≥ 2,000 households" via partner self-report is honest but fragile with no partner secured — the
"Publicly Shipped (generic public good)" fallback partly addresses this.

---

## 2. Competitive landscape

| Competitor | What it is | Strengths | Weaknesses vs. our project |
| --- | --- | --- | --- |
| **University Cooperative Extension** (US land-grant) | Authoritative regional planting guides/calendars | The **gold standard** for accuracy; region-specific; many are **reusably licensed** — e.g. Virginia Cooperative Extension materials are explicitly "available for public use, reprint, or citation… with credit" ([VCE](https://www.pubs.ext.vt.edu/AREC/AREC-66/AREC-66.html)); UMD, UMaine, Arizona publish free calendars | **Fragmented** across hundreds of institutions; **US-only**; PDF-centric, not structured data; **license varies per document** (some PD/CC, some all-rights-reserved); not offline/i18n; no unified schema |
| **Old Farmer's Almanac** (almanac.com) | Free online planting calendar + frost-date calculator **by ZIP** | Frictionless ZIP→dates UX; 30-yr-average frost dates from nearest station; huge brand trust ([Almanac planting calendar](https://www.almanac.com/gardening/planting-calendar), [frost dates](https://www.almanac.com/gardening/frostdates)) | **Proprietary/all-rights-reserved**; **US-centric** (ZIP-based); ad-supported/commercial; not offline; no structured open dataset; no invasive screening; no i18n |
| **Gardenate** (gardenate.com / app) | Climate-zone kitchen-garden calendar + reminders | Real **multi-country zone model** (AU, CA, NZ, ZA, UK, USA), 90+ veg/herbs; closest analog to our UX ([gardenate.com](https://gardenate.com/), [zones](https://gardenate.com/zones/USA+-+Zone+6b)) | **Proprietary**, paid ($1.99 app), **not updated since ~2019**, rated ~3.1/5, missing many crops; **6 anglophone/temperate countries only** — no tropics/Global South; closed data |
| **RHS** (rhs.org.uk) | UK's leading horticultural charity advice | Deep, expert, trusted plant advice | **Strict copyright** — "no material may be used without written permission," personal non-commercial viewing only ([RHS terms](https://collections.rhs.org.uk/terms-of-use)); **UK-centric**; link-only for us |
| **FAO Crop Calendar + FAOSTAT/GAEZ** | Global crop sowing/harvest calendars | **100+ crops across 50+ countries**, agro-ecological-zone aware, **openly licensed** — dataset is **CC BY 3.0 IGO / Open Data** ([FAO catalog](https://data.apps.fao.org/catalog/dataset/crop-calendar-by-country-crop-and-activity), [tool](https://cropcalendar.apps.fao.org/)) | **National-scale & staple/field-crop oriented** (not garden-bed granularity); not beginner-facing; no offline/print kit; coarse for a home grower's microclimate |
| **ECHO / ECHOcommunity** | Tropical smallholder ag NGO + global seed bank | Exactly our **Global-South / food-security** niche; seed banks, appropriate tech, underutilized crops ([ECHOcommunity](https://www.echocommunity.org/), [seed bank](https://echonet.org/echo-news/growing-beyond-free-seeds-the-importance-of-community-seed-banks-from-trial-packets-to-local-food-security/)) | Network/PDF resources, **not a structured region-aware tool**; **potential partner, not a competitor** |
| **PFAF (Plants For A Future)** | DB of 8,000+ useful/edible plants | Large; perennial/permaculture focus; free to access ([pfaf.org](https://pfaf.org/), [Wikipedia](https://en.wikipedia.org/wiki/Plants_for_a_Future)) | **Temperate-biased** (7,000 temperate / 1,000 tropical); **license unclear/not open**; species reference, **not** a planting-calendar/when-to-sow tool |
| **iNaturalist / GBIF / GISD** | Biodiversity + invasive-species data | GISD is **free, IUCN-managed**, on GBIF/CABI — usable for the **invasive screen** ([GISD/GBIF](https://www.gbif.org/dataset/b351a324-77c4-41c9-a909-f30f77268bc4)) | Not gardening guidance; a **data input**, not a competitor |
| **Köppen-Geiger climate datasets** | Climate classification maps | Beck et al. 2023 1-km maps are **CC BY 4.0**, with future-climate projections ([GloH2O/Köppen](https://www.gloh2o.org/koppen/), [Nature Sci Data](https://www.nature.com/articles/s41597-023-02549-6)) | A **data input** for the zone engine, not a competitor |

**Net:** No existing product is simultaneously **(a) open-licensed, (b) globally/region-aware beyond
the anglophone temperate world, (c) beginner-focused, (d) offline/printable, and (e) structured open
data with provenance.** Extension = accurate but US-only/fragmented/PDF. Almanac/Gardenate = good UX but
proprietary, US/anglophone, and Gardenate is stale. FAO = open + global but national-scale, not
beginner garden guidance. That intersection is the wedge.

---

## 3. Gaps we can fill

1. **A single open, structured, machine-readable dataset** (crop × zone × calendar with citations) —
   nobody offers this under CC-BY/CC0; extension/almanac/RHS are PDFs/HTML behind copyright.
2. **Global, hemisphere- and regime-aware coverage** — fill the **Global-South / Southern-Hemisphere /
   tropical** void that Gardenate, Almanac, and PFAF largely ignore (rainfall-driven, not frost-driven).
3. **Beginner-first framing** with explicit failure-avoidance (last-sow dates, what-not-to-eat, invasive
   warnings) — extension/FAO assume baseline knowledge.
4. **Offline + printable + low-bandwidth + accessible** delivery for exactly the low-connectivity,
   low-end-device, paper-handout beneficiaries no commercial app serves.
5. **License-clean reuse base** — a CC-licensed corpus partners can localize, unlike RHS/Almanac/PFAF.
6. **Provenance & auditability** — every date traceable to a cited source + rule, vs. opaque app
   black-boxes.
7. **Multilingual with binomial-anchored crop names** — preventing the toxic-plant-name-confusion
   failure mode that English-only resources can't address.

---

## 4. Differentiators to win

1. **Deterministic, source-cited, expert-reviewed engine** (no LLM in the runtime path) — *auditable
   correctness* as the brand, the opposite of a "gardening chatbot."
2. **Region-correctness-as-safety** + invasive/toxic screening as hard gates — an ethics/quality bar no
   competitor advertises.
3. **Truly open** (MIT / CC-BY / CC0 data) and **forkable/localizable** by partners.
4. **Anti-North-bias by design** — first-class tropical/Southern-Hemisphere, rainfall-regime support.
5. **Offline-first, printable, WCAG 2.2 AA, zero-telemetry** — built for the underserved, privacy-clean.
6. **Provenance on every recommendation** (`derivedFrom { ruleId, inputs }`) — reproducible and trusted.

---

## 5. Claude API leverage (and hard limits)

**Where Claude adds leverage (draft-and-structure for human/expert review — never the live oracle):**

1. **Extracting + structuring sourced agronomy into the schema** — read an extension PDF or FAO crop
   calendar and draft a `CropProfile` / calendar entry (germination temp, spacing, DTM, frost
   tolerance) with each field **tagged to its source quote**, for SME verification. High-leverage, since
   the bottleneck is structuring fragmented PDFs.
2. **Plain-language + reading-level adaptation** — rewrite extension jargon into beginner, low-literacy,
   translation-ready prose; generate consistent sow/transplant/harvest microcopy and pictograph alt-text.
3. **Region-adaptation drafting** — propose how a base crop profile's *guidance text* should differ for
   a target region (given the engine's computed windows), flagging where sources diverge for the SME.
4. **Translation drafting + crop-name reconciliation** — draft localized strings and propose localized
   common names **anchored to the binomial**, for the translation+agronomy reviewer (not auto-publish).
5. **License/provenance triage** — scan candidate sources and pre-classify likely license status (PD /
   CC / all-rights-reserved) to queue for human verification; draft citation metadata.
6. **QA assistant** — diff a draft calendar against cited sources and surface unsupported/missing claims
   for review (a reviewer aid, not an approver).

**Where Claude must NOT decide (hard guardrails — these are the project's spine):**

- **No model in the runtime path that emits a planting date or suitability verdict** — the engine
  computes deterministically from cited data + rules.
- **No fabricated/improvised planting data** — every numeric value (germ temp, frost date, DTM, spacing)
  must come from a cited authoritative source, not model recall. Hallucinated DTM or frost dates = the
  exact food-loss harm the plan guards against.
- **Region-correctness and horticultural accuracy must be SME-verified against extension/FAO sources** —
  Claude drafts; a region-qualified expert approves (author ≠ approver).
- **Invasive/toxic-plant safety is expert-reviewed**, not model-judged — false "safe" claims are
  poisoning/environmental-harm risks.
- **License status must be human-verified** — Claude's triage is a hint, never the authorization to
  embed text/data.
- **No medicinal/foraging/preservation/pesticide-rate content** — Claude must refuse and link out per
  the carve-outs.

---

## 6. Ten concrete optimizations

1. **Type the agronomic rule set by limiting regime** (frost-limited / rainfall-limited / heat-limited /
   photoperiod-limited) in ADR-2, so the engine doesn't emit "after last frost" nonsense in the tropics.
   *Highest-impact correctness fix.*
2. **Decouple hardiness zone from sowing windows** in the schema; document that `hardinessZone` is
   US-only, perennial-cold-survival data, and **not** the driver of annual-veg planting dates.
3. **Add `lastSowDate` / days-to-frost logic** to the engine (don't let beginners sow too late to
   mature) and treat **days-to-maturity as a temperature-conditioned range**, not a constant.
4. **Add an expert-reviewed `toxicityNotes` field** for cultivated crops with toxic parts (rhubarb
   leaves, green potatoes, raw beans, **cassava** processing) — close the in-scope poisoning gap.
5. **Pin frost-date probability thresholds** (e.g. 50% vs 10% exceedance) and **source-map vintage**
   (USDA 2023) per unit; surface uncertainty as a range with its probability, not a single date.
6. **Adopt named, licensed screen sources**: GISD (CC, via GBIF/CABI) as the global invasive baseline +
   national regulatory noxious-weed lists; screen for **both** ecological invasiveness and legal
   restriction, warning even when legal.
7. **Lead with FAO Crop Calendar (CC BY 3.0 IGO) + Köppen-Geiger (CC BY 4.0)** as the open, global
   data spine to de-risk non-US coverage and the license chain from day one.
8. **Make the first launch region a *paired* cold-start**: one PD-rich US zone (fast) **and** one
   tropical/Southern-Hemisphere region (anti-bias) in M1–M2, not US-only first.
9. **Event-triggered re-review**, not just annual: auto-flag units when a source dataset re-releases
   (next USDA map, new FAO calendar version).
10. **Ship a low-text/pictographic guide mode** (sow/transplant/harvest icons, container/small-space
    variants) for low-literacy and urban beneficiaries — beyond translated paragraphs.

---

## 7. Parallel & perpendicular spin-offs

- **Region-aware plant/crop database (extractable core).** The crop × zone × calendar dataset with
  provenance is reusable infrastructure — publish it as a standalone **open dataset** (CC-BY/CC0) that
  the guide site merely renders. *Parallel.*
- **MCP server (`gardening-guides-mcp`).** Expose the deterministic engine + crop DB as read-only MCP
  tools (`get-planting-window`, `screen-invasive`, `crop-profile`) so other agents query **cited,
  verified** data instead of hallucinating planting dates — turns the project into an authoritative
  tool-call backend. *Perpendicular, high-leverage.*
- **`food-security-briefs`** — region planting calendars feed seasonal "what to grow now for nutrition"
  briefs; shared beneficiary + partner channels (ECHO/FAO). *Parallel.*
- **`climate-adaptation-guides`** — the zone-shift / Köppen-future-projection data (Beck CC-BY 4.0
  future maps) directly powers "how your growing conditions are changing" guidance. *Perpendicular.*
- **`urban-tree-inventory`** — shares the climate-zone engine, region selector, and invasive screen;
  fruit/nut tree suitability is a natural crossover. *Parallel.*
- **`heritage-recipes-open`** — localized crop names + harvest seasons link "grew it → cook it,"
  reinforcing cultural relevance for resettlement/mutual-aid beneficiaries. *Perpendicular.*
- **Seed-viability / seed-saving module** — ties to ECHO seed-bank networks; a beginner barrier the
  current scope omits. *Parallel.*

---

## 8. Open questions

1. **Launch-region strategy:** does the de-risking value of a PD-rich US zone outweigh the anti-North-
   bias mandate to lead with a tropical/Southern-Hemisphere region? (Recommend a paired launch.)
2. **Regime-typed rule set:** how are rainfall/photoperiod-limited regions modeled in ADR-2 without
   over-claiming precision where dry-season onset is itself variable?
3. **Global-South reviewer supply:** can FAO/ECHO networks supply region-qualified SMEs, given that the
   extension/Master-Gardener credential bar is US/UK-centric?
4. **Invasive vs. legal screen sources:** which authoritative lists per region, and how to handle
   "legal but ecologically invasive" crops (warn vs. exclude)?
5. **Toxicity scope:** does adding cultivated-crop `toxicityNotes` risk drifting toward the excluded
   medicinal/edibility territory, or is it squarely in-scope safety?
6. **Data license:** CC0 (max reuse) vs CC-BY (attribution chain) for the factual dataset — and does any
   upstream source (FAO CC BY **3.0 IGO**, Köppen CC BY 4.0) force CC-BY downstream? (Likely yes for
   derived data — verify attribution-stacking.)
7. **MCP/dataset productization:** is the extractable engine+DB an Elyos-supported artifact, and who
   maintains it?
8. **Partner:** named community-garden/food-bank/extension/NGO (ECHO and FAO are obvious first calls).

---

### Sources
- USDA 2023 Plant Hardiness Zone Map — https://www.ars.usda.gov/news-events/news/research-news/2023/usda-unveils-updated-plant-hardiness-zone-map/ ; https://planthardiness.ars.usda.gov/
- NPR (half the country shifted) — https://www.npr.org/2023/11/17/1213600629/-it-feels-like-im-not-crazy-gardeners-arent-surprised-as-usda-updates-key-map
- Civil Eats (USDA downplays climate link) — https://civileats.com/2024/01/29/the-usda-updated-its-gardening-map-but-downplays-connection-to-climate-change/
- Virginia Cooperative Extension (reuse-with-credit) — https://www.pubs.ext.vt.edu/AREC/AREC-66/AREC-66.html
- Old Farmer's Almanac planting calendar / frost dates — https://www.almanac.com/gardening/planting-calendar ; https://www.almanac.com/gardening/frostdates
- Gardenate — https://gardenate.com/ ; https://gardenate.com/zones/USA+-+Zone+6b
- RHS terms of use — https://collections.rhs.org.uk/terms-of-use
- FAO Crop Calendar (CC BY 3.0 IGO / Open Data) — https://data.apps.fao.org/catalog/dataset/crop-calendar-by-country-crop-and-activity ; https://cropcalendar.apps.fao.org/
- ECHOcommunity / ECHO seed bank — https://www.echocommunity.org/ ; https://echonet.org/echo-news/growing-beyond-free-seeds-the-importance-of-community-seed-banks-from-trial-packets-to-local-food-security/
- PFAF — https://pfaf.org/ ; https://en.wikipedia.org/wiki/Plants_for_a_Future
- GISD (invasive screen, via GBIF/CABI) — https://www.gbif.org/dataset/b351a324-77c4-41c9-a909-f30f77268bc4 ; https://ckan.cabi.org/data/dataset/gisd-global-invasive-species-database
- Köppen-Geiger (Beck et al. 2023, CC BY 4.0) — https://www.gloh2o.org/koppen/ ; https://www.nature.com/articles/s41597-023-02549-6
