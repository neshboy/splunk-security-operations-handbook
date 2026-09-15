# Splunk Security Operations — STYLE-GUIDE.md

**Status:** Adopted, pre-authoring. Applies to every part, appendix, and diagram in this book before any unit is drafted or reviewed.
**Applies to:** every writer, technical reviewer, and editor working on *Splunk Security Operations*, a title in the NESHBOY SOC Professional Library.
**Audience:** every writer, technical reviewer, and editor working on this book.
**Sibling volume:** *The Detection Engineering Handbook* (DEH), V2, `C:\Users\User\projects\detection-engineering-handbook\release-v2\`. This document is derived from `release-v2\STYLE-GUIDE.md` and keeps that document's structural conventions (callout templates, evidence-classification taxonomy, tag format, heading levels) unchanged wherever this book has no platform-specific reason to diverge. Every divergence below is named and justified, not silent drift.

## Why this document exists

DEH V2's own STYLE-GUIDE.md exists because 55 independently-drafted V1 units produced five renderings of one callout box and zero rendered diagrams. This book starts from that lesson already learned rather than re-deriving it: the callout shapes, the tag format, and the evidence-classification discipline below are inherited, not reinvented. What *is* new here is specific to this book's subject and its evidentiary position — a platform-operations title about one commercial product, written without a real deployment of that product in the author's own lab. Two things follow from that, and this guide exists mainly to nail them down before a single part is drafted:

1. **This book is not a second SPL syntax reference.** DEH Part 26 already teaches `stats`, `transaction`, subsearch cost, and the search-time/index-time field split at the language level, and DEH Part 23 already covers Sigma-vs-native authoring tradeoffs and cross-backend translation loss. This book assumes the reader has read those or can be pointed to them, and it earns its own existence by covering what neither part does: the platform underneath the query language — index/sourcetype architecture, the Common Information Model (CIM) and data models, Splunk Enterprise Security's notable-event and risk-based-alerting machinery, accelerated searches, lookups and macros as maintained infrastructure, search performance at scale, and Splunk-specific investigation workflow. §0 below states the non-duplication boundary explicitly so no unit re-teaches what DEH already owns.
2. **This book has to be honest about what backs its claims.** The author's home lab (documented separately) is real infrastructure — Proxmox, a honeynet, a vulnerability-scanner platform — and none of it runs Splunk. Every platform-behavior claim in this book is therefore sourced from Splunk's own public documentation and product pages, or stated as a conceptual illustration, never as something captured from a live deployment that doesn't exist. §9 makes this a structural rule, not a disclaimer buried in a footnote.

**"Must"** means a PR gets rejected if it doesn't comply. **"Should"** means deviate only with a reason recorded in the PR description. **"Avoid"** is a strong default a reviewer can override with justification, and a documented override is a guide change, not silent drift.

This guide governs prose voice, Markdown conventions, callout boxes, content-level tags, and the evidence-classification system for figures. It does not cover technical review standards (accuracy of a specific CIM field mapping, whether a stated `tstats` behavior is correct for a given Splunk version) — that is a separate reviewer concern, and for this book specifically it means every technical claim must be traceable to a cited public source or explicitly marked conceptual before a reviewer signs off. See §9.4.

---

## 0. Relationship to the Detection Engineering Handbook

- **Cross-reference format:** `DEH Part 23 §<n>` or `DEH Part 26 §<n>` on first reference in a section; bare `Part 23`/`Part 26` afterward within the same section. Always resolve to the real file: `detection-engineering-handbook\release-v2\chapters\part23-query-language-strategy.md` / `...\part26-splunk-spl.md`. Never restate DEH's own content at length to make a cross-reference "self-contained" — a one-clause summary plus the pointer is correct; a paragraph-long recap is duplication DEH's own BOOK-INDEX.md was explicitly designed to prevent.
- **What DEH Part 26 owns, and this book never re-teaches:** SPL syntax fundamentals — the pipeline model, `search`/`eval`/`where`/`rex`, `stats` function syntax, `transaction` syntax, subsearch mechanics, and the search-time-vs-index-time field distinction at the language level. When this book needs any of that (and it will — correlation searches and RBA rules are SPL), state the one sentence of context a reader needs and point to the relevant DEH Part 26 section rather than re-deriving it.
- **What DEH Part 23 owns, and this book never re-teaches:** the Analytic-vs-Detection-Rule distinction, Sigma-first-vs-native-authoring tradeoffs, and cross-backend translation loss in the general case. This book's correlation-search and RBA content assumes that vocabulary; where DET-23-01 (the canonical LSASS-access analytic carried through DEH Parts 24–29) is the cleanest illustration of a platform-specific concept here, reuse it and its ID rather than inventing a parallel example — a reader who has read DEH Part 26's DET-26-01 SPL rule should recognize it immediately when this book turns it into a correlation search in §14.
- **What this book owns, that DEH explicitly declined to cover:** DEH Part 26's own scope note says it "does not cover Splunk administration, index/bucket lifecycle management, or `tstats`/data-model acceleration in depth... a full accelerated-search treatment belongs in a dedicated performance-engineering appendix, not here." This book *is* that appendix, expanded to a full title. Cite that DEH Part 26 scope note when framing this book's own opening part.
- **Detection ID discipline:** this book does not mint new `DET-##-##` IDs that compete with DEH's global ledger. Where a unit implements or extends a DEH-defined analytic as a correlation search or RBA rule, it is annotated as a platform-specific realization of that existing ID (e.g., "DET-26-01, deployed as a scheduled correlation search"), not renumbered. Where a unit needs an example with no DEH antecedent (a CIM-mapping illustration, a lookup-table pattern), it is marked `CONCEPTUAL SAMPLE` per §3/§9 rather than assigned a DET ID at all — this book does not own a detection ledger.

