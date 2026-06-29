# TASKS — open-gardening-guides (region-specific, climate-zone-aware open food-growing guides)

> Status: Draft · Version: 0.1.0 · Last updated: 2026-06-28 · Owner: TBD (maintainer) · Lane: donated

## How these tasks map to Elyos

Each task below becomes an Elyos **Task JSON** validated against
`packages/schema/src/schemas.ts`. Field mapping:

- `id` — stable slug ID, e.g. `open-gardening-guides-engine-001`.
- `title` — the task title from the table.
- `project` — `"open-gardening-guides"`.
- `type` — one of `code | research | writing | data | design-spec | maintenance` (the "Type" column).
- `lane` — `"donated"` for all tasks here (no escrow/API spend). A `funded` task would additionally
  require `fundedBudgetUsd` (per the schema's conditional); none are funded in this backlog.
- `priority` — `high | medium | low`.
- `domain` — array, e.g. `["food-security","agriculture","software","accessibility","climate"]`.
- `riskTier` — `low | medium | high` (the "Risk" column). Core crop/calendar content is **low** but
  carries a **mandatory agronomy accuracy review** (a medium-style bar inside a low-tier project, per
  PLAN §8). Carve-out content (foraging/preservation/medicinal/pesticide-rate) is **out of scope**;
  none appears here.
- `urgent` — boolean (default `false`; this is seasonal planning, not live response).
- `deliverable` — `pr | dataset | document | translation` (the "Deliverable" column).
- `tokenEstimate` — `small | medium | large` (the "Size" column).
- `status` — `open | in-progress | review | delivered | done` (all start `open`).
- `context`, `objective`, `acceptanceCriteria[]`, `output` — task narrative + checkable criteria.
- `resources[]` — source/reference URLs or repo paths.
- `requestor` — partner/requestor (**TO BE SECURED**; use `"TBD"` until a partner is named).
- `verifiedNeed` — **`false` until a named partner org confirms need in writing** (honest default).
- `outputLicense` — `"MIT"` for code; `"CC-BY-4.0"` for content/translations; data is `"CC-BY-4.0"`
  or `"CC0-1.0"` per ADR #3 (`open-gardening-guides-arch-002`).

**Reviewer column legend:** `maintainer` (code review), `a11y` (accessibility reviewer),
`agronomy/SME` (agronomy/horticulture domain reviewer; for region-specific timing with real failure
risk, a **region-qualified expert** — extension staff / agronomist / certified Master Gardener for
the region), `translation+agronomy` (competent speaker paired with crop-name + agronomy re-check),
`steward` (partner/last-mile owner).

**Content review rule (all `agronomy/SME` tasks):** the author may **not** approve their own work —
`reviewedBy` (author) and `approvedBy` (SME) must be distinct people, and the approver must meet the
credential bar (current/former Cooperative Extension staff, degreed agronomist/horticulturist, or
certified Master Gardener active in the region, or equivalent). Each approval writes a **PR-tied,
append-only review-log entry** (PR #, commit SHA, unit `dataVersion`, reviewer + approver, sources
checked, source-divergence + **invasive-screen** decisions). This is the auditable record the
Definition of Shipped checks against.

**Sequencing rules.** ADR #1 (data format, `arch-002`) is decided **before** the crop/zone schemas
(`data-004`) and the engine (`engine-005`/`engine-006`) are finalised. ADR #2 (climate model + rule
set + datasets, `arch-003`) is decided **before** any planting calendar is computed or shipped
(`calendar-008` onward). Until those ADRs land, schemas are provisional.

---

## Milestone M0 — Foundation & cold-start

