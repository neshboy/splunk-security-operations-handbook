# References

Citations for every `OFFICIAL REFERENCE` figure, every Product Version Note, and every prose claim
naming a specific Splunk version, edition, default, or documented percentage/limit, per
`STYLE-GUIDE.md` §9.4. Grouped by source. Retrieval dates are the date this book's research tooling
actually fetched the page, not a publication date claimed by the source itself unless the two are
stated as identical.

Per `STYLE-GUIDE.md` §9.4: `docs.splunk.com` was not reliably fetchable by this book's own research
tooling as of the dates below (fetch attempts returned HTTP 403/404). Entries below are drawn from
`www.splunk.com` product/pricing pages and Splunkbase listings instead, per that section's disclosed
fallback policy — each entry records which of those it actually came from.

## External / Third-Party Sources

### Splunk, Inc. — Product and Pricing Pages (www.splunk.com)

- **[SPLUNK-PRICING-OVERVIEW]** — Splunk product pricing overview page (`www.splunk.com/en_us/products/pricing.html`), describing activity-based, ingest, and workload pricing options across Splunk's portfolio and naming Enterprise Security's Essentials/Premier edition split. Retrieved 2026-09-15. Cited in: Part 4.
- **[SPLUNK-PRICING-FAQ]** — Splunk pricing FAQ page (`www.splunk.com/en_us/products/pricing/faqs.html`), naming Entity, Workload, and Ingest pricing as the three named models and covering general licensing-term/renewal policy. Retrieved 2026-09-15. Cited in: Part 4; Part 11 (Product Version Note on which pricing model an environment runs under).
- **[SPLUNK-CLOUD-PLATFORM-PRICING]** — Splunk Cloud Platform pricing page (`www.splunk.com/en_us/products/pricing/splunk-cloud-platform.html`), confirming Ingest and Workload as the two pricing options offered specifically for Splunk Cloud Platform. Retrieved 2026-09-15. Cited in: Part 4.
- **[SPLUNK-WORKLOAD-PRICING]** — Splunk workload-pricing page (`www.splunk.com/en_us/products/pricing/workload-pricing.html`), defining Splunk Virtual Compute (SVC) as a unit of cloud compute, memory, and I/O resources, and stating SVC consumption is driven primarily by search quantity/complexity and daily indexing volume. Retrieved 2026-09-15. Cited in: Part 4.
- **[SPLUNK-ES-PRODUCT-PAGE]** — Splunk Enterprise Security product page (`www.splunk.com/en_us/products/enterprise-security.html`), naming the Essentials/Premier edition split and confirming UEBA, SOAR, and Automated Threat Analysis as Premier-gated, with Detection Studio and AI Assistant for Security available in both editions; also carries a Risk-Based Alerting feature callout stating "Enhance your SOC's productivity with high-fidelity threat detection, reducing your alert volumes by up to 90%," with RBA not listed among the page's Premier-gated capabilities. Retrieved 2026-09-15. Cited in: Part 1 §2 (Product Version Note); Part 4 (in passing, per §1); Part 5 §5 (Product Version Note); Part 8 §4 (Product Version Note); Part 13 §2, §6 (Product Version Notes — primary citation); also cited in Part 6 §7 (Product Version Note on Pivot/data models sitting outside the edition gate); Part 10 §5 (Product Version Note); Part 15 §1 (Product Version Note — RBA alert-volume-reduction claim and edition placement); Part 19 §6, §7 (Product Version Notes — AI Assistant, and the page's silence on Investigation Workbench/Incident Review/Mission Control as evidence of marketing-surface naming churn); Part 17 §2 (Product Version Note — Asset and Identity framework's Essentials-tier licensing); Part 16 §3 (Product Version Note — AI Assistant for Security included in both editions).
- **[SPLUNK-CLOUD-PRODUCT-PAGE]** — Splunk Cloud Platform product page (`www.splunk.com/en_us/products/splunk-cloud-platform.html`), describing Splunk Cloud Platform as a managed service ("Splunk experts manage your IT backend... while our platform scales to your analytics needs") without enumerating specific internal architecture, elastic-scaling mechanics, or Admin Config Service boundaries. Retrieved 2026-09-15. Cited in: Part 2 §7 (Product Version Note).

### Splunk, Inc. — Public GitHub Repositories (github.com/splunk)

- **[SECURITY-CONTENT-REPO]** — `splunk/security_content` public GitHub repository, `macros/` directory, specifically `security_content_ctime.yml` and `security_content_summariesonly.yml`. Splunk Threat Research Team's own published, version-controlled macro definitions backing Enterprise Security Content Update (ESCU) detections; used as a real, verified worked example of a plain text-substitution macro and of macro-to-macro nesting. Also used, from the same repository: `macros/risk_index.yml` (default macro definition `index=risk`); `detections/endpoint/access_lsass_memory_for_dump_creation.yml` (`finding.entity` block: field `dest`, type `system`, score `50`); `detections/application/okta_risk_threshold_exceeded.yml` (`risk_object_type=user`, `tstats ... FROM datamodel=Risk.All_Risk` threshold pattern, `mitre_technique_id_count > 5`); `detections/endpoint/windows_post_exploitation_risk_behavior.yml` (`risk_object_type=other`, `source_count >= 4` threshold pattern); and `playbooks/risk_notable_*.py`/`.yml` (confirming "Risk Notable" as Splunk's own terminology for a notable generated via the risk framework). Retrieved 2026-09-15. Cited in: Part 9 §3, §4, §6; Part 15 §§2–5.
- **[CONTENTCTL-REPO]** — `splunk/contentctl` public GitHub repository, Splunk's own build tooling that compiles ESCU's YAML-authored detections into the `savedsearches.conf` stanzas Enterprise Security loads. `contentctl/output/templates/savedsearches_detections.j2` shows the literal rendered stanza keys for a risk-annotating, notable-generating detection (`action.risk`, `action.risk.param._risk`, `action.risk.param._risk_message`, `action.notable`, `action.notable.param._entities`, `action.correlationsearch.*`) and confirms an RBA-driven notable's `action.notable.param.rule_title` is prefixed `RBA: ` when the detection's type is Correlation with risk-based alerting enabled; `contentctl/objects/risk_analysis_action.py` confirms the JSON shape of `action.risk.param._risk` (a list of `{risk_object_field, risk_object_type, risk_score}` and/or `{threat_object_field, threat_object_type}` objects). Not verified by this source: whether Splunk Web's own adaptive-response-action picker displays these same parameter names under an unchanged label — this is ESCU's content-build pipeline, not a capture of the Splunk Web UI. Retrieved 2026-09-15. Cited in: Part 15 §3.

