# PLAN — open-gardening-guides (region-specific, climate-zone-aware open food-growing guides)

> Status: Draft · Version: 0.1.0 · Last updated: 2026-06-28 · Owner: TBD (maintainer) · Lane: donated
> Risk tier: **low** (core) with explicit **medium/high carve-outs** that are excluded or expert-gated (see §8).

---

## Executive summary

**open-gardening-guides** turns spare AI capacity into a free, open, **climate-zone-aware** body of
food-growing guidance for households and communities working to feed themselves. Its purpose is
narrow and honest: tell a person *what food crops they can realistically grow where they actually
live, and what to do in each month of their season* — sourced from authoritative agronomy, traceable
to a named source, and usable offline and on paper.

The project is not a chatbot that improvises gardening tips. Its value — and its entire reason to
exist as a *good deed* rather than another content farm — is that every recommendation is
**deterministic, region-correct, and source-cited**. The hard engineering is a **climate-zone
engine**: a transparent mapping from a user-selected location → its growing conditions (hardiness
zone, frost dates, agro-ecological zone, season length) → a **suitability + planting-window**
calendar for a curated set of crops, computed from **open climate datasets**, never guessed by a
model. A wrong planting date or an unsuitable crop is not a harmless error here: it can waste a
food-insecure household's scarce seed, water, labour, and an entire growing season. So
"region-correctness" is treated as a safety property, not a nicety.

The work has two coupled lanes: (1) a **data/content pipeline** — structured, versioned crop
profiles, climate-zone definitions, and per-region planting calendars, each carrying source
citations and a review log; and (2) a **delivery surface** — a static, offline-capable, accessible
site/PWA that assembles those data into printable, low-bandwidth regional guides.

Although the portfolio lists this project as **low risk**, this plan deliberately **does not gloss
over** the higher-risk neighbours of food-growing content. Foraging / wild-plant edibility
identification (poisoning risk), home food **preservation/canning** (botulism risk), **medicinal /
herbal** health claims, and **pesticide application** instruction are **out of scope or
expert-gated** — we link to the authorities instead of improvising. Recommending a species that is
**invasive or legally restricted** in a region is treated as an environmental-harm bug. These
guardrails are part of the project's identity, not an afterthought.

**Honesty note on the partner / verified need.** No partner organisation, community-garden network,
extension office, or food-security NGO has been secured, and no need has been verified in writing.
Until one is, the partner and verified-need status are **TO BE SECURED**, every task ships with
`verifiedNeed: false`, and the project proceeds only as a generic public good. General need is
well-documented by public agencies (rising food insecurity; growing zones shifting under climate
change), which justifies M0–M1 foundation work, but partner endorsement is required before the
project meets Elyos's *delivered, not merged* bar. See §2 and §16.

---

## Problem & beneficiaries

**The problem.** People who most need to grow their own food are the least well-served by existing
gardening information. That information is (a) **fragmented** across thousands of extension
publications, blogs, and seed-company pages of wildly varying quality; (b) **geographically wrong for
most readers** — generic "plant tomatoes in spring" advice is useless or harmful when "spring" means
something different in every climate; (c) **locked behind copyright** (RHS, most seed catalogues,
many magazines) or **paywalls**; (d) **online-only and bandwidth-heavy**, when many target users have
intermittent connectivity, low-end devices, or rely on printed handouts; and (e) increasingly **stale
against a changing climate** — hardiness zones and frost dates have shifted, so even good older advice
misfires. The commercial gardening market fills the gap with product upsells and affiliate links, not
free, neutral, regionally-correct public guidance.

**Who is helped (beneficiaries).**
- **Food-insecure households** growing food to stretch a budget, where a failed season is a real
  loss — the primary beneficiary.
- **Community gardens, school/teaching gardens, and food-bank garden programs** that need free,
  printable, locally-correct material to hand to participants instead of building their own.
- **Mutual-aid groups and resettlement / refugee support orgs** helping newcomers grow familiar
  crops in an unfamiliar climate.
- **Smallholders and home growers in low-resource regions** under-served by English-only,
  temperate-Northern-Hemisphere-biased content.
- **Volunteers and extension-adjacent educators** who want a license-clean base to localise.

**Verified need / partner org: TO BE SECURED.** No organisation has confirmed a need in writing and
none is named. We treat the need as **plausible and well-evidenced in the aggregate but unverified
for any specific community** until at least one named partner (a community-garden network, food bank,
extension office, or food-security NGO) confirms it in writing (letter of support or lightweight MOU)
and names the **priority regions, crops, and languages**. Foundation work proceeds; partner-specific
endorsement gates *Definition of Shipped*.

---

## Goals and non-goals

**Goals.**
- Publish **region-specific, climate-zone-aware** food-growing guidance: for a user-selected
  location, a **suitability list** (what food crops can realistically grow there) and a **planting
  calendar** (month-by-month sow / transplant / harvest actions for the local season).
- Make every recommendation **deterministic and source-traceable** — derived from open climate
  datasets + cited authoritative agronomy, **never** model-improvised dates or claims.
- Ship the guidance as **open, offline-capable, printable, low-bandwidth** material (MIT code /
  CC-BY-4.0 content + CC-BY-4.0 or CC0 data).
- Cover a **curated, high-impact staple/vegetable crop set** well, across multiple climate zones,
  rather than every plant shallowly.
- Be **internationalised** (UI + at least one non-English locale + localised crop names) so the
  content reaches non-English growers.
- Be **adoptable** by a partner community-garden / food-security org in their region and language.
- Treat **region-correctness, license cleanliness, and the safety carve-outs as hard gates**, not
  best-effort.