| ID | Title | Type | Size | Risk | Deliverable | Depends on | Reviewer |
| --- | --- | --- | --- | --- | --- | --- | --- |
| open-gardening-guides-arch-002 | ADRs: **data format (decide first)**, UI framework + SW tooling, hosting, print/low-bandwidth budget | design-spec | small | low | document | — | maintainer |
| open-gardening-guides-arch-003 | ADR: climate-zone model — launch-region datasets + agronomic rule set + licenses + uncertainty handling | design-spec | medium | low | document | — | maintainer, agronomy/SME |
| open-gardening-guides-data-004 | Crop-profile + climate-zone + planting-calendar schemas + provenance/review-log format | data | medium | low | dataset | arch-002, arch-003 | maintainer, agronomy/SME |
| open-gardening-guides-engine-005 | Climate-zone engine skeleton (region → zone params → suitability/window) with `derivedFrom` provenance + golden-calendar test harness | code | medium | low | pr | data-004 | maintainer |
| open-gardening-guides-ci-006 | CI gates: lint, typecheck, unit, schema validation, license/provenance check, invasive-species screen, offline + no-telemetry smoke E2E | code | medium | low | pr | engine-005 | maintainer |
| open-gardening-guides-content-007 | Sample crop profile (e.g. bush bean) + one region planting calendar, source-cited + expert-reviewed, rendered in a static page | writing | small | low | document | data-004, engine-005 | agronomy/SME |

**Acceptance criteria — key tasks**

- **open-gardening-guides-arch-003 (climate-model ADR):**
  - Names the exact open datasets defining the launch region's zones + frost dates (with each source's
    **license verified** and recorded) and the **agronomic rule set** (e.g. germination-temperature /
    last-frost-window rules) used to derive planting windows.
  - States how **uncertainty/ranges** are represented (no false day-exact precision) and how
    **source divergence** is resolved (jurisdiction-first precedence; state + cite both).
  - Lands **before** any planting calendar is computed (gates `calendar-008`).
- **open-gardening-guides-data-004 (schemas):**
  - Captures crop fields (binomial, localised names, sow method, min germ temp, frost tolerance,
    days-to-maturity, spacing, sun/water, prevention/IPM-framed problems, harvest indicators),
    zone/region fields (hardiness/Köppen/AEZ, frost-date ranges, season length, **invasiveListRef**),
    and calendar fields (suitability, limitingFactor, actionsByMonth, **derivedFrom**), plus
    `sources[]`, `reviewStatus`, `reviewedBy`, `approvedBy` (distinct), `reviewLogRef`, `lastReviewed`,
    `contentLicense`, `dataVersion`.
  - Format follows the data-format ADR (`arch-002`), which lands first; schema is provisional until then.
  - Build-time validation **rejects** a unit missing required citation/review fields **and** a unit
    where `approvedBy` equals `reviewedBy` (no self-approval).
- **open-gardening-guides-engine-005 (engine skeleton):**
  - Given a region selector (no geolocation), resolves zone parameters and computes per-crop
    suitability + planting window **deterministically from cited data + rules** — **no LLM/model in
    the runtime path** that emits a date or verdict.
  - Every output entry records `derivedFrom { ruleId, inputs }` for full reproducibility.
  - **Golden-calendar tests** lock representative outputs so a refactor cannot silently shift a date.
- **open-gardening-guides-content-007 (sample crop + calendar):**
  - Every claim traceable to a cited authoritative source (USDA/extension/FAO) with retrieval date +
    source license; **no verbatim copying** of copyrighted text; no logos.
  - The crop is **screened against the region's invasive/restricted list** and is in-scope (no
    foraging/preservation/medicinal/pesticide-rate content).
  - Passes `agronomy/SME` accuracy + region-suitability review by an approver **distinct from the
    author** meeting the credential bar; a PR-tied review-log entry is written.

**Definition of Done (M0):** Repo builds (TS/ESM/pnpm); data-format ADR recorded **before** schemas;
climate-model ADR recorded **before** any calendar; crop/zone/calendar schemas + provenance/review-log
defined; deterministic engine skeleton with `derivedFrom` + golden tests; CI enforces
lint/type/unit/schema/license/invasive-screen/offline/no-telemetry; one source-cited, expert-reviewed
crop + region calendar renders from the schema; telemetry/PII audit green (CSP `connect-src 'none'` +
runtime network-interception E2E).

---

## Milestone M1 — Climate-zone engine & core crop set