### Splunkbase (splunkbase.splunk.com)

- **[SPLUNKBASE-OKTA-TA]** — Splunk Add-on for Okta Identity Cloud, Splunkbase listing (`splunkbase.splunk.com/app/6553`), version `5.1.0`, last updated 2026-07-30, listing compatibility with Splunk Enterprise/Cloud Platform `9.3` and higher. Used as a dated data point establishing a version floor for currently-shipping Splunk platform compatibility as of this book's writing. Retrieved 2026-09-15. Cited in: Part 4.
- **[SPLUNKBASE-CIM-ADDON]** — Splunk Common Information Model (CIM) Add-on, Splunkbase listing (`splunkbase.splunk.com/app/1621`), version `8.7.0`, released 2026-09-02, listing compatibility with Splunk Enterprise/Splunk Cloud Platform `9.4`–`10.5`. Listing does not enumerate individual data models or child datasets; that gap is disclosed in-text rather than filled from general familiarity. Retrieved 2026-09-15. Cited in: Part 2 §1 (Product Version Note); Part 5 §1 (Product Version Note); Part 6 §2.3, §7 (Product Version Note; What Would Change My Mind); Part 7 §4, §5 (Product Version Notes); Part 10 §5 (Product Version Note).
- **[ES-SPLUNKBASE]** — Splunk Enterprise Security app, Splunkbase listing (`splunkbase.splunk.com/app/263`), default version `8.7.0`, released 2026-09-02, listing Splunk-platform compatibility `10.2`–`10.5` and CIM-version compatibility `8.x`. Retrieved 2026-09-15. Cited in: Part 2 §1 (Product Version Note); Part 3 §7 (Product Version Note); Part 14 §3 (Product Version Note); Part 10 §5 (Product Version Note); Part 19 §6, §7 (Product Version Notes).
- **[ESCU-SPLUNKBASE]** — Splunk ES Content Update (ESCU), Splunkbase listing (`splunkbase.splunk.com/app/3449`), version `6.6.0`, released 2026-09-09, described on the listing as delivering "pre-packaged Security Content" that can generate notable and risk events against Enterprise Security once installed; underlying detections also published on Splunk's public `security_content` GitHub repository per the listing's own stated source-code link. Retrieved 2026-09-15. Cited in: Part 14 §5 (Product Version Note).

## Internal — Project Governing Documents

### STYLE-GUIDE.md

Cited (by section) in: Part 4 (§9.2 evidence-class default, §9.4 sourcing constraint).

### BOOK-INDEX.md

Cited in: Part 4 (Part Table scope and neighboring-part boundaries).

## Cross-Book — Detection Engineering Handbook (DEH), V2

- DEH `release-v2\chapters\part26-splunk-spl.md` §1.2 — search-time vs. index-time field split, cited for what counts as "indexed volume" under ingest-based licensing (Part 4) and extended to sourcetype/index assignment being fixed at index time (Part 3 §3.1). Cited in: Part 3, Part 4.
- DEH `release-v2\chapters\part26-splunk-spl.md` introductory scope note and §1.3's `tstats` table row — states that DEH Part 26 "does not cover Splunk administration, index/bucket lifecycle management, or `tstats`/data-model acceleration in depth," and names `tstats` as "not covered in depth here," cited as the non-duplication boundary this book's Part 3 and Part 10 fill. Cited in: Part 3, Part 10.
- DEH `release-v2\chapters\part26-splunk-spl.md` §1.2 and §5 — search-time vs. index-time field split and the DET-26-01 LSASS-access SPL rule, cited for why DET-26-01's fields (`SourceImage`, `TargetImage`, `GrantedAccess`) are search-time fields and therefore not directly usable by `tstats` without deliberate index-time promotion into an accelerated summary. Cited in: Part 10 §2.1 (`tstats` walkthrough).