**Non-goals (constraints that define the project).**
- **No foraging or wild-plant edibility identification.** We cover *deliberately sown, cultivated
  food crops* only. We will never tell anyone a wild plant or mushroom is safe to eat — poisoning
  risk; link to authorities.
- **No home food-preservation / canning instructions.** Botulism and food-safety risk; out of scope.
  Link to authoritative sources (e.g. USDA NCHFP / national food-safety bodies) and the sibling
  `food-safety-open` project.
- **No medicinal, herbal, or "health benefit" claims** about any plant. Medical territory; excluded.
- **No pesticide / herbicide / fungicide application instruction** (rates, mixing, schedules). The
  product label is the legally binding instruction; we default to **prevention, cultural methods, and
  IPM principles**, and link to the label + the local regulator. No brand/product recommendations.
- **No recommendation of invasive or legally restricted species** for a region; the engine must
  screen against regional noxious-weed / invasive lists.
- **No unqualified soil-contamination remediation advice** (e.g. urban lead). Flag the risk; link to
  authorities for soil testing.
- **Not commercial / not affiliate.** No product, seed-vendor, or brand promotion; no lead-gen.
- **Not a precision-agriculture, IoT, sensor, or live-weather product.** Advisory, not real-time.
- **Not a social network, marketplace, or account-based app.** No PII, no user tracking.
- **Not partisan advocacy** on contested agronomy debates (organic vs. conventional, GMO, etc.):
  we present authoritative, evidence-based, product-neutral information and let users decide.

---

## Success metrics (outcomes)

Outcome-centric and beneficiary-first. Baselines are zero at project start unless noted. We avoid
vanity metrics (stars, raw page views, downloads alone). Because we collect **no telemetry** (§14),
reach and real-world outcomes are measured by **partner self-report**, an honest privacy/measurement
trade-off.