| ID | Title | Type | Size | Risk | Deliverable | Depends on | Reviewer |
| --- | --- | --- | --- | --- | --- | --- | --- |
| open-gardening-guides-engine-008 | Full engine: real launch-region zone/frost datasets ingested + suitability/window computation across the crop set | code | large | low | pr | engine-005, arch-003 | maintainer, agronomy/SME |
| open-gardening-guides-content-009 | ≥ 10 staple/vegetable crop profiles, source-cited + expert-reviewed + invasive-screened | writing | large | low | document | content-007 | agronomy/SME |
| open-gardening-guides-calendar-010 | First region's full planting calendar (crop × zone × month), expert-signed-off | data | medium | low | dataset | engine-008, content-009 | agronomy/SME |
| open-gardening-guides-ui-011 | Guide renderer + client-side printable/exportable output (low-bandwidth) | code | medium | low | pr | data-004 | maintainer, a11y |
| open-gardening-guides-a11y-012 | WCAG 2.2 AA hardening + manual assistive-tech audit | code | medium | low | pr | ui-011 | a11y |
| open-gardening-guides-partner-013 | Secure named partner org (need, regions, crops, languages, endorsement) | research | medium | low | document | — | maintainer, steward |

**Acceptance criteria — key tasks**

- **open-gardening-guides-engine-008 (full engine):**
  - Ingests the launch region's licensed zone + frost datasets (versioned, with retrieval dates) and
    computes suitability (suitable/marginal/unsuitable + limiting factor) + planting windows for all
    M1 crops, deterministically, with `derivedFrom` provenance on every entry.
  - Surfaces uncertainty/ranges (no false precision); golden-calendar tests extended to cover the
    crop set; no model in the runtime path.
- **open-gardening-guides-content-009 (≥ 10 crops):**
  - Each profile source-cited (org/title/url/retrievedDate/sourceLicense), paraphrased not copied,
    **invasive/restricted-screened** for the launch region, and in-scope.
  - Each approved by an SME distinct from the author (credential bar met) with a review-log entry;
    crop anchored to its scientific **binomial**.
- **open-gardening-guides-calendar-010 (first region calendar):**
  - Every calendar entry is engine-derived (`derivedFrom`) and then **expert-signed-off** for
    region suitability + timing; 0 invasive/restricted recommendations; source divergences logged.
- **open-gardening-guides-a11y-012 (AA hardening):**
  - axe/pa11y pass with 0 critical issues across all core flows.
  - Documented manual audit across the matrix (NVDA+Firefox/Win, JAWS+Chrome/Win,
    VoiceOver+Safari/macOS·iOS, TalkBack+Chrome/Android, keyboard-only per desktop browser), signed
    off by the qualified a11y reviewer; cadence before milestone exit + each release.

**Definition of Done (M1):** Deterministic engine computes suitability + planting windows for a launch
region with full provenance; ≥ 10 source-cited, expert-reviewed, invasive-screened crops; ≥ 1 region's
calendar complete + signed off; printable/exportable guide renders client-side; WCAG 2.2 AA (automated
+ manual) pass; 0 unsourced claims; 0 invasive/restricted recommendations; partner outreach underway.

---

## Milestone M2 — Coverage breadth, offline & i18n