---

## 1. Voice and Tone

### 1.1 The core rule

Identical to DEH's core rule, restated for this audience: write like a Splunk engineer explaining a design decision to the person who inherits the search head cluster after them — not like a Splunk partner's sales deck. The reader already knows what a SIEM is; they don't need to be sold on visibility. Every sentence should survive: **what does this actually tell me to configure, check, or expect?**

Concretely, unchanged from DEH:

- State the claim, then the evidence.
- Name the failure mode; don't gesture at it. "This silently stops matching after a TA upgrade renames the field" beats "data quality issues can occur."
- Prefer the concrete number over the vague qualifier — and for this book specifically, prefer the *cited, sourced* number over an invented one. "Splunk's own Enterprise Security marketing states risk-based alerting can cut notable-event volume by up to 90%; that figure is Splunk's, not independently verified here" beats either silently repeating "90%" as fact or vaguely saying "significantly reduces alert volume."
- Name the mechanism, the `.conf` stanza, the command, the field — not the adjective.
- Active voice with a named actor ("the correlation search," "the indexer," "the analyst," "Splunk") over passive constructions that hide the actor.
- Commit to a claim. If a platform behavior isn't verified against current documentation, say so ("unverified against a live 10.x deployment — confirm against your own `tstats` output before trusting this at production scale") rather than hedging vaguely.
- Second person for procedural instruction, first person plural for the book's own reasoning, same as DEH.
- It's fine to say a Splunk feature is boring, expensive, or already solved by a config change. Not everything is a differentiator.

### 1.2 Banned filler

Identical table to DEH STYLE-GUIDE.md §1.2 — reuse verbatim, do not re-derive. The reviewer's test is the same: does the phrase carry information, or just sound like it does? One addition specific to this book's subject:

| Banned pattern (as filler) | Why it's banned | Legitimate exception |
|---|---|---|
| "enterprise-grade" / "best-in-class SIEM" | Vendor-marketing superlative carried over from Splunk's own materials; asserts nothing checkable | Cut, or name the specific capability and its actual limit (concurrent search cap, ingest tier) |
| "unifies your security operations" | Restates Splunk's own current ES positioning language without adding technical content | Fine only when directly quoting and citing Splunk's marketing as a claim under examination, not adopting it as this book's own voice |

### 1.3 Worked GOOD vs. BAD examples

**Example 1 — introducing risk-based alerting**

> BAD: "Risk-based alerting is a powerful, next-generation capability that transforms your SOC's ability to surface the threats that matter most."

> GOOD: "A traditional correlation search fires one notable event per match. Risk-based alerting instead adds a risk score to a risk object — a user, a host, an IP — on every match, and only generates a notable when that object's accumulated score crosses a threshold. Splunk's own Enterprise Security materials state this can cut notable-event volume by up to 90% relative to per-event notables; treat that figure as a vendor claim, not a measured result from this book's own testing, since no deployment backs it here."