| Outcome | Baseline | Target (first 12 months post-launch) | How measured (privacy-preserving) |
| --- | --- | --- | --- |
| Climate zones × regions with a reviewed planting calendar | 0 | ≥ 8 zone/region calendars, each source-cited + expert-reviewed | In-repo review log |
| Food crops with complete, sourced profiles | 0 | ≥ 25 staple/vegetable crops | Schema-validated crop catalogue |
| Agronomic accuracy of shipped guidance | n/a | 100% of shipped units source-cited + expert-signed-off; 0 unsourced claims | Provenance + review log; CI source-check |
| Region-correctness defects (wrong zone/date/unsuitable crop reaching production) | n/a | 0 in shipped content; any found = incident + fix | Review log + issue tracker |
| Invasive/restricted-species screening | n/a | 100% of recommended crops screened against the region's invasive/noxious list; 0 violations | CI screen + reviewer check |
| Partner orgs endorsing / adopting | 0 | ≥ 1 named partner adopts; goal 3 | Signed letters of support / MOUs (manual record) |
| Gardeners / households reached (partner-reported, opt-in) | 0 | ≥ 2,000 distinct households over a rolling 12-month window | Partner self-report template (no in-app tracking) |
| Beds / plots actually planted using a guide (true outcome, partner-reported) | 0 | ≥ 300 plots reported planted (confirmed, de-duplicated) | Partner self-report; estimates flagged separately |
| Offline + print usability | none | 100% of core guides usable offline after first load and as a clean printable PDF | Offline E2E + print-snapshot test in CI |
| Languages localised (UI + ≥ 1 region's content) | 1 (en) | ≥ 2 languages, with localised crop names | Translation-completeness CI check |
| Accessibility | none | WCAG 2.2 AA; 0 critical axe violations + manual AT pass | axe/pa11y + manual screen-reader audit |
| Privacy posture | n/a | 0 telemetry endpoints, 0 third-party trackers, 0 PII fields | Static audit + runtime network-interception E2E |

**Reach/outcome measurement — windows, denominators, anti-double-counting.** "Households reached" and
"plots planted" count **distinct households / distinct plots** (not page views, downloads, or
distributed leaflets) over a **rolling 12-month window** starting at production launch. A partner
counts a household **once per reporting period** across all touchpoints; households reached by more
than one partner are de-duplicated by the steward at roll-up (best-effort, partner-attested, regional
overlap noted). **Estimates are recorded as estimates, separate from confirmed counts.** "Plots
planted" is the project's truest outcome and the hardest to measure; we report it honestly with its
uncertainty rather than substituting an easy vanity number.

**Partner self-report template (standard fields).** Per period: partner name; reporting window;
channel(s); **distinct households reached (confirmed)**; estimated additional reach (flagged);
**plots/beds reported planted (confirmed)**; region(s)/zone(s)/language(s) served; de-dup notes;
free-text outcomes note (what grew, what failed — failures are valuable feedback). The steward keeps
reports in the manual outcomes record and publishes only de-duplicated, aggregated figures.

---

## Scope

**In scope.**
- A **climate-zone engine**: deterministic mapping from a user-selected region → growing parameters
  (hardiness zone, average first/last frost dates, season length, agro-ecological/Köppen zone, broad
  rainfall regime) → **crop suitability** + **planting-window calendar**, computed from open climate
  datasets with full source provenance. No hidden model inference of dates/suitability.
- A **crop catalogue**: structured, versioned profiles for a curated staple/vegetable set (sowing
  method, spacing, sun/water needs, days-to-maturity ranges, common cultural problems handled by
  prevention/IPM, harvest indicators), each source-cited.
- **Per-region planting calendars** assembled from crop data × zone parameters, expert-reviewed.
- A **delivery surface**: static, accessible, offline-capable site/PWA that renders guides and
  exports clean **printable** versions; low-bandwidth by design.
- **i18n** framework + ≥ 1 non-English locale, including **localised/scientific crop names** so a
  crop is unambiguous across languages (binomial + common names).
- **Provenance + review log** for every crop profile and region calendar.
- **Invasive/restricted-species screening** against per-region authoritative lists.

**Out of scope (explicit).**
- Foraging / wild-plant or mushroom edibility identification.
- Home food preservation, canning, fermentation, curing instructions (link out).
- Medicinal / herbal / health-benefit claims.
- Pesticide/herbicide/fungicide application rates, mixing, or schedules (link to label + regulator).
- Soil-contamination remediation advice (flag + link to soil-testing authorities).
- Live weather, real-time alerts, sensors/IoT, precision-ag, or automated irrigation control.
- Geolocation tracking, user accounts, social features, marketplaces, or any server-side PII.
- Commercial recommendations, affiliate links, seed/vendor promotion, advertising, telemetry.
- Native app-store binaries at launch (PWA install only; backlog).
- Any region/crop/language we cannot source-verify and expert-review.

---

## Solution approach & architecture

**Overview.** A **data-first, static-first** system. The intellectual core is a curated open dataset
(crops × climate zones × planting calendars) with rigorous provenance; the delivery surface is a thin,
offline-capable, accessible static site that *renders* that dataset. No backend is required for core
use; the only network needs are initial load and optional update checks. Crucially, **all
recommendations are deterministic and source-backed** — the build computes calendars from data and
cited rules, so output is reproducible and auditable. (AI/agent capacity is used to *draft and
structure* sourced content for human/expert review, never to be the live oracle.)

**Components.**
- **Climate-zone engine (the hard part).** A deterministic library that, given a region selector
  (country/admin region or zone code the user picks — no geolocation), resolves growing parameters
  from bundled open climate datasets and computes, per crop, a suitability verdict
  (suitable / marginal / unsuitable, with the limiting factor) and a planting window (sow/transplant/
  harvest months) using **explicit, cited agronomic rules** (e.g. "direct-sow when soil reliably
  above the crop's minimum germination temperature / after last frost ± N weeks"). Every output value
  carries the inputs and source that produced it. **No LLM is in the runtime path that emits a date or
  a suitability verdict.**
- **Crop catalogue store.** Versioned structured records (see data model), each with `sources[]`,
  review status, and license — shipped as static data bundles.
- **Region/zone definitions.** Bundled, license-checked climate-zone and frost-date data per region.
- **Guide renderer (UI).** TypeScript/ESM static site that assembles crop + zone + calendar data into
  a readable, printable regional guide. Offline-capable (service worker), WCAG 2.2 AA, low-bandwidth
  (no heavy media; bundled fonts; no runtime third-party fetch).
- **i18n layer.** Message catalogs per locale; crop records keyed with binomial + per-locale common
  names; build-time completeness validation; safe fallback to English.
- **Provenance/review subsystem.** Per-unit `sources[]`, reviewer/approver identities, review-log
  entries; CI rejects unsourced or unreviewed units.
- **Build/CI.** pnpm workspace; lint, typecheck, unit (incl. **engine determinism + golden-calendar
  tests**), schema validation, **license/provenance check**, **invasive-species screen**, a11y, and
  offline/print E2E gates.

**Tech stack.** TypeScript, ESM, pnpm workspaces. Static-first site + PWA (web app manifest + service
worker). Static hosting (GitHub Pages / Netlify / Cloudflare Pages — no app server). Testing: Vitest
(unit + engine determinism), Playwright (offline + print E2E), axe-core/pa11y (a11y), AJV (schema).
License: **MIT** (code) / **CC-BY-4.0** (content) / **CC-BY-4.0 or CC0** (data, decided in ADR).

**Data model (sketch — finalised after the data-format ADR).**
```
CropProfile {
  id: string                       // stable slug, e.g. "tomato"
  binomial: string                 // e.g. "Solanum lycopersicum" (disambiguates across languages)
  names: { [lang]: string[] }      // localised common names
  family: string
  lifecycle: "annual" | "biennial" | "perennial"
  sowMethod: ("direct" | "transplant")[]
  minGermTempC: number             // cited
  frostTolerance: "tender" | "half-hardy" | "hardy"
  daysToMaturity: { min: number, max: number }
  spacingCm: { inRow: number, betweenRow: number }
  sun: "full" | "partial" | "shade-tolerant"
  waterNeed: "low" | "moderate" | "high"
  commonProblems: Problem[]        // prevention/IPM framing only; no pesticide rates
  harvestIndicators: localized string
  sources: Citation[]              // { org, title, url, retrievedDate, sourceLicense }
  reviewStatus: "draft" | "in-review" | "approved"
  reviewedBy: string               // author/reviewer
  approvedBy: string               // SME; MUST differ from reviewedBy (no self-approval)
  reviewLogRef: string             // PR-tied, append-only review-log entry
  lastReviewed: date
  contentLicense: "CC-BY-4.0"
  dataVersion: string
}

ClimateZone / RegionProfile {
  id: string                       // e.g. "us-zone-7b" or admin-region code
  hardinessZone?: string           // e.g. USDA 7b (PD source)
  koppen?: string                  // Köppen-Geiger class
  agroEcologicalZone?: string      // FAO/GAEZ class
  lastFrostDate?: date-range       // cited; with uncertainty
  firstFrostDate?: date-range
  seasonLengthDays?: number
  rainfallRegime?: string          // broad descriptor
  invasiveListRef: string          // authoritative noxious-weed/invasive list for the region
  sources: Citation[]
  ... (review fields as above)
}

PlantingCalendarEntry {            // computed, then expert-reviewed
  cropId, zoneId,
  suitability: "suitable" | "marginal" | "unsuitable",
  limitingFactor?: string,         // why marginal/unsuitable
  actionsByMonth: { sow?, transplant?, harvest? }[],
  derivedFrom: { ruleId, inputs },  // full reproducibility
  reviewStatus, reviewedBy, approvedBy, reviewLogRef
}
```

**Key decisions (recorded as ADRs in M0).**
1. **Data format & storage** (JSON vs. structured Markdown/MDX; how citations + review status are
   enforced in CI) — **decided first**; the crop/zone schemas and the engine depend on it.
2. **Climate-zone model & source set** — which datasets define zones/frost dates per launch region,
   their licenses, and the agronomic rule set (germination temp / frost-window rules) — explicit and
   cited, with uncertainty handling.
3. **Data license** — CC-BY-4.0 vs. CC0 for the crop/calendar dataset (CC0 maximises reuse;
   CC-BY preserves attribution chain). Default proposal: CC-BY-4.0 content, **CC0 for pure factual
   data** where source licenses permit; verify per source.
4. **UI framework + SW tooling** (a11y + bundle size + offline + i18n/RTL support weighted).
5. **Print/export** (print CSS vs. client PDF) and the low-bandwidth budget.

**Decision ordering.** ADR #1 (data format) lands **before** the crop/zone schemas and engine are
finalised; ADR #2 (climate model + rule set) lands **before** any planting calendar is computed or
shipped. Until those land, schemas here are **provisional**. TASKS.md sequences this explicitly.

---

## Data, licensing & compliance

**This section is load-bearing. Region-correctness and license cleanliness are the project's core
promises — be conservative.**

**Climate / zone data sources (verify each license before use).**
- **USDA Plant Hardiness Zone** data (US; USDA-ARS/OSU) — US government work, generally **public
  domain**; verify the specific dataset terms and attribution request.
- **Köppen-Geiger climate classification** — published academic datasets; several are openly licensed
  (e.g. CC-BY); verify the specific dataset and cite.
- **FAO / GAEZ agro-ecological zones** and **FAOSTAT** — FAO data terms (often CC-BY-style with
  attribution); verify per dataset.
- **Frost-date / climate-normals** data — national meteorological services (NOAA in the US is
  generally **PD**; other national services vary — many are open-with-attribution, some restricted).
  Verify and cite per country/region; record uncertainty.

**Agronomic guidance sources (paraphrase + cite; never copy verbatim).**
- **University Cooperative Extension** publications (US land-grant universities), **USDA**, **FAO**,
  national agricultural research institutes — authoritative, but **license varies per institution and
  per document** (some are PD or CC, many assert copyright). We **verify each document's terms**,
  **paraphrase and cite**, and **never reuse copyrighted text or logos**.
- **Explicitly license-restricted sources we will NOT copy:** RHS, seed-company catalogues,
  gardening magazines/books, and most blogs are copyrighted — **link only**, never embed.
- **Open/CC/PD only** for any embedded text or data, per the portfolio guardrail.

**Source-divergence precedence (sources disagree on dates/suitability).** (1) The **agency or
extension with jurisdiction over the user's region wins** for that region (local/national extension >
supranational > foreign). (2) For global/no-region defaults, prefer **FAO** for staple-crop agronomy
and the relevant **national extension** for region-specific timing. (3) Where credible sources still
materially disagree on a planting action, **state the range/divergence and cite both** rather than
averaging silently. Each region may carry a **region override** layered on the base crop data,
recorded with its source. Material divergences are noted in the review-log entry.

**Invasive / restricted-species compliance (mandatory).** Before a crop is recommended for a region,
it is **screened against that region's authoritative noxious-weed / invasive-species / regulated-plant
list** (e.g. national/state agriculture department lists). A crop flagged invasive or restricted in a
region is **not recommended there** (and the guide may note the restriction). This screen is a CI gate
and a reviewer checkpoint — recommending an invasive is treated as an **environmental-harm defect**.

**Licensing of our outputs.** Code **MIT**; content **CC-BY-4.0**; factual dataset **CC-BY-4.0 or
CC0** (ADR #3). All clearly stated in-repo and in-app.

**Provenance model.** Every crop profile, zone definition, and calendar entry carries `sources[]`
(org/title/url/retrievedDate/sourceLicense) and a review-log entry. A repo-level provenance/review log
is the auditable record the *Definition of Shipped* checks against. Calendar entries additionally
record `derivedFrom` (rule + inputs) so any recommendation is fully reproducible.

**Privacy / PII stance.** **Zero PII.** Region is **user-selected, not geolocated**; no accounts, no
analytics, no telemetry, no third-party scripts, no cookies beyond what the service worker needs.
Anything a user enters (e.g. a saved region or a chosen crop list) **stays on-device** and is exported
only by explicit action (print/local file). Enforced and audited in CI (§14).

**Attribution & endorsement.** Source institutions are credited per unit and on an in-app credits
page. We do **not** imply endorsement by USDA, FAO, any extension, or any agency we cite unless that
body has explicitly endorsed the project.

---

## Quality, review & risk gates

**Risk tier: low (core), with medium/high carve-outs excluded or expert-gated.** The portfolio marks
the project **low**, and the *core* (region-appropriate cultivated-crop calendars, spacing, watering,
composting basics) is genuinely low-risk. But this plan refuses to treat the whole surface as low:

- **Region-correctness is a safety property.** Because a wrong zone/date/unsuitable-crop
  recommendation can cost a food-insecure household a season, **agronomic accuracy review by a
  qualified domain reviewer is mandatory** for every shipped crop profile and region calendar — a
  medium-style review bar applied within a low-tier project.
- **High-risk carve-outs (excluded or gated):** foraging/edibility ID, food preservation/canning,
  medicinal/herbal claims, and pesticide application instruction are **out of scope**; if any such
  content is ever proposed it requires the relevant **credentialed expert sign-off** per the
  high-tier rule **or** is rejected and replaced with a link to the authority. Default is to link out.

**Required reviews before a deed is "done":**
- **Code/engine tasks:** maintainer review + CI green (lint, typecheck, unit incl. **engine
  determinism + golden-calendar tests**, schema validation, license/provenance check,
  invasive-species screen, a11y, offline/print E2E). A11y regressions block merge.
- **Crop/calendar content tasks:** an **agronomy/horticulture domain reviewer** verifies every claim
  against the cited authoritative sources and confirms **region suitability + timing**, **invasive/
  restricted screening**, and **scope compliance** (no foraging/preservation/medicinal/pesticide-rate
  content). Where region-specific timing carries real failure risk, an **extension agent / agronomist
  / qualified Master Gardener for that region** signs off.
  - **Reviewer credential bar:** current/former Cooperative Extension staff, a degreed
    agronomist/horticulturist, or a certified Master Gardener active in the relevant region (or an
    equivalent recognised qualification), recorded with the sign-off.
  - **Role separation / no self-approval:** the author (`reviewedBy`) and the approver (`approvedBy`)
    must be **distinct people**.
  - **Tamper-evident review log:** every approval writes a **PR-tied, append-only** entry (PR #,
    commit SHA, unit `dataVersion`, reviewer + approver, sources checked, divergence + invasive-screen
    decisions). This is the auditable record.
- **Translations:** reviewed by a competent speaker **and** re-checked for **agronomic accuracy +
  crop-name correctness** (a mistranslated crop name can send someone to the wrong, possibly toxic,
  plant), not just fluency. Binomial names anchor the meaning.
- **Accessibility:** automated checks **plus** a **manual assistive-technology audit** on a defined
  matrix (NVDA+Firefox/Win, JAWS+Chrome/Win, VoiceOver+Safari/macOS·iOS, TalkBack+Chrome/Android,
  plus keyboard-only per desktop browser), before each milestone exit and each release, by the
  qualified accessibility reviewer.

**Definition of Shipped (project-level).** A deployed, offline-capable, accessible guide site that:
(1) renders **deterministic, source-cited, expert-reviewed** region-specific planting guidance for
≥ the M3 coverage target; (2) passes WCAG 2.2 AA (automated + manual) and works offline + prints
cleanly (E2E); (3) ships **only in-scope content** with **0 invasive/restricted recommendations** and
**0 unsourced claims**; (4) collects no telemetry/PII; and (5) is **adopted/endorsed by a named
partner org** and available in that community's region(s) and language(s). Until a partner is secured,
criterion (5) is **outstanding**: the project is "publicly usable" but not "shipped" by the
*delivered, not merged* bar.

**"Publicly Shipped (no partner)" success state.** Criteria (1)–(4) can be fully met with no partner.
If, by a **decision point 6 months after the M3 build is production-ready**, no partner is secured,
the steward + Elyos governance may declare **"Publicly Shipped (generic public good)"**: deployed,
announced, and distributed directly and via mutual-aid/community channels, with outcomes by
best-effort self-report. This is a recognised, honest success state distinct from
"Shipped (partner-adopted)"; partner outreach continues and a later endorsement upgrades the status
rather than gating launch.

---

## Roadmap & milestones

Phased; each phase has measurable exit criteria. M0 is a thin cold-start foundation.

- **M0 — Foundation & cold-start (thin slice).**
  Goal: the data spine + one end-to-end vertical slice — schemas, the climate-engine skeleton, one
  sample crop, one region calendar, and CI guardrails.
  Exit criteria: repo builds (TS/ESM/pnpm); **data-format ADR (#1) recorded before** the crop/zone
  schemas are finalised; **climate-model ADR (#2)** records the launch-region dataset set + agronomic
  rule set with licenses + uncertainty handling **before** any calendar is computed; one **source-
  cited crop profile** + one **region planting calendar** validate against schema and render in a
  static page; CI runs lint/typecheck/unit + **schema validation** + **license/provenance check** +
  **invasive-species screen** + **engine determinism (golden-calendar) test** + an offline/no-telemetry
  smoke E2E; zero telemetry verified by CSP `connect-src 'none'` + runtime network-interception E2E.

- **M1 — Climate-zone engine & core crop set.**
  Goal: the real value — a working deterministic engine and a first region's full guide.
  Exit criteria: engine resolves zone parameters + computes suitability/planting windows for a launch
  region from open data, with `derivedFrom` provenance on every entry; ≥ 10 staple/vegetable crops
  with complete **source-cited, expert-reviewed** profiles; ≥ 1 region's planting calendar complete +
  expert-signed-off; printable/exportable guide generated client-side; WCAG 2.2 AA (automated +
  manual) pass; 0 invasive/restricted recommendations; 0 unsourced claims.

- **M2 — Coverage breadth, offline & i18n.**
  Goal: more regions/zones, full offline PWA, and reach beyond English.
  Exit criteria: ≥ 4 zone/region calendars reviewed; ≥ 18 crops; installable offline PWA (loads +
  prints offline after first visit, within bandwidth budget); i18n framework with build-time
  completeness check; ≥ 1 non-English locale (UI + ≥ 1 region's content) with **localised crop names
  anchored to binomials**, accuracy-reviewed; safe locale fallback.

- **M3 — Depth, partner adoption & shipped.**
  Goal: broaden coverage, secure a partner, meet the full Definition of Shipped.
  Exit criteria: ≥ 8 zone/region calendars reviewed; ≥ 25 crops; ≥ 2 languages; **named partner org
  endorsement/adoption on file** (verifiedNeed flips true); production build deployed; outcomes-tracking
  process (partner self-report incl. plots-planted) + content re-review cadence operational.

Dependencies: M1 depends on M0 (schemas + engine skeleton + ADRs). M2 depends on M1 (stable engine +
crop set to scale + UI to localise). M3 depends on M2 (multilingual, multi-region base) and on the
**partner being secured** (pursued in parallel from M0).

---

## Work breakdown

The itemised, schema-mapped backlog lives in **TASKS.md**, organised by the M0–M3 milestones above.
Each task maps to an Elyos Task JSON (see schema), is sized (small/medium/large), risk-tagged, names a
reviewer, and lists dependencies. TASKS.md also gives acceptance criteria for the most important tasks
per milestone, milestone Definitions of Done, a backlog, and a complete schema-valid example Task JSON
(`verifiedNeed: false`, `requestor: "TBD"` — no partner yet).

## Governance, roles & stakeholders

- **Maintainer (Owner): TBD.** Owns repo, roadmap, releases, review standards, license discipline.
- **Code reviewers:** rotation of TS-competent contributors; ≥ 1 approval + CI green to merge.
- **Agronomy/horticulture domain reviewers:** contributors with the credential bar (extension staff,
  agronomist/horticulturist, certified Master Gardener for the region) who verify accuracy, region
  suitability, and invasive screening. For region-specific timing with real failure risk, a
  **region-qualified expert** signs off; author ≠ approver.
- **Accessibility reviewer:** contributor competent with assistive tech; performs manual audits.
- **Translation reviewers:** competent speakers per locale, paired with an agronomy/crop-name
  accuracy re-check.
- **Steward (last-mile owner): TBD** — owns deployment, the partner relationship, and getting guides
  into growers' hands.
- **Partner / requestor: TO BE SECURED** — a named community-garden/food-security/extension org
  confirming need, priority regions/crops/languages, and ultimately endorsing/adopting.
- **Elyos governance/board:** arbitrates edge cases, risk-tier decisions, and any proposal to bring a
  carve-out (e.g. preservation) in-scope under expert sign-off.

## Dependencies & integrations

- **External data sources:** USDA Plant Hardiness Zones, Köppen-Geiger datasets, FAO/GAEZ + FAOSTAT,
  NOAA/national met frost-date data, Cooperative Extension/USDA/FAO agronomy publications, and
  per-region invasive/noxious-weed lists (all read-only references; license-checked per use).
- **Tooling/libraries:** PWA/service-worker tooling (e.g. Workbox), UI framework (TBD), i18n library,
  AJV (schema validation), test stack (Vitest, Playwright, axe-core/pa11y), client print/PDF.
- **Hosting:** static host (GitHub Pages / Netlify / Cloudflare Pages) — no app server.
- **Elyos pieces:** Task schema (`packages/schema`), agent-neutral CLI workspace prep / PR flow
  (donated lane), good-deed definition + risk-tier governance, review/sign-off process.
- **Human/expert dependency:** agronomy/horticulture reviewers and a partner org — the gating
  non-software dependencies.

## Risks & mitigations

| Risk | Likelihood | Impact | Mitigation | Owner |
| --- | --- | --- | --- | --- |
| Wrong region/zone/date or unsuitable-crop advice wastes a food-insecure household's season | Medium | High | Deterministic, source-backed engine (no LLM-guessed dates) + `derivedFrom` provenance; mandatory region-qualified expert sign-off; golden-calendar tests; treat any escape as an incident | Domain reviewer |
| Recommending an invasive/legally-restricted species (environmental + legal harm) | Medium | High | Mandatory per-region invasive/noxious-list screen in CI + reviewer checkpoint; block recommendation; note restriction | Domain reviewer |
| Copyright/trademark misuse of source content (RHS, seed catalogues, extension docs) | Medium | High | Paraphrase + cite, never copy verbatim or reuse logos; verify each source license; "link, don't embed" default; provenance log | Maintainer |
| Climate data stale vs. shifting zones/frost dates (climate change) | High | Medium | Cite dataset version + retrieval date; record uncertainty; periodic re-review cadence; flag aging units | Domain reviewer |
| Scope creep into foraging / preservation / medicinal / pesticide-rate content (high-stakes) | Medium | High | Hard non-goals; CI/reviewer scope check; link-out pattern; any inclusion requires credentialed expert sign-off via governance | Maintainer |
| Soil-contamination (urban lead) advice given unqualified | Low | High | No remediation advice; flag risk + link to soil-testing authority; reviewer checkpoint | Domain reviewer |
| No partner org secured (verified need unconfirmed) | Medium | High | Pursue partner early in parallel; 6-month-post-M3 decision point to declare "Publicly Shipped (generic public good)"; later endorsement upgrades status | Steward |
| Source agronomy sources conflict on timing/suitability | Medium | Medium | Source-divergence precedence rule (jurisdiction-first; state divergence + cite both); region overrides; logged | Domain reviewer |
| Translation introduces wrong/toxic crop-name confusion | Medium | High | Anchor every crop to its binomial; competent speaker + agronomy/crop-name re-check; not fluency-only | Translation reviewer |
| Accessibility regressions slip in | Medium | High | a11y CI gate (axe/pa11y) blocking merge; periodic manual AT audits; AA hard requirement | A11y reviewer |
| Non-neutral stance on contested agronomy (organic/GMO/pesticide debates) | Low | Medium | Product-neutral, evidence-based, defer to authorities; no advocacy; governance review | Maintainer |
| Maintainer bandwidth / bus factor | Medium | Medium | Reviewer rotation, documented processes, MIT/CC-BY/CC0 low lock-in | Maintainer |

## Security & privacy

**Threat surface.** As a static, no-backend, no-PII site/PWA the attack surface is small. Principal
concerns: (1) dependency supply-chain risk; (2) service-worker cache poisoning / stale guidance;
(3) third-party script/tracker creep; (4) hostile forks implying false agency endorsement;
(5) **data-integrity tampering** that could alter a planting date or suitability verdict.

**Controls.**
- **No secrets** anywhere (no API keys/tokens/credentials in code, logs, or receipts; static hosting).
- **No telemetry / no PII (defense in depth):** strict CSP with **`connect-src 'none'`** (+ locked
  `script-src`/`img-src`/`font-src 'self'`) blocks runtime exfiltration; a **runtime
  network-interception E2E** fails the build on any unexpected outbound request; a static CI audit
  (no analytics/trackers/PII fields) is the cheap first line. User-selected region/crop lists stay
  on-device; export only by explicit action.
- **Supply chain:** pinned/locked deps (pnpm lockfile), dependency review, minimal deps, SRI where
  applicable, CI dependency audit.
- **Service-worker hygiene:** scoped SW, integrity-checked precache, explicit versioning + update
  flow so users aren't stuck on stale guidance; HTTPS-only.
- **Data integrity:** crop/zone/calendar data is build-time **schema-validated, license-checked, and
  invasive-screened**; calendar entries carry `derivedFrom` provenance so a tampered or wrong value is
  detectable against its cited inputs; all guidance source-cited + review-gated.
- **Abuse/misuse:** prominent disclaimer that the tool is **not an official agency product** unless a
  named partner endorses it, and that it covers **cultivated crops only — not foraging, preservation,
  or medicinal use**; trademark/branding terms documented; license requires attribution.

## Sustainability & maintenance

- **Ownership after delivery:** the **maintainer** owns code/data/releases; the **steward** owns
  deployment + the partner relationship. Both **TBD**; must be named before M3.
- **Content freshness:** crop/zone/calendar units carry `lastReviewed` + dataset version/retrieval
  dates; a **periodic re-review cadence** (recommended at least annually, and after any major source
  or climate-data update) is enforced via maintenance tasks; stale units flagged. Climate shift makes
  this non-optional.
- **Outcomes tracking:** no telemetry → outcomes (adoption, households reached, **plots planted**)
  tracked via **partner self-report** + a manual endorsement/MOU record. An explicit
  privacy/measurement trade-off.
- **Low lock-in:** MIT/CC-BY/CC0, static hosting, standard web tech, and a portable open dataset keep
  the project forkable, cheap to run, and survivable.

## Open questions

1. **Partner org:** Who is the named partner (community-garden network / food bank / extension /
   food-security NGO)? The need is well-evidenced in aggregate but unverified for any community — a
   human decision is required before *Definition of Shipped*.
2. **Launch regions/zones:** Which region(s) first? Should be partner-driven; provisional default is a
   well-documented, PD-data-rich region (e.g. a set of US USDA zones) to de-risk the cold start, with
   a Global-South / Southern-Hemisphere region prioritised early to avoid temperate-North bias.
3. **Priority crops:** Which staple/vegetable set first (nutrition + calorie value + ease + cultural
   relevance)? Partner-driven once secured.
4. **Priority languages:** First non-English locale (provisional Spanish (es)); partner may override.
5. **Data license:** CC-BY-4.0 vs. CC0 for the factual dataset (ADR #3) — needs a decision.
6. **Climate rule set:** exact agronomic rules + datasets per region, and how uncertainty/ranges are
   surfaced to users without false precision (ADR #2).
7. **Carve-out policy:** confirm with governance that preservation/foraging/medicinal/pesticide-rate
   content stays out unless brought in under credentialed expert sign-off.
8. **Hosting/deployment target** and who operates it (steward).

## References

- Elyos work rules — `C:\code\elyos\CLAUDE.md`
- Good-deed definition & risk tiers — `C:\code\elyos\docs\good-deed-definition.md`
- Task schema — `C:\code\elyos\packages\schema\src\schemas.ts`
- Portfolio roadmap — `C:\code\elyos\planning\ROADMAP.md`
- Sibling project plan (house style) — `C:\code\elyos\planning\projects\proper-prepper\PLAN.md`
- Project proposal — **TO BE WRITTEN** (`C:\code\elyos\governance\proposals\open-gardening-guides.md`)
- Authoritative data/content sources (license-checked per use): USDA Plant Hardiness Zones,
  Köppen-Geiger climate datasets, FAO/GAEZ + FAOSTAT, NOAA/national met frost-date data, US
  Cooperative Extension / USDA / FAO agronomy publications, and per-region invasive/noxious-weed lists.
- WCAG 2.2 AA (W3C Web Content Accessibility Guidelines).

---

## Appendix A — Improvements applied

The 25 specific improvements below were identified against the first draft and **applied** to the
plan above (and to TASKS.md). Each states the gap and the concrete change made.

1. **Region-correctness reframed as a safety property.** Added explicit language (Exec summary, §8)
   that a wrong date/zone/crop wastes a food-insecure household's season, justifying a medium-style
   accuracy-review bar inside a low-tier project — instead of treating "low risk" as "light review."
2. **Deterministic engine, no LLM in the runtime path.** Made it a hard architectural rule (§6, §14)
   that no model emits a date or suitability verdict at runtime; the engine computes from cited data +
   rules, with `derivedFrom` provenance, so output is reproducible and auditable.
3. **Invasive / restricted-species screening added as a first-class gate.** New mandatory CI check +
   reviewer checkpoint (§7, §8, metrics, risks) — recommending an invasive is an environmental-harm
   defect, a gap the generic "gardening guide" framing would have missed.
4. **Explicit high-risk carve-outs.** Foraging/edibility ID, preservation/canning, medicinal/herbal
   claims, and pesticide application rates were named and **excluded or expert-gated** (§3, §7), with
   a "link to the authority" default — taking real care rather than glossing.
5. **Crop-name safety via binomials.** Every crop anchored to its scientific binomial (§6 data model,
   §7 translations) so a mistranslated common name cannot send someone to the wrong/toxic plant.
6. **"Plots planted" added as the truest outcome metric.** Beyond reach, we track beds actually
   planted (partner-reported, de-duplicated, with uncertainty) — the real-world good, honestly hard
   to measure (§4).
7. **Data-format ADR sequenced before schema/engine.** Made ADR #1 land first and ADR #2 (climate
   model) before any calendar is computed (§6 decision ordering, M0 exit, TASKS sequencing).
8. **Source-divergence precedence rule.** Added jurisdiction-first precedence + "state the divergence,
   cite both" + region overrides for conflicting agronomy sources (§7), mirroring the proper-prepper
   discipline.
9. **License realism on extension content.** Called out that extension/USDA/FAO licenses vary
   per-document and that RHS/seed catalogues/blogs are copyright — paraphrase+cite, link-don't-embed
   (§7).
10. **Climate-shift staleness risk.** Added the climate-change angle: zones/frost dates move, so
    dataset versioning, uncertainty, and a re-review cadence are mandatory (§7, §13, §15).
11. **No-geolocation privacy stance.** Region is user-selected, never geolocated; zero PII; on-device
    only — made explicit (§7, §14) to pre-empt a tempting but privacy-eroding "detect my location."
12. **No false precision.** Frost dates/season length carry ranges + uncertainty and are surfaced
    without implying day-exact certainty (§6, §7, open question 6).
13. **Soil-contamination (urban lead) guardrail.** Added a non-goal + risk row: flag + link to
    soil-testing authority, no unqualified remediation advice (§3, §7, risks).
14. **Non-partisan stance on contested agronomy.** Organic/GMO/pesticide debates handled
    product-neutral, evidence-based, defer-to-authority (§3, §7, risks) — the civic-neutrality
    guardrail applied to a non-civic domain.
15. **Defense-in-depth no-telemetry enforcement.** CSP `connect-src 'none'` + runtime
    network-interception E2E + static audit, not a grep alone (§4, §14) — matching the house standard.
16. **Golden-calendar determinism tests.** Engine output is locked by golden tests so a refactor
    can't silently shift a planting date (§6, §9 M0 exit, TASKS).
17. **No self-approval + credential bar.** author ≠ approver, with a defined agronomy credential bar
    and a PR-tied append-only review log (§7, TASKS review rule).
18. **Anti-North-bias coverage goal.** Open question 2 + roadmap call to prioritise a Global-South /
    Southern-Hemisphere region early, countering temperate-Northern-Hemisphere bias in gardening
    content.
19. **Honest partner status everywhere.** verifiedNeed=false, requestor=TBD, "TO BE SECURED," and a
    "Publicly Shipped (generic public good)" fallback state so a finished resource isn't stranded
    (§2, §7, TASKS).
20. **Printable + low-bandwidth as hard requirements.** Print-snapshot + offline E2E gates and a
    bandwidth budget (§4, §5, §6) — because many beneficiaries use paper and low-end devices.
21. **Accessibility matrix + cadence.** Concrete AT support matrix and audit cadence, not an ad-hoc
    check (§7), with a qualified reviewer.
22. **Outcome includes failures.** Partner self-report explicitly captures what *failed* to grow —
    negative results are valuable agronomic feedback, not hidden (§4).
23. **Reproducibility/provenance on every recommendation.** `derivedFrom { ruleId, inputs }` per
    calendar entry makes each date auditable to its source + rule (§6, §14).
24. **Schema-valid example task + funded-lane note.** TASKS includes a complete AJV-valid Task JSON
    and notes that any funded task needs `fundedBudgetUsd` (this project is donated-lane).
25. **Carve-out governance path.** Defined how a carve-out (e.g. preservation) could ever come
    in-scope: only via Elyos governance + credentialed expert sign-off (§3, §11, open question 7) —
    closing the "someone adds risky content later" hole.

---

## Review sign-off

A completeness + correctness pass was run against the PLAN_SPEC requirements and Elyos guardrails;
findings were applied above. Result of the pass:

- **Measurable metrics:** ✅ §4 has baseline + 12-month targets, privacy-preserving measurement,
  rolling-window/de-dup definitions, and the truest outcome (plots planted) with honest uncertainty.
- **Enforceable gates:** ✅ CI gates (schema, license/provenance, invasive screen, engine
  determinism, a11y, offline/print, no-telemetry) + human review gates (domain accuracy, region
  sign-off, no self-approval, credential bar) are concrete and named, not aspirational.
- **Risks with owners + mitigations:** ✅ §13 table covers region-correctness, invasive species,
  copyright, climate-staleness, scope creep, soil contamination, partner, translation, a11y, and
  neutrality — each with an owner.
- **License / PII / expert guardrails:** ✅ open/PD/CC-only with per-source verification; zero PII +
  no geolocation; high-risk carve-outs excluded or credential-gated; "not advice / cultivated crops
  only" framing.
- **Sequencing:** ✅ ADR #1 (format) → schemas/engine; ADR #2 (climate model) → calendars; M0→M3
  dependencies stated; partner pursued in parallel.
- **Schema-valid tasks:** ✅ TASKS.md maps every field, includes an AJV-valid example with
  `verifiedNeed: false` / `requestor: "TBD"`, and milestone DoDs.
- **Outstanding human decisions (not blockers to M0–M1):** name a partner; choose launch
  regions/crops/languages; decide dataset license (CC-BY vs CC0); confirm carve-out policy with
  governance. Logged in §16.

**Sign-off status:** Draft ready for maintainer + Elyos governance review. No fabricated partner or
verified need; honest gaps are surfaced, not hidden.