| ID | Title | Type | Size | Risk | Deliverable | Depends on | Reviewer |
| --- | --- | --- | --- | --- | --- | --- | --- |
| open-gardening-guides-pwa-014 | Offline-capable PWA (installable; loads + prints offline after first visit; bandwidth budget) | code | medium | low | pr | ui-011 | maintainer, a11y |
| open-gardening-guides-content-015 | Expand to ≥ 18 crops + ≥ 4 zone/region calendars (incl. a non-temperate-North region) | writing | large | low | document | content-009, engine-008 | agronomy/SME |
| open-gardening-guides-i18n-016 | i18n framework (RTL, bundled offline fonts, ICU plurals, locale formatting) + build-time completeness check | code | medium | low | pr | ui-011 | maintainer, a11y |
| open-gardening-guides-l10n-017 | First non-English locale (UI + ≥ 1 region's content) with localised crop names anchored to binomials | writing | medium | low | translation | i18n-016, content-015 | translation+agronomy |

**Acceptance criteria — key tasks**

- **open-gardening-guides-pwa-014 (offline PWA):**
  - Installs (web app manifest); core guides load **and print** offline after first visit; stays
    within the bandwidth/precache budget set in `arch-002`; explicit versioned update flow; no network
    egress beyond initial load/update (CSP `connect-src 'none'`).
- **open-gardening-guides-content-015 (breadth + anti-North-bias):**
  - ≥ 18 crops + ≥ 4 region calendars, each source-cited, expert-reviewed, invasive-screened;
    **at least one region is non-temperate-Northern-Hemisphere** to counter coverage bias.
- **open-gardening-guides-l10n-017 (first locale):**
  - UI strings + ≥ 1 region's content localised; crop names localised **and anchored to binomials**;
    reviewed for crop-name correctness + agronomic accuracy (not fluency only); safe fallback to
    English; i18n assets work offline.

**Definition of Done (M2):** Installable offline PWA (loads + prints offline, within budget); ≥ 18
crops; ≥ 4 region calendars incl. a non-temperate-North region; i18n framework with CI completeness
check; ≥ 1 non-English locale (UI + ≥ 1 region's content) with binomial-anchored crop names,
accuracy-reviewed; safe locale fallback verified.

---

## Milestone M3 — Depth, partner adoption & shipped

| ID | Title | Type | Size | Risk | Deliverable | Depends on | Reviewer |
| --- | --- | --- | --- | --- | --- | --- | --- |
| open-gardening-guides-content-018 | Reach ≥ 25 crops + ≥ 8 zone/region calendars, all reviewed | writing | large | low | document | content-015 | agronomy/SME |
| open-gardening-guides-l10n-019 | Reach ≥ 2 fully localised languages (UI + key regions) | writing | large | low | translation | l10n-017 | translation+agronomy |
| open-gardening-guides-deploy-020 | Production deployment + versioned cache/update + disclaimers (cultivated-crops-only; not-an-official-agency-tool) | code | medium | low | pr | a11y-012, pwa-014 | maintainer, steward |
| open-gardening-guides-ops-021 | Outcomes-tracking process (partner self-report incl. plots-planted) + content/climate re-review cadence | maintenance | small | low | document | partner-013 | maintainer, steward |

**Acceptance criteria — key tasks**

- **open-gardening-guides-partner-013 (secure partner) [carried from M1]:**
  - A named community-garden/food-security/extension org confirms the need in writing (letter of
    support or MOU) and identifies priority regions, crops, and languages.
  - On success, `verifiedNeed` flips to `true` and `requestor` is set to the named org across tasks.
- **open-gardening-guides-deploy-020 (production deploy):**
  - Static production build over HTTPS; versioned precache + update flow so users aren't stuck on
    stale guidance; visible disclaimers that the tool covers **cultivated crops only (not foraging,
    preservation, or medicinal use)** and is **not an official agency product** unless a partner
    endorses it; trademark/attribution terms present.
- **open-gardening-guides-ops-021 (outcomes + freshness):**
  - Documented privacy-preserving outcomes process (partner self-report; no in-app telemetry) using
    the standard template (partner, period, channels, **distinct households reached (confirmed)**,
    flagged estimates, **plots/beds planted (confirmed)**, regions/zones/languages, de-dup notes, and
    what failed to grow) over a rolling 12-month window; counts distinct households/plots, once per
    period, de-duplicated across partners.
  - Re-review cadence defined (≥ annual + after major source/climate-data updates); stale-unit
    flagging based on `lastReviewed` + dataset version.

**Definition of Done (M3):** ≥ 25 reviewed crops; ≥ 8 region calendars; ≥ 2 localised languages;
**named partner endorsement/adoption on file** (verifiedNeed = true); production build deployed with a
safe update strategy + disclaimers; outcomes tracking (incl. plots planted) + re-review cadence
operational. This satisfies the project-level *Definition of Shipped (partner-adopted)*.

**Decision point (so a finished resource isn't stranded):** if no partner is secured by **6 months
after the M3 production build is ready**, the steward + Elyos governance declare **"Publicly Shipped
(generic public good)"** — criteria (1)–(4) met, deployed + distributed directly/via community
channels, outcomes by best-effort self-report. A later partner endorsement upgrades the status rather
than re-opening launch.

---

## Backlog / future

| ID | Title | Type | Size | Risk | Deliverable | Notes |
| --- | --- | --- | --- | --- | --- | --- |
| open-gardening-guides-content-022 | Container / small-space / balcony growing variants | writing | medium | low | document | In-scope; SME-reviewed |
| open-gardening-guides-content-023 | Companion-planting + crop-rotation guidance (evidence-based, not folklore) | writing | medium | low | document | Cite evidence; flag low-evidence claims |
| open-gardening-guides-content-024 | Composting + soil-health basics (no contamination remediation advice) | writing | medium | low | document | Link to soil-testing authority |
| open-gardening-guides-feat-025 | Pictograph/icon set for low-literacy + multilingual users | design-spec | medium | low | document | License-clean assets only |
| open-gardening-guides-feat-026 | Region-aware crop relevance picker (advisory; no geolocation/PII) | code | medium | low | pr | User selects region; no tracking |
| open-gardening-guides-content-027 | Seed-saving basics (cultivated crops; no IP-restricted varieties) | writing | medium | low | document | Note PVP/patented-variety restrictions |
| open-gardening-guides-feat-028 | Native app-store wrappers (TWA/Capacitor) | code | large | low | pr | Out of launch scope |
| open-gardening-guides-ops-029 | Annual content + climate-data re-validation pass | maintenance | medium | low | document | Recurring; climate-shift driven |
| open-gardening-guides-sec-030 | Dependency/supply-chain audit + SRI hardening | maintenance | small | low | pr | Recurring |
| open-gardening-guides-content-031 | "Link-out" reference pages for excluded carve-outs (preservation/foraging/pesticide → authorities) | writing | small | low | document | Pure link-out; no instruction |

---

## Example task JSON

Complete, schema-valid Task JSON for the first M0 task. `verifiedNeed` is `false` and `requestor` is
`"TBD"` because **no partner org is secured yet** (honest default per the plan). Lane is `donated`, so
no `fundedBudgetUsd` is required.

```json
{
  "id": "open-gardening-guides-arch-002",
  "title": "ADRs: data format (decide first), UI framework + SW tooling, hosting, print/low-bandwidth budget",
  "project": "open-gardening-guides",
  "type": "design-spec",
  "lane": "donated",
  "priority": "high",
  "domain": ["food-security", "agriculture", "software", "climate"],
  "riskTier": "low",
  "urgent": false,
  "deliverable": "document",
  "tokenEstimate": "small",
  "status": "open",
  "context": "open-gardening-guides publishes deterministic, source-cited, climate-zone-aware food-growing guidance as an open dataset rendered by a static, offline-capable, accessible site. Foundational architecture decisions must be recorded before schemas and the climate-zone engine are built. Per the plan, the data-format ADR is decided FIRST because the crop/zone schemas and the engine depend on it.",
  "objective": "Record ADRs for: (1) data format and how citations/review status are enforced in CI (decided first); (2) UI framework + service-worker tooling weighted for accessibility, bundle size, offline support, and i18n/RTL; (3) static hosting target and versioned update strategy; (4) print/export approach and a low-bandwidth/precache budget.",
  "acceptanceCriteria": [
    "Data-format ADR is recorded and explicitly marked as the decision that gates the crop/zone/calendar schemas (data-004) and the engine (engine-005)",
    "ADR specifies how source citations and review/approval status are enforced at build time (CI), including rejection of units missing citations or with approvedBy == reviewedBy",
    "UI-framework + service-worker ADR documents accessibility, bundle-size, offline, and i18n/RTL support as weighted criteria",
    "Hosting ADR selects a static host (no app server) and a versioned cache/update strategy so users are not stuck on stale guidance",
    "Print/export ADR defines a client-side printable output and an explicit low-bandwidth/precache budget",
    "Each ADR records context, options considered, decision, and consequences; committed in-repo"
  ],
  "resources": [
    "C:\\code\\elyos\\planning\\projects\\open-gardening-guides\\PLAN.md",
    "C:\\code\\elyos\\packages\\schema\\src\\schemas.ts",
    "https://www.w3.org/WAI/WCAG22/quickref/"
  ],
  "output": "A document (set of ADRs) recording the data-format (decided first), UI-framework + SW-tooling, hosting/update, and print/low-bandwidth decisions, committed to the repo as the architectural baseline for M0.",
  "requestor": "TBD",
  "verifiedNeed": false,
  "outputLicense": "CC-BY-4.0"
}
```