**Example 2 — describing a limitation**

> BAD: "It is important to note that data model acceleration may present certain tradeoffs around storage overhead."

> GOOD: "An accelerated data model builds and maintains its own `tsidx` summary alongside the raw index. That summary consumes additional disk on every indexer holding the accelerated data, on top of the raw data it's summarizing — budget for it as separate storage, not a rounding error, before enabling acceleration on a data model with a long summary range."

**Example 3 — an honesty statement about evidence**

> BAD: "This dashboard design has been proven to reduce analyst triage time significantly."

> GOOD: "This dashboard layout follows the panel-density and drill-down guidance in Splunk's own Enterprise Security documentation (cited in REFERENCES.md). No triage-time measurement backs the specific claim that it reduces analyst effort — that would require a real SOC running it, which this book cannot supply. Treat the layout as a documented starting point, not a benchmarked one."

**Example 4 — a hedge that should be a claim**

> BAD: "It is crucial to note that lookup table maintenance is paramount to detection quality."

> GOOD: "A lookup table that isn't reviewed on a schedule drifts the same way any allowlist drifts — DEH Part 26 §4.2 documents a subsearch-based exclusion list that silently truncated for exactly this reason. A CSV lookup doesn't truncate the same way, but it can still go stale: a decommissioned scanner host that never gets removed from `scanner_allowlist.csv` keeps suppressing a notable that should have started firing again the day that host was reassigned."

---

## 2. Heading Level Conventions

Identical to DEH STYLE-GUIDE.md §2 — same H1/H2/H3/H4 rules, same mandatory unnumbered `## Why this part exists` opener, same sentence-case rule, same "never skip a level" rule. Reuse verbatim.

One addition for this book's subject matter: DEH's rule for a dedicated event-ID walkthrough heading (`### 4624 — An account was successfully logged on`) has a direct analog here for a dedicated Splunk-object walkthrough:

- When a `##`/`###` section is a dedicated walkthrough of one named Splunk command, `.conf` stanza type, or knowledge object, format the heading as the literal object name in code font, an em dash, then a plain-language label: `### tstats — searching accelerated data models without touching raw events` or `### savedsearches.conf — where a correlation search actually lives on disk`. Numeral-first doesn't apply here (there's no numeral); the object name takes that slot instead.
- Never invent a heading that looks like a literal SPL command or `.conf` stanza name but isn't one — if the heading names `tstats`, the section must be about the real `tstats` command, not a paraphrase.

---

## 3. Code Block Conventions

Every fenced code block **must** carry an explicit language tag, same rule as DEH. This book's fence-tag table extends DEH's (§3) with the artifact types a platform-operations title actually shows, most of which DEH never needed:

