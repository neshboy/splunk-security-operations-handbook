# Splunk Security Operations

**Platform Architecture, the Common Information Model, and Enterprise Security — Running Splunk Itself as a Security Platform, Once You Already Know the Query**

📄 **[Download the full PDF](./Splunk_Security_Operations.pdf)** — 216 pages, ~86,000 words across 20 parts.

A title in the **NESHBOY SOC Professional Library**, alongside [The Detection Engineering Handbook V2](https://github.com/neshboy/detection-engineering-handbook) and [The SOC Manager's Operating Handbook](https://github.com/neshboy/soc-manager-handbook). DEH's own Part 26 (Splunk SPL) closes by naming a gap and declining to fill it: it "does not cover Splunk administration, index/bucket lifecycle management, or `tstats`/data-model acceleration in depth... a full accelerated-search treatment belongs in a dedicated performance-engineering appendix, not here." *Splunk Security Operations* is that appendix, expanded into a full platform-operations handbook.

This book assumes the reader already knows how to write a Splunk search — DEH Part 23 (query-language strategy) and DEH Part 26 (SPL syntax) are prerequisite reading it deliberately does not re-teach. Its subject is everything else a detection engineer, SIEM administrator, SOC analyst, threat hunter, or SOC manager needs to run Splunk itself as a security platform: deployment architecture, index/sourcetype design and the bucket lifecycle, licensing and ingest economics, the Common Information Model and data models, lookups and macros as maintained detection infrastructure, data-model acceleration and search performance at scale, and — at flagship depth across five parts (13–17) — Splunk Enterprise Security's full correlation-search-to-notable-event pipeline: architecture and editions, correlation-search anatomy and lifecycle, risk-based alerting, Incident Review triage, and asset/identity correlation, treated as five genuinely different audiences rather than one compressed "Enterprise Security" chapter.

## Reading the book

- **[Splunk_Security_Operations.pdf](./Splunk_Security_Operations.pdf)** — the assembled, print-ready book. Start here.
- **[BOOK-INDEX.md](./BOOK-INDEX.md)** — the full 20-part table with per-part scope and recurring callouts, plus a "Key structural decisions and provenance" section recording exactly why the book is organized the way it is (why Enterprise Security gets five parts instead of one, why acceleration/summary-indexing is kept separate from general performance tuning, etc.).
- **[STYLE-GUIDE.md](./STYLE-GUIDE.md)** — the voice, formatting, and figure-evidence-classification contract every part follows: six content tags (`[CONCEPT]`, `[SOC ANALYST]`, `[DETECTION ENGINEER]`, `[THREAT HUNTER]`, `[PLATFORM ENGINEER]`, `[SOC MANAGEMENT]`) and nine recurring callouts — the eight inherited from the Detection Engineering Handbook V2 plus one this book adds, **Product Version Note** — adapted for series-wide consistency.
- **[REFERENCES.md](./REFERENCES.md)** — every citation backing an `OFFICIAL REFERENCE` figure or Product Version Note, with retrieval dates.

## What's real vs. documented

**No Splunk deployment exists in this book's evidence base.** Unlike its sibling volumes — which draw on a real home-lab Proxmox environment, a real honeynet, and a real vulnerability-scanner platform — none of that lab runs Splunk, and this book says so structurally rather than as a footnote. `STYLE-GUIDE.md` §9 makes it a hard rule: every figure and platform-behavior claim in this book is tagged either `OFFICIAL REFERENCE` (reproduced or closely adapted from Splunk's own public documentation, product pages, or Splunkbase listings, cited in `REFERENCES.md`) or `CONCEPTUAL` (this book's own illustrative diagram or worked example, explicitly labeled as not a capture of a running system). The two evidence classes that would imply a real deployment — `CONTROLLED LAB EXAMPLE` and `REAL LAB EXAMPLE` — are used **zero** times across all 20 parts, by disclosed policy, not by omission. Part 20 closes the book with an honest inventory of exactly which claims that constraint leaves unverified and what standing up a real Splunk lab would need to look like to start closing the gap. If a real Splunk instance is ever stood up in the lab, that will be a dated, disclosed change to this policy — not a silent upgrade of old `CONCEPTUAL` figures after the fact.

## How it was built

- `build/build_book.js` — parses `BOOK-INDEX.md`'s Part Table, assembles all 20 chapters into one HTML document, and prints it to PDF via headless Chrome. (`BOOK-INDEX.md`'s Appendix Table names two appendices, A1 and A2, that are not yet authored — `appendices/` is empty as of this release — so the build deliberately skips that table rather than emitting "MISSING FILE" placeholders for content that simply hasn't been written yet; it will pick the appendices up automatically once they exist.)
- `build/render_mermaid.py` — auto-detects every Mermaid diagram source in `chapters/*.md` (22 across the 20 parts), renders it to SVG via `@mermaid-js/mermaid-cli`, and inserts the image tag immediately after its source fence.
- `build/add_watermark.py` — applies the diagonal `neshboy` watermark to every page.

## Rebuilding it yourself

```
cd build
npm install
python render_mermaid.py
node build_book.js
"C:\Program Files\Google\Chrome\Application\chrome.exe" --headless=new --disable-gpu --no-sandbox --no-pdf-header-footer ^
  --print-to-pdf="..\_build\Splunk_Security_Operations.pdf" "..\_build\book.html"
python add_watermark.py
```

## Repository layout

- `chapters/` — the 20 parts, Markdown source of record, front matter carrying `author`/`reviewer`/`status`/`last_validated`/`depends_on`.
- `appendices/` — reserved for A1 (Splunk object/naming quick reference) and A2 (DEH cross-reference map); not yet authored.
- `assets/diagrams/` — rendered Mermaid SVGs plus their `.mmd` source.
- `build/` — the build/render/watermark tooling above.
- `BOOK-INDEX.md`, `STYLE-GUIDE.md`, `REFERENCES.md` — cross-cutting project documentation.
