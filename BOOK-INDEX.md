# Splunk Security Operations

**BOOK-INDEX.md — canonical part list and appendix list.**
**Status:** Adopted architecture, pre-authoring. A title in the NESHBOY SOC Professional Library.
**Sibling volume:** *The Detection Engineering Handbook* (DEH), V2 — `C:\Users\User\projects\detection-engineering-handbook\release-v2\`. This book assumes DEH Part 23 (Query Language Strategy) and DEH Part 26 (Splunk SPL) as prerequisite reading and does not re-teach either. See `STYLE-GUIDE.md` §0 for the exact non-duplication boundary.

## What this book is

DEH Part 26 teaches Splunk's Search Processing Language — `stats`, `transaction`, subsearch cost, the search-time/index-time field split — at a query-language-comparison level, deliberately scoped short: its own closing section states it "does not cover Splunk administration, index/bucket lifecycle management, or `tstats`/data-model acceleration in depth... a full accelerated-search treatment belongs in a dedicated performance-engineering appendix, not here." *Splunk Security Operations* is that appendix, expanded into a full platform-operations handbook: everything a detection engineer, SIEM administrator, SOC analyst, threat hunter, or SOC manager needs to run Splunk itself as a security platform, once they already know how to write the query.

Like DEH, this book runs a **telemetry-layer vs. analytic-layer** distinction through its domain parts, adapted to a single-platform scope: a **platform layer** (indexes, sourcetypes, data models, acceleration, licensing — what Splunk itself is and how it's built) and an **operations layer** (correlation searches, risk-based alerting, notable-event triage, dashboards, investigation workflow — what a SOC actually does with that platform). Every part states, in its opening scope paragraph, which layer it is and which neighboring part owns the layer it isn't covering — the same mechanical fix DEH uses against V1's silent-duplication problem, applied here to prevent this book's own parts from re-explaining each other.

**Evidentiary honesty, stated up front:** this book is written without a real Splunk deployment in the author's lab. Every platform-behavior claim is sourced from Splunk's own public documentation, product pages, and Splunkbase listings (cited in `REFERENCES.md`, to be created alongside the first authored part) or stated as an explicitly labeled conceptual illustration — never presented as something observed in a live environment that doesn't exist. `STYLE-GUIDE.md` §9 makes this a structural rule: this book's figures are, and are expected to remain, almost exclusively `OFFICIAL REFERENCE` and `CONCEPTUAL`, with zero `CONTROLLED LAB EXAMPLE` or `REAL LAB EXAMPLE` tags unless that changes and is disclosed. Part 20 closes the book by naming exactly what that constraint leaves unverified.

## Multi-level content model

Adapted from DEH's six content tags for a platform-operations audience (full rationale in `STYLE-GUIDE.md` §7); two are renamed, four are reused with a platform-scoped definition:

- `[CONCEPT]` — foundational "what and why" for a platform mechanism (a data model, a risk object, a `tsidx` summary), no assumption the reader acts on it directly.
- `[SOC ANALYST]` *(renamed from DEH's `[ANALYST]`)* — triage-facing: what a notable event's urgency/severity means, how to work it in Incident Review, escalation criteria inside Enterprise Security specifically.
- `[DETECTION ENGINEER]` — correlation-search and RBA rule logic, field selection against CIM/data models, thresholds, adaptive response actions.
- `[THREAT HUNTER]` — hypothesis-driven exploration through Splunk's own tooling: Pivot, ad hoc `tstats` sweeps, pivoting from a risk object into raw search.
- `[PLATFORM ENGINEER]` *(renamed from DEH's `[ENGINEERING]`)* — the Splunk platform itself as infrastructure: indexes, buckets, licensing, acceleration, clustering, search performance.
- `[SOC MANAGEMENT]` — licensing cost, ES edition tradeoffs, staffing, risk-acceptance framing.

Layered on top: DEH's eight recurring callouts (**Detection Autopsy**, **Hunter's Note**, **Engineering Reality**, **Blind Spot**, **False Positive Trap**, **Detection Test**, **SOC Management View**, **What Would Change My Mind**), reused with unchanged templates, plus one new ninth callout this book adds — **Product Version Note** — flagging any claim about current Splunk editions, defaults, or feature availability that's verified as of a specific date and expected to go stale as Splunk ships. Full templates in `STYLE-GUIDE.md` §6.

## Production model

Same mandatory front matter as DEH (`author`, `reviewer`, `status`, `last_validated`, `depends_on`) and the same separation-of-duties requirement — no unit ships to `released` without a reviewer sign-off recording at least one attempted technical objection. This book's reviewer checklist (`STYLE-GUIDE.md` §11) adds two gates DEH's doesn't need: every version/edition/default claim must carry a Product Version Note, and every figure must be checked against the zero-`CONTROLLED LAB EXAMPLE`/zero-`REAL LAB EXAMPLE` default. A `REFERENCES.md`, tracking every `OFFICIAL REFERENCE` citation, is created alongside the first authored part and is load-bearing for this book the way `VISUAL-INVENTORY.md` is for DEH.

---

## Part Table

*File path pattern:* `C:\Users\User\projects\splunk-security-operations-handbook\chapters\partNN-slug.md`

### Section A — The Platform Underneath the Query Language

| Part | Title | File Path | Scope | Recurring Features |
|---|---|---|---|---|
| 1 | Why This Book Exists: From SPL Syntax to Platform Operations | `chapters\part01-why-this-book-exists.md` | Bridging part. States the non-duplication boundary against DEH Part 23 (query-language strategy, DET-23-01) and DEH Part 26 (SPL syntax, DET-26-01) explicitly; previews the platform-layer/operations-layer split this book runs throughout; states the evidentiary-honesty constraint (no real Splunk deployment) as a design fact, not a disclaimer. | Engineering Reality, Product Version Note |
| 2 | Splunk Deployment Architecture | `chapters\part02-splunk-deployment-architecture.md` | Platform layer. Indexers, search heads, forwarders (universal/heavy), deployment servers, indexer/search-head clustering, on-prem vs. Splunk Cloud topology choices, and how a detection's search actually gets distributed and executed across that topology. | Engineering Reality, Product Version Note |
| 3 | Indexes, Sourcetypes, and the Bucket Lifecycle | `chapters\part03-indexes-sourcetypes-and-the-bucket-lifecycle.md` | Platform layer. Index design as a security-relevant decision (retention isolation, index-level RBAC, multi-tenancy), sourcetype assignment and drift, hot/warm/cold/frozen bucket mechanics, and how retention policy is actually enforced at the bucket level rather than the query level. | Engineering Reality, Blind Spot |
| 4 | Licensing and Ingest Economics | `chapters\part04-licensing-and-ingest-economics.md` | Platform layer, SOC-management-facing. Ingest-volume vs. workload-based licensing models, license-warning/violation mechanics, and the cost levers a security team actually controls (index design, retention, filtering at the heavy forwarder, summary indexing) before "reduce license usage" becomes "reduce visibility." | SOC Management View, Product Version Note |

### Section B — Normalizing Data Into a Common Model

| Part | Title | File Path | Scope | Recurring Features |
|---|---|---|---|---|
| 5 | The Common Information Model: What It Actually Standardizes | `chapters\part05-the-common-information-model.md` | Platform layer. CIM as a field-naming and tagging contract (`tags.conf`, `eventtypes.conf`, field aliases), not a magic normalizer — what CIM compliance means for a given sourcetype, and why "CIM-compliant" is a testable, falsifiable claim rather than a checkbox. Cross-references DEH Part 6 (Normalisation/ECS/OCSF/UDM/ASIM) for the general translation-problem framing this part applies specifically to Splunk. | Engineering Reality, Product Version Note |
| 6 | Data Models and Pivot | `chapters\part06-data-models-and-pivot.md` | Platform layer. Data model structure and inheritance, how a CIM data model (e.g., Authentication, Network Traffic) becomes a queryable dataset, and Pivot as a knowledge-object-driven query builder for analysts who don't write SPL directly. | Concept, Product Version Note |
| 7 | Building CIM-Compliant Data Onboarding | `chapters\part07-building-cim-compliant-data-onboarding.md` | Platform layer, implementation-focused. The practical work of mapping a new log source's raw fields onto CIM field names — field aliasing, `tags.conf`/`eventtypes.conf` authoring, validating against the CIM Add-on's own compliance checks — and what silently breaks (Detection Debt's translation-layer failure mode, per DEH Part 23 §3) when that mapping goes stale after a source-format change. | Detection Autopsy, Engineering Reality |

### Section C — Reusable Detection Infrastructure

| Part | Title | File Path | Scope | Recurring Features |
|---|---|---|---|---|
| 8 | Lookup Tables as Detection Infrastructure | `chapters\part08-lookup-tables-as-detection-infrastructure.md` | Operations layer. CSV lookups vs. KV Store collections, automatic lookups, versioning and review of lookup content as a maintained artifact rather than a one-off file — directly extends DEH Part 26 §4.3's lookup-vs-subserach guidance into how a lookup table is actually built, deployed, and kept current at platform scale. | Detection Autopsy, False Positive Trap |
| 9 | Search Macros and Reusable Logic | `chapters\part09-search-macros-and-reusable-logic.md` | Operations layer. Macro definitions and arguments, eval-based macros, macro versioning/testing as code, and macro sprawl as its own form of detection debt when nobody owns the shared logic every correlation search quietly depends on. | Engineering Reality, Blind Spot |

### Section D — Performance at Scale

| Part | Title | File Path | Scope | Recurring Features |
|---|---|---|---|---|
| 10 | Data Model and Report Acceleration | `chapters\part10-data-model-and-report-acceleration.md` | Platform layer. `tsidx` summary mechanics behind an accelerated data model, `tstats` as the command that reads them directly (named but not syntax-taught in DEH Part 26 §1.3 — this part is where that forward reference resolves), acceleration's storage/maintenance cost, and summary-range coverage gaps as a specific, named blind spot. | Engineering Reality, Blind Spot |
| 11 | Summary Indexing and Other Pre-Computation Patterns | `chapters\part11-summary-indexing-and-pre-computation.md` | Platform layer. Summary indexing vs. data model acceleration as two different pre-computation strategies, `sistats`/`sitimechart`, and the staleness/backfill risk a summary index carries that a live accelerated search doesn't. | Engineering Reality, What Would Change My Mind |
| 12 | Search Performance and Tuning at Scale | `chapters\part12-search-performance-and-tuning-at-scale.md` | Platform layer. Search concurrency limits and skipped-search mechanics, the Search Job Inspector as the primary diagnostic tool, scheduling priority and workload management, and real-time vs. scheduled search cost — the direct payoff of DEH Part 26 §1.1's "cost is paid left to right" principle at fleet scale rather than single-query scale. | Engineering Reality, Product Version Note |

### Section E — Splunk Enterprise Security

| Part | Title | File Path | Scope | Recurring Features |
|---|---|---|---|---|
| 13 | Enterprise Security Architecture, Editions, and the CIM Dependency | `chapters\part13-enterprise-security-architecture-and-editions.md` | Operations layer, foundational. ES as an app layered on the platform (not a separate product), its current Essentials/Premier edition split and what each gates (UEBA, SOAR integration are Premier-only as of this writing), and why every downstream ES feature in this section inherits Part 5–7's CIM-compliance requirement as a hard dependency, not an optional best practice. | Product Version Note, Engineering Reality |
| 14 | Correlation Searches: From Analytic to Notable Event, and Their Lifecycle | `chapters\part14-correlation-searches.md` | Operations layer, flagship depth. Correlation-search anatomy (`savedsearches.conf` stanza, schedule, throttling, adaptive response actions), using DEH Part 23's canonical DET-23-01 analytic and DEH Part 26's DET-26-01 SPL rule as the literal search body this part turns into a scheduled, throttled, notable-generating correlation search; also covers content-lifecycle practice — versioning correlation searches as code, promoting them across environments, and the ES Content Update model — extending DEH Part 22's detection-as-code discipline to Splunk's own content-packaging mechanism. | Detection Autopsy, Detection Test |
| 15 | Risk-Based Alerting: Risk Objects, Risk Scores, and the Risk Index | `chapters\part15-risk-based-alerting.md` | Operations layer. Risk objects (user/host/IP), risk-score annotation on every match rather than a notable per match, the risk index as its own retained dataset, and threshold-based notable generation — including the honest treatment of Splunk's own marketed alert-volume-reduction figures as a vendor claim, not a measured result. | Product Version Note, What Would Change My Mind |
| 16 | Notable Events and the Incident Review Workflow | `chapters\part16-notable-events-and-incident-review.md` | Operations layer, analyst-facing. Urgency/severity computation, notable-event status and ownership fields, working a notable in Incident Review, and the specific difference between triaging a traditional per-event notable and triaging an RBA-generated one with multiple contributing events behind it. | SOC Management View, False Positive Trap |
| 17 | Asset and Identity Correlation | `chapters\part17-asset-and-identity-correlation.md` | Operations layer. The Asset and Identity framework's enrichment of notables and risk objects with asset criticality and identity context, and the specific failure mode when asset/identity lookup data goes stale — a notable enriched with a decommissioned host's old criticality rating is a wrong-priority alert, not a missing one. | Blind Spot, False Positive Trap |

### Section F — Dashboards, Investigation, and the Extended Ecosystem

| Part | Title | File Path | Scope | Recurring Features |
|---|---|---|---|---|
| 18 | Dashboards and Visualizations for Security Operations | `chapters\part18-dashboards-and-visualizations.md` | Operations layer. Simple XML vs. Dashboard Studio (naming which is current default vs. legacy-but-still-real, per Product Version Note discipline), panel-design patterns for SOC consumption, and accelerated searches (Part 10) as the thing that keeps a dashboard's panels from timing out at scale. | Product Version Note, SOC Management View |
| 19 | Splunk-Specific Investigation Workflows | `chapters\part19-splunk-specific-investigation-workflows.md` | Operations layer. The actual pivot path an analyst or hunter follows inside Splunk — from a notable event, to its risk object's contributing events, to raw search against the underlying data model, to asset/identity context (Part 17) — and where that workflow's own tooling has changed name/shape across recent ES releases. | Hunter's Note, Product Version Note |
| 20 | What This Book Doesn't Know Yet: Validation Gaps and the Path to Real Evidence | `chapters\part20-validation-gaps-and-the-path-to-real-evidence.md` | Capstone, cross-cutting. An honest inventory of every claim in Parts 1–19 that rests on `OFFICIAL REFERENCE` or `CONCEPTUAL` evidence rather than a real deployment, organized by what specific observation (per DEH's own falsifiability convention) would upgrade each one — and what standing up a real Splunk lab would need to look like to start closing the gap. Synthesis, not new platform material. | What Would Change My Mind, Product Version Note |

**Total: 20 parts.**

---

## Appendix Table

*File path pattern:* `C:\Users\User\projects\splunk-security-operations-handbook\appendices\aN-slug.md`

| Appendix | Title | File Path | Contents |
|---|---|---|---|
| A1 | Splunk Object & Naming Quick Reference | `appendices\a1-splunk-object-and-naming-quick-reference.md` | Index/sourcetype naming conventions, data model and dataset naming, correlation-search title conventions, lookup and macro naming — the companion reference for `STYLE-GUIDE.md` §4's notation rules, built out to full field/object tables as parts are authored. |
| A2 | Cross-Reference Map to the Detection Engineering Handbook | `appendices\a2-cross-reference-map-to-deh.md` | A table mapping every part of this book to the specific DEH part(s) it depends on, defers to, or extends (e.g., Part 10 → DEH Part 26 §1.3's named-but-undeveloped `tstats` reference; Part 14 → DEH Part 23's DET-23-01 and DEH Part 26's DET-26-01; Part 7 → DEH Part 6's normalization framing) — kept current as both books evolve so neither silently drifts out of sync with the other. |

**Total: 2 appendix bundles.**

---

## Key structural decisions and provenance

1. **Part 1 exists solely to state the non-duplication boundary against DEH Parts 23 and 26.** Without an explicit bridging part, every later part would need its own ad hoc disclaimer about what it isn't re-teaching — DEH's own Part 23 plays exactly this role for its six language parts, and the pattern is reused here for the same reason.
2. **Index/sourcetype architecture and licensing get three dedicated parts (2–4) rather than a single "Splunk admin" part.** DEH Part 26 explicitly named this gap and explicitly declined to fill it ("a full accelerated-search treatment belongs in a dedicated performance-engineering appendix, not here") — this book takes that scope note as its own founding brief, and a topic DEH flagged as needing dedicated depth gets dedicated depth, not a single compressed chapter.
3. **CIM and data models get three parts (5–7), split into concept, mechanism, and implementation.** DEH Part 6 (Normalisation) already covers the general translation-problem framing across ECS/OCSF/UDM/ASIM/CIM; this book's Part 5 cross-references that rather than repeating it and instead goes deep on what's Splunk-specific — the concept (5), how it becomes a queryable data model (6), and the maintained implementation work of keeping a source CIM-compliant (7). Collapsing these into one part would repeat DEH Part 26's Part 23 §3 mistake of treating "the mapping exists" and "the mapping still works" as one claim instead of two.
4. **Accelerated searches/summary indexing (10–11) are kept separate from general search performance/tuning (12).** Acceleration and summary indexing are pre-computation decisions made before a query ever runs; performance tuning is about what happens when a query runs against whatever pre-computation already exists. Conflating them was the exact gap DEH Part 26 named and stepped around ("tstats... not covered in depth here") — separating them here keeps each on the design layer it actually belongs to.
5. **Enterprise Security is split into five parts (13–17)**, not one "Enterprise Security" mega-chapter, following the same audience-segmentation logic DEH itself applies (e.g., DEH's Identity split into Parts 12/13, Cloud split into Parts 18/19): architecture/editions (13), rule-building (14), scoring mechanics (15), analyst-facing triage (16), and entity-enrichment (17) are genuinely different audiences and different failure modes, and merging them would force a reader who only needs Incident Review guidance to wade through correlation-search `.conf` internals first.
6. **Part 14 absorbs content-lifecycle/governance (versioning correlation searches, ES Content Update) rather than spinning off a 21st part.** It's a natural extension of "how a correlation search actually gets built and deployed," and folding it in keeps the book at 20 parts — the top of the requested 16–20 range — instead of drifting past it for a topic that's a subsection of Part 14's own subject, not a new one.
7. **Part 20 is a deliberate, disclosed capstone, not padding.** DEH's own Part 40 (Detection Autopsy) and Part 43 (Detection Debt) both close out earlier material as synthesis rather than introducing new subject matter; this book's evidentiary position (no real Splunk deployment) makes an equivalent honesty capstone more load-bearing here than in DEH, not less — it's the single place a reader can see, in one pass, exactly which of this book's claims are `OFFICIAL REFERENCE`/`CONCEPTUAL` and what would be needed to upgrade them, rather than that constraint being scattered across twenty separate Product Version Notes with no synthesis.
8. **Product Version Note is added as a ninth callout, not folded into Engineering Reality.** DEH's six query languages are comparatively stable specifications; Splunk Enterprise Security's feature surface has visibly reorganized within the current release cycle alone (the Essentials/Premier edition split, a newly-branded "Detection Studio" and "AI Assistant," confirmed against Splunk's own current product page and Splunkbase listings during this outline's research). That risk profile is distinct enough from DEH's general "documented vs. actual production behavior" gap (Engineering Reality's job) to earn its own grep-able callout rather than being absorbed into one that already means something else.
9. **`[ANALYST]` and `[ENGINEERING]` are renamed to `[SOC ANALYST]` and `[PLATFORM ENGINEER]`; the other four DEH tags are kept as-is.** Full rationale in `STYLE-GUIDE.md` §7 — in short, this book talks about Splunk's own Incident Review workflow often enough that the triage-facing tag needs to be unambiguous against the informal "Splunk analyst" sense of the word, and this book's engineering scope (the Splunk platform specifically) is categorically narrower than DEH's general pipeline-engineering scope, so the tag name says so.
10. **20 total parts** — 4 short of DEH's 48, deliberately: this is a single-platform operations title, not a cross-domain detection-engineering survey, and its scope is bounded by what DEH itself named as out-of-scope for Part 26 plus Enterprise Security's own operational surface. Landing at the top of the requested 16–20 range reflects that Enterprise Security alone (13–17) and performance/acceleration alone (10–12) both earned real depth rather than a single compressed chapter each.