| Language / artifact | Fence tag | Notes |
|---|---|---|
| Splunk SPL | `` ```spl `` | Identical convention to DEH STYLE-GUIDE.md §3 — one pipe-clause per line for 3+ pipes, `\|` aligned at the start of the continuation line. This book shows SPL almost exclusively inside correlation-search and RBA examples; it does not re-teach `stats`/`transaction` syntax (DEH Part 26 owns that) — a framing sentence pointing to the relevant DEH Part 26 section takes the place of syntax explanation. |
| `.conf` files (`props.conf`, `transforms.conf`, `savedsearches.conf`, `indexes.conf`, `authorize.conf`, `macros.conf`) | `` ```ini `` | Splunk `.conf` files are INI-shaped (`[stanza]` + `key = value`); no renderer has a dedicated `splunk-conf` mode, so `ini` is the closest accurate highlight and is what this book standardizes on. Never invent a `conf` fence tag. |
| Simple XML dashboards | `` ```xml `` | Legacy dashboard format; still real and still deployed at scale in existing environments even as Dashboard Studio becomes the default for new dashboards — see the PRODUCT VERSION NOTE convention in §6.9 before asserting which is "current." |
| Dashboard Studio definitions, KV Store documents, REST API request/response bodies | `` ```json `` | Use `json` only where the excerpt is valid, complete JSON; a truncated/annotated excerpt with ellipses becomes `` ```text `` instead, same rule as DEH §3's raw-log-excerpt guidance. |
| CSV lookup table contents | `` ```text `` | Never `csv` as a fence tag — most renderers have no CSV highlight mode and it adds nothing over `text`; if columns need emphasis, use a Markdown table instead of a fenced CSV excerpt. |
| Splunk REST/CLI invocations (`curl` against the management port, `splunk search` CLI) | `` ```bash `` | Same rule as DEH §3: tag matches the actual shell, never a generic `shell`. |
| Conceptual/illustrative artifacts (invented field names, an index layout that hasn't been deployed) | Same tag as normal, **plus** the DEH `CONCEPTUAL SAMPLE — <one clause>` label line immediately above the fence | Identical convention to DEH §3's final row. Given §9's evidence-class default for this book, expect this label to appear far more often here than in DEH — that's expected, not a sign of unfinished work, as long as every instance is honestly labeled. |

Additional rules, unchanged from DEH §3:

- Every code block that is a real detection/correlation-search query must be preceded by one sentence stating what it targets and followed by one sentence stating its main limitation or a pointer to the callout that covers it.
- Inline code (single backtick) for field names, index/sourcetype names, command names, filenames — never a substitute for a fenced block showing more than one line.
- Comments inside query/config code explain *why*, not restate the syntax.

---

## 4. Splunk Object Notation

DEH STYLE-GUIDE.md §4 locks down Windows Event ID formatting because DEH is full of bare, unintroduced event numbers. This book's equivalent recurring-notation risk is bare, unintroduced Splunk object names — an index, a sourcetype, a data model, a correlation search title, a lookup, a macro — each of which means nothing to a reader who hasn't seen it defined. The rule:

- **First mention of any named index, sourcetype, data model, or correlation search in a chapter:** name it and say what it is in one clause — `the Authentication data model (the CIM data model covering login/logoff events across log sources)`, not bare `Authentication` on first appearance. Every subsequent mention in the same section may drop the clause.
- **Field names, index names, sourcetype names, macro names, and lookup filenames** are always inline-coded: `` `index=windows` ``, `` `sourcetype=WinEventLog:Security` ``, `` `Authentication` `` (data model), `` `lsass_access_allowlist.csv` ``, `` `\`get_asset_criticality(1)\`` `` (macro invocation).
- **Correlation search titles** are given in full, in double quotes, on first mention within a section — `the "SSH Brute Force against DB Server" correlation search` — and may be shortened to a clear paraphrase afterward ("the brute-force search") as long as no other correlation search in the same section could be confused with it.
- **`.conf` stanza names** follow the file-and-stanza convention: `the [SSH_Failed_Auth] stanza in savedsearches.conf`, inline-coded, file name always paired with stanza name on first mention.
- If a Windows Event ID, Sysmon Event ID, or MITRE ATT&CK ID appears in this book's own prose (it will — CIM's Authentication data model maps directly onto events DEH Part 3/8 already cover), follow DEH STYLE-GUIDE.md §4 and §5 exactly rather than inventing a competing convention. Do not restate those rules here; cite them.

---

## 5. MITRE ATT&CK ID Formatting

Identical to DEH STYLE-GUIDE.md §5 — reuse verbatim, do not restate at length. Condensed for reference: `T1558.003 (Kerberoasting)` on first reference in a section, bare `T1558.003` after; uppercase `T`, no space, sub-technique dot-suffix never truncated; a standalone `**MITRE:**` line always spells out ID + name in full regardless of prior mentions; never invent or guess an ID. This book will use MITRE IDs less often than DEH, since it's largely one layer removed from raw technique detection (a correlation search implementing DET-26-01 inherits DET-23-01's MITRE mapping rather than restating it independently) — when it does cite one directly, follow DEH's rule exactly.

---

## 6. Callout Boxes — Exact Templates

This book inherits DEH's eight callout templates unchanged — same blockquote-with-bold-label shape, same rule that a callout is never a heading and never nests inside another, same "if it needs more than ~5 sentences, promote it to prose" rule. **No part may invent its own variant of any of the eight below**, and no part may reshape them for this book's subject — the whole point of a shared callout system across the NESHBOY SOC Professional Library is that a reader who's read DEH recognizes these on sight in this book too.

This book adds **one new, ninth callout — Product Version Note** — because this book's central practical risk (Splunk's editions, features, and default behaviors changing release to release) has no DEH equivalent; DEH's six query languages are comparatively stable specifications, where Splunk Enterprise Security's own feature surface has visibly reorganized (Essentials/Premier editions, a "Detection Studio," an "AI Assistant") within the current release cycle. Adding a ninth templated callout, rather than scattering ad hoc version caveats through prose, keeps the same "grep for it, recognize it on sight" property DEH's eight already have.

### 6.1–6.8: reused verbatim from DEH

**Detection Autopsy, Hunter's Note, Engineering Reality, Blind Spot, False Positive Trap, Detection Test, SOC Management View, What Would Change My Mind** — use the exact templates in DEH STYLE-GUIDE.md §6.1–6.8, unmodified. One usage note per callout, specific to this book:

- **Detection Autopsy:** this book's version dissects a correlation search or RBA model that shipped broken (a throttling misconfiguration, a stale field mapping, an RBA threshold nobody tuned after go-live) rather than a raw detection rule. Given §9's evidence constraints, most of this book's Detection Autopsy boxes describe a documented, publicly-known failure pattern from Splunk practitioner sources (conference talks, official known-issues documentation), explicitly cited, rather than a captured incident from this book's own deployment — say so in the box or in the sentence introducing it.
- **Engineering Reality:** the single most load-bearing callout in this book — licensing tiers, default acceleration behavior, search concurrency limits, and edition feature gates are exactly the "documented/expected vs. what you'll actually hit" gap this callout exists for.
- **Detection Test:** where DEH's version assumes a lab you can run an attack tool in, this book's version more often has to describe what you'd check *in a real Splunk deployment* (a job inspector output, a `tstats` result count, a notable's risk-score contribution) rather than a reproducible attack simulation — reframe the three-line structure (**Setup**/**Action**/**Expected result**) around "what to run against your own instance to confirm this," and say plainly that it hasn't been run against one here.
- **What Would Change My Mind:** given this book's evidence base, expect this callout to appear more often than in DEH, and expect a large share of its answers to be "a real Splunk deployment, instrumented and observed over a real retention window" — that is a legitimate, specific answer, not a cop-out, as long as it says what observation would specifically move the claim.

### 6.9 Product Version Note (new)

Flags a claim about current Splunk product behavior, editions, defaults, or feature availability that is verified as of a specific date against a specific cited source, and that the book expects to go stale — because Splunk ships this fast, not because the author was careless.

```
> **Product Version Note**
> The specific claim, stated as fact. As of <date>, verified against <source, cited in
> REFERENCES.md>. What would make this stale: <the kind of change that would invalidate it —
> a version bump, an edition restructure, a default flipping>.
```

Worked example (built from sources actually checked for this book's outline — see §9.4):

```
> **Product Version Note**
> Splunk Enterprise Security is currently sold in two editions — Essentials and Premier — with
> UEBA, SOAR integration, and "Automated Threat Analysis" gated to Premier. As of 2026-09-15,
> verified against Splunk's own Enterprise Security product page and the Enterprise Security
> app's Splunkbase listing (ES 8.7.0, released 2026-09-02; REFERENCES.md entries [ES-PRODUCT-PAGE]
> and [ES-SPLUNKBASE]). This edition split is recent restructuring, not a stable, long-standing
> feature boundary — confirm which edition a given environment is licensed for before assuming
> any Premier-gated capability (UEBA, SOAR) is available, and re-verify this split before treating
> it as still accurate more than a few release cycles out.
```

Usage rule: **every** claim in this book naming a specific Splunk version number, edition name, default setting, or recently-introduced feature (Detection Studio, AI Assistant, RBA's marketed alert-reduction percentage, CIM/ES version-compatibility ranges) gets one of these, not a passing mention in prose. If a reviewer finds a bare, unflagged version-specific claim, that's a rejection per §11.

---

## 7. Content-Level Tags

Format locked to `**[TAG]**`, identical mechanical rule to DEH §7 — bold, brackets, all caps, start of the paragraph/subsection it governs, never a heading, never doubled on one paragraph.

Per the task brief for this book, **two of DEH's six tags are renamed** for a platform-operations audience; the other four are reused with a platform-scoped definition. The rationale for each rename is recorded here, not left implicit:

| Tag | Renamed from | Use for | Do not use for |
|---|---|---|---|
| `[CONCEPT]` | (unchanged) | Foundational "what and why" for a platform mechanism — what a data model is, what a risk object is, what a `tsidx` summary is — with no assumption the reader acts on it directly. | Anything that gives a specific configuration, threshold, or query — that's a lower tag. |
| `[SOC ANALYST]` | `[ANALYST]` | Triage-facing content scoped to Splunk's own tooling: what a notable event's urgency/severity means, how to work it in Incident Review, what a risk object's contributing events tell you, escalation criteria inside ES specifically. | Content about building or tuning the correlation search/RBA model itself — that's `[DETECTION ENGINEER]`. |
| `[DETECTION ENGINEER]` | (unchanged) | Correlation search and RBA rule logic, field selection against CIM/data models, thresholds, adaptive response actions, and the platform-specific reasoning behind a detection's design — the analytic-to-notable-event translation layer. | Raw SPL syntax teaching — that's DEH Part 26's job; cross-reference it instead of re-deriving it here. |
| `[THREAT HUNTER]` | (unchanged) | Hypothesis-driven exploration specifically through Splunk's own tooling — Pivot against an accelerated data model, an ad hoc `tstats` sweep, a pivot from one notable's risk object into raw search. | A named, deployable correlation search — tag the finished rule `[DETECTION ENGINEER]` and keep the hunting narrative `[THREAT HUNTER]`. |
| `[PLATFORM ENGINEER]` | `[ENGINEERING]` | The Splunk platform itself as infrastructure: index/sourcetype design, bucket lifecycle and retention, licensing and ingest cost, data model acceleration mechanics, search-head/indexer clustering, search performance and workload management — the plumbing every detection and dashboard in this book runs on top of. | Detection or correlation-search logic itself — `[PLATFORM ENGINEER]` is about the platform the rule runs on, not the rule; and generic (non-Splunk-specific) telemetry-pipeline concerns already owned by DEH Parts 5–7 — cross-reference those rather than re-covering parsing/normalization theory from scratch. |
| `[SOC MANAGEMENT]` | (unchanged) | Licensing cost, ES edition tradeoffs (Essentials vs. Premier), staffing for content development vs. platform administration, and risk-acceptance framing for a Splunk-specific decision. | Any content with a specific configuration action embedded — if a management-tagged paragraph starts specifying a `.conf` stanza, split it. |

**Why these two renames and not the others:** `[ANALYST]` becomes `[SOC ANALYST]` because a platform-operations book about Splunk has a second, easily-confused sense of "analyst" in the room — a Splunk content/detection developer is sometimes informally called an "analyst" too — and this book talks about Splunk's own Incident Review workflow often enough that the triage-facing tag needs to be unambiguous on sight. `[ENGINEERING]` becomes `[PLATFORM ENGINEER]` because this book's engineering concern is categorically narrower and more specific than DEH's: DEH's `[ENGINEERING]` spans onboarding, parsing, normalization, retention, and pipeline cost across any SIEM or log pipeline in general; this book's platform-engineering content is *the Splunk platform specifically* — indexers, search heads, buckets, licensing, acceleration — and the renamed tag says so instead of reusing an overloaded generic term. `[DETECTION ENGINEER]`, `[THREAT HUNTER]`, `[CONCEPT]`, and `[SOC MANAGEMENT]` keep their DEH names because their scope translates directly with no ambiguity risk.

Tagging guidance, unchanged from DEH: `[CONCEPT]` is the only tag allowed to open a section before any other tag appears; most `##` sections carry more than one tag across subsections; the pair authors most often confuse here is `[DETECTION ENGINEER]` (correlation-search/RBA logic) vs. `[PLATFORM ENGINEER]` (the platform that logic runs on) — ask "is this about the analytic, or the plumbing under it" and match accordingly.

---

## 8. Table Conventions

Identical to DEH STYLE-GUIDE.md §8 — reuse verbatim: lead-in sentence stating what decision the table supports, title-case noun-phrase headers, left-aligned text columns, fragment cells (not full sentences), em dash for "not applicable," inline code for field/object names, MITRE IDs formatted per §5. One addition: any table cell stating a Splunk version, edition, or release date must be inline-coded as a literal value (`` `10.5` ``, `` `ES 8.7.0` ``) and the table's lead-in sentence or a footnote must carry (or point to) a Product Version Note per §6.9 — a table is not an exemption from that requirement any more than it's an exemption from MITRE formatting.

---

## 9. Figures, Diagrams, and Screenshots: Evidence Classification

### 9.1 The four evidence classes — reused, unchanged in definition

This book uses the same four evidence-class tags DEH defines in its STYLE-GUIDE.md §9.1: `CONTROLLED LAB EXAMPLE`, `REAL LAB EXAMPLE`, `OFFICIAL REFERENCE`, `CONCEPTUAL`. Definitions are unchanged — reuse DEH's table rather than restating it. What changes for this book is the *distribution* across those four classes, and that distribution is a deliberate, disclosed policy, not an accident of what evidence happened to be available.

### 9.2 This book's evidence-class default, and why

**No Splunk deployment exists in this book's author's environment.** The author's home lab (Proxmox-based, documented separately) runs a honeynet, a vulnerability-scanner platform, and general infrastructure — real evidence that DEH itself draws on directly (its Part 26, for instance, cites a real `lastb` capture and a real honeynet `http_events` table). None of that lab runs Splunk. That means, as a hard structural fact rather than a stylistic choice:

- **`CONTROLLED LAB EXAMPLE` and `REAL LAB EXAMPLE` are presumptively unavailable to this book.** A unit must not label any figure with either tag unless a real Splunk instance was actually stood up and actually observed to produce that specific artifact. Until and unless that changes, expect **zero** uses of these two tags across the entire book.
- **This book's figures are, and are expected to remain, almost exclusively `OFFICIAL REFERENCE` and `CONCEPTUAL`.** `OFFICIAL REFERENCE` covers a screenshot, table, or diagram reproduced or closely adapted from Splunk's own public documentation, product pages, or Splunkbase listings, always cited in `REFERENCES.md`. `CONCEPTUAL` covers this book's own illustrative diagrams — an architecture sketch of an index/bucket lifecycle, a sequence diagram of a correlation-search-to-notable-event flow, a mocked-up dashboard layout — with no claim that it was captured from a running system.
- **If this ever changes** — if a real Splunk instance gets stood up in the lab later — that is a stated, dated change to this policy (recorded in this file, the same way DEH treats a documented style-guide override), not a silent upgrade of old `CONCEPTUAL` figures to `CONTROLLED LAB EXAMPLE` after the fact. A figure's evidence-class tag describes what backs it *at the time it was written*, exactly as DEH §9.1 already requires; this book just starts from a narrower available set than DEH did.
- **Never soften this by mislabeling.** A screenshot copied from Splunk's own documentation is `OFFICIAL REFERENCE`, not `CONTROLLED LAB EXAMPLE` "because it shows what the real product looks like" — it shows what Splunk's own docs chose to show, possibly from a different version than the one a given reader runs. A hand-drawn mockup of a dashboard is `CONCEPTUAL`, not `OFFICIAL REFERENCE` "because it's based on real Splunk panel types." When genuinely unsure which of the two legitimate classes applies, `CONCEPTUAL` is the safer default — it is never wrong to under-claim evidence, only to over-claim it.

### 9.3 Caption format — unchanged from DEH

Reuse DEH STYLE-GUIDE.md §9.2's caption format exactly: `**Figure N.M — [title].** *[Evidence class].* One to two sentences on what it shows / illustrates. Citation if `OFFICIAL REFERENCE`.` Given this book's near-total reliance on those two classes, expect most captions to end in a `REFERENCES.md` citation (`OFFICIAL REFERENCE`) or an explicit "illustrates ... not a capture of any real system" clause (`CONCEPTUAL`) — both are required content, not boilerplate.

Worked examples:

```
**Figure 9.1 — CIM Authentication data model, top-level fields.** *OFFICIAL REFERENCE.* Reproduced
from the field list published in Splunk's Common Information Model Add-on documentation for the
Authentication data model. See REFERENCES.md entry [CIM-ADDON-DOCS]. Field set shown reflects
CIM 8.x as listed on the add-on's Splunkbase page as of 2026-09-15; verify against the CIM version
actually installed before treating this as current.
```

```
**Figure 14.2 — Correlation search to notable event, sequence.** *CONCEPTUAL.* Illustrates the
expected sequence from a scheduled correlation search's SPL execution through notable-event
creation and adaptive-response dispatch. This is a sequence diagram of documented expected
behavior, not a capture from a live Splunk job inspector or a real Enterprise Security instance —
none exists in this book's evidence base (see STYLE-GUIDE.md §9.2).
```

### 9.4 Citation and sourcing requirement (new for this book)

Because this book leans on `OFFICIAL REFERENCE` more heavily than any other title in the library is likely to, its `REFERENCES.md` (to be created alongside the first authored part) carries a heavier load than DEH's and must be treated accordingly:

- Every `OFFICIAL REFERENCE` figure, every `Product Version Note` (§6.9), and every prose claim naming a specific Splunk version, edition, default, or documented percentage/limit must resolve to a `REFERENCES.md` entry with a retrieval date. Splunk's own documentation domain (`docs.splunk.com`) is not reliably fetchable by this book's own research tooling as of this outline's authoring — Splunkbase app listings, `www.splunk.com` product pages, Splunk's public GitHub repositories (e.g., `splunk/security_content`), and Wikipedia's Splunk entry were used instead where `docs.splunk.com` could not be reached, and each `REFERENCES.md` entry must record which of those it actually came from, not imply it came from the canonical docs site if it didn't.
- A claim that cannot be traced to a checkable public source gets marked `CONCEPTUAL` (if illustrative) or dropped, never asserted as fact on the strength of general familiarity with the product. This is the same discipline DEH's §5 applies to MITRE IDs ("never invent or guess an ID") extended to this book's platform claims generally.

### 9.5 Pending placeholder format — unchanged from DEH

Reuse DEH STYLE-GUIDE.md §9.3's `[FIGURE PENDING — target evidence class: ...]` blockquote format exactly. Given §9.2, a pending figure in this book should almost never target `CONTROLLED LAB EXAMPLE` or `REAL LAB EXAMPLE` — if a unit finds itself wanting one, that's a signal to flag the gap explicitly (a `What Would Change My Mind` box naming "a real Splunk lab" as the specific missing evidence) rather than leaving a placeholder that implies that evidence is merely pending capture on the current trajectory.

---

## 10. Diagram Rendering Requirement

Identical to DEH STYLE-GUIDE.md §10 — every `` ```mermaid `` block must be rendered to a static image and committed alongside the source, referenced via the §9.3 caption format, almost always tagged `CONCEPTUAL` per §9.2 for this book specifically. The Mermaid source stays in the file as editable source of truth. A unit is not review-complete with an unpaired `mermaid` fence.

---

## 11. Review Checklist (for the independent reviewer, per unit)

Adapted from DEH STYLE-GUIDE.md §11 for this book's tags, callout, and evidence-default additions:

1. **Voice:** any banned filler present, including the Splunk-marketing-specific additions in §1.2? Any sentence that fails the "what does this tell me to configure/check/expect" test?
2. **Non-duplication:** does this unit re-teach SPL syntax DEH Part 26 already owns, or Sigma/translation-strategy content DEH Part 23 already owns, instead of citing it? (§0)
3. **Headings:** correct level nesting, `## Why this part exists` present, Splunk-object walkthrough headings follow §2's code-font-plus-em-dash convention where applicable?
4. **Code blocks:** every fence tagged per §3's extended table (including `.ini` for `.conf` files, never a bare `conf` tag), framing sentences present for real queries?
5. **Splunk object notation:** first-use clause present for named indexes/sourcetypes/data models/correlation searches per §4; Windows/Sysmon Event IDs and MITRE IDs, where they appear, follow DEH §4/§5 exactly?
6. **Callouts:** correct label and structure for all eight inherited callouts plus Product Version Note; every version/edition/default/feature-availability claim carries a Product Version Note (§6.9) — this is a hard gate, not a should; density not excessive?
7. **Tags:** every `##`/`###` subsection carries at least one of the six tags in §7 (using the renamed `[SOC ANALYST]`/`[PLATFORM ENGINEER]`, not the DEH originals), no paragraph carries two?
8. **Tables:** lead-in sentence present, no blank cells, version/edition cells inline-coded and backed by a Product Version Note per §8?
9. **Figures:** every figure tagged with one of the four evidence classes; **zero** `CONTROLLED LAB EXAMPLE` or `REAL LAB EXAMPLE` tags present anywhere in the unit (§9.2) — a reviewer finding either is a hard rejection unless a real Splunk deployment has been disclosed and logged as a policy change to this file; every `OFFICIAL REFERENCE` figure and claim resolves to a dated `REFERENCES.md` entry (§9.4); every Mermaid block has a paired rendered image?
10. **Depth check:** does this unit's technical depth match sibling units covering comparable scope, and does it actually add platform-operations content DEH doesn't already cover rather than restating DEH's SPL/Sigma material from a different angle?

Match this guide over inventing new precedent. Any deviation a reviewer approves is recorded as a documented change to this file.
