# Wiki Schema — Personal Knowledge Base

This file defines how the LLM (Claude Code) maintains this wiki. Read it at the start of every session before doing anything else.

---

## What this is

A personal knowledge base covering university studies, research, and general learning. Sources come in many forms — articles, PDFs, books, lecture slides, transcripts, data files, images. The LLM maintains the wiki; the user curates sources and directs analysis.

**The rule:** raw sources are immutable (never edit them). The wiki is LLM-owned (the user reads it; the LLM writes it). The user decides what to ingest and what questions to ask.

---

## Directory layout

```
raw/                    ← source documents, never modified by the LLM
  articles/             ← web articles (clipped as markdown)
  pdfs/                 ← papers, books, reports as PDF
  notes/                ← user's own handwritten or typed notes
  transcripts/          ← podcast, lecture, video transcripts
  images/               ← standalone images, diagrams, figures
  data/                 ← CSV, Excel, JSON, datasets
  presentations/        ← PowerPoint, PDF slides, lecture decks
  books/                ← full books (PDF or text)
  videos/               ← notes/transcripts from video content

wiki/                   ← LLM-generated markdown, the knowledge base
  index.md              ← master catalog of all wiki pages (update on every ingest)
  log.md                ← append-only activity log
  overview.md           ← evolving high-level synthesis of everything in the wiki
  topics/               ← concept and subject pages (e.g., "Machine Learning.md")
  entities/             ← people, organizations, tools, places (e.g., "Geoffrey Hinton.md")
  sources/              ← one summary page per ingested source
  analyses/             ← comparisons, research summaries, deep dives, tables
  outputs/              ← generated deliverables: slide decks, resumes, reports

CLAUDE.md               ← this file
```

---

## Page formats

### Source page (`wiki/sources/`)
One page per ingested source. Filename mirrors the source file (e.g., `raw/articles/attention-is-all-you-need.md` → `wiki/sources/attention-is-all-you-need.md`).

```markdown
---
title: <title>
type: source
source_file: raw/<subdir>/<filename>
source_type: article | pdf | book | transcript | notes | data | presentation | image | video
date_ingested: YYYY-MM-DD
subject: <primary subject/course>
tags: [tag1, tag2]
---

## Summary
<2–5 paragraph summary of the source's key content>

## Key Points
- <bullet list of the most important claims, findings, or ideas>

## Concepts Introduced
<links to topic pages this source touches: [[Topic Name]], ...>

## People / Organizations
<links to entity pages: [[Person Name]], ...>

## Data & Evidence
<notable data points, statistics, findings — include numbers>

## Quotes
> <notable direct quotes worth preserving>

## Questions Raised
<things this source left unclear or that warrant further investigation>

## Connections
<how this source relates to other sources or wiki pages>
```

### Topic page (`wiki/topics/`)
One page per concept, subject, or recurring theme.

```markdown
---
title: <concept name>
type: topic
subject: <field/course if applicable>
sources: [source1.md, source2.md]
tags: [tag1, tag2]
---

## Definition
<clear definition or explanation>

## Key Ideas
<the most important things to understand about this topic>

## Subtopics
- [[Subtopic 1]]
- [[Subtopic 2]]

## Evidence & Data
<key data points or findings from sources>

## Debates & Contradictions
<where sources disagree, or where the field is unsettled>

## Applications
<practical uses, examples, real-world relevance>

## Sources
<links to all source pages that cover this topic>

## Related Topics
<links to related topic pages>
```

### Entity page (`wiki/entities/`)
One page per person, organization, tool, or place that appears meaningfully across multiple sources.

```markdown
---
title: <name>
type: entity
entity_type: person | organization | tool | place
tags: [tag1, tag2]
---

## Overview
<who/what this is in 1–3 sentences>

## Key Contributions / Role
<what they're known for, what they did, why they matter>

## Appearances
<links to source and topic pages where this entity appears>

## Notes
<anything worth tracking that doesn't fit above>
```

### Analysis page (`wiki/analyses/`)
Generated on demand for comparisons, research summaries, deep dives, tables.

```markdown
---
title: <descriptive title>
type: analysis
analysis_type: comparison | summary | deep-dive | table | report | research
date: YYYY-MM-DD
sources_used: [source1.md, source2.md]
tags: [tag1, tag2]
---

<freeform content — structure to fit the analysis type>
```

### Output page (`wiki/outputs/`)
Generated deliverables: slide decks (Marp), study guides, resumes, reports.

```markdown
---
title: <title>
type: output
output_type: slides | study-guide | resume | report | cheatsheet | essay-outline
date: YYYY-MM-DD
based_on: [source or analysis pages used]
---

<deliverable content>
```

---

## Frontmatter conventions

- Always include frontmatter on every wiki page
- `date_ingested` / `date` → always ISO format: `YYYY-MM-DD`
- `tags` → lowercase, hyphenated (e.g., `machine-learning`, `university`, `economics`)
- `subject` → use consistent names matching university courses or domains (e.g., `Statistics`, `Economics`, `Computer Science`, `Personal`)
- `sources` on topic/entity pages → list of source page filenames (not raw filenames)

---

## Workflows

### Ingest a new source

When the user says "ingest [file]" or drops a new source:

1. **Read** the source file carefully (for images, view them; for data files, summarize the structure and key stats)
2. **Discuss** with the user — ask what to emphasize, what course/subject it belongs to, any specific focus
3. **Write** a source page in `wiki/sources/`
4. **Update or create** topic pages in `wiki/topics/` for every major concept the source covers
5. **Update or create** entity pages in `wiki/entities/` for significant people/orgs/tools
6. **Update** `wiki/overview.md` if the source meaningfully shifts the big picture
7. **Update** `wiki/index.md` — add the new source page and any new topic/entity pages
8. **Append** to `wiki/log.md` with the format: `## [YYYY-MM-DD] ingest | <Source Title>`

A single ingest may touch 5–20 wiki pages. That's normal and expected.

### Answer a query

When the user asks a question:

1. Read `wiki/index.md` to identify relevant pages
2. Read those pages
3. Synthesize an answer with citations (link to wiki pages, not raw sources)
4. Ask the user: "Should I save this as an analysis page?" — if yes, write it to `wiki/analyses/`
5. If the answer reveals a gap (missing topic page, orphaned concept), flag it

### Generate an output

When the user asks for slides, a study guide, a report, a cheatsheet, etc.:

1. Identify relevant wiki pages via the index
2. Read them
3. Generate the deliverable in the appropriate format (see below)
4. Save to `wiki/outputs/` with correct frontmatter
5. Append to log: `## [YYYY-MM-DD] output | <Output Title>`

**Slide decks** → use Marp format:
```markdown
---
marp: true
theme: default
---

# Title

---

# Slide 2
...
```

**Study guides / cheatsheets** → dense markdown with headers, bullet points, tables

**Essay outlines** → hierarchical outline with thesis, main arguments, evidence pointers

### Lint the wiki

When the user says "lint" or "health check":

1. Scan all wiki pages via the index
2. Report:
   - Contradictions between pages
   - Stale claims superseded by newer sources
   - Orphan pages (no inbound links)
   - Concepts mentioned but lacking their own topic page
   - Missing cross-references that should exist
   - Data gaps worth filling with a web search
3. Suggest 3–5 new questions or sources worth investigating
4. Fix obvious issues (broken links, missing index entries) without asking
5. Append to log: `## [YYYY-MM-DD] lint | <brief summary>`

---

## Index format (`wiki/index.md`)

Organized by category. Each entry: `- [[Page Title]] — one-line description`

```markdown
# Wiki Index

_Last updated: YYYY-MM-DD — N sources, N topics, N entities, N analyses_

## Sources
- [[source-page]] — one-line description (source type, subject)

## Topics
- [[topic-page]] — one-line description

## Entities
- [[entity-page]] — one-line description (person/org/tool)

## Analyses
- [[analysis-page]] — one-line description

## Outputs
- [[output-page]] — one-line description
```

---

## Log format (`wiki/log.md`)

Append-only. Never edit past entries. Newest entries at the top.

```markdown
# Activity Log

## [YYYY-MM-DD] ingest | Source Title
- Source: raw/subdir/filename
- Pages created/updated: [[page1]], [[page2]], ...
- Key takeaways: <1–2 sentences>

## [YYYY-MM-DD] query | Query Summary
- Question: <what the user asked>
- Saved as analysis: yes/no — [[analysis-page]] if yes

## [YYYY-MM-DD] output | Output Title
- Type: slides | study-guide | etc.
- Saved: [[output-page]]

## [YYYY-MM-DD] lint | Health Check
- Issues found: N
- Fixed: <what was fixed>
- Suggestions: <top suggestions>
```

---

## General rules

- **Never edit raw sources.** Read them, never write to them.
- **Always update the index** after any ingest. A page that isn't in the index effectively doesn't exist.
- **Cross-reference aggressively.** Every time a topic or entity is mentioned in a page, link it with `[[Page Name]]`. This is what makes the wiki navigable.
- **Flag contradictions explicitly.** If a new source contradicts something already in the wiki, note it on the relevant topic page under "Debates & Contradictions" rather than silently overwriting.
- **Prefer updating over creating.** Before creating a new topic page, check if the concept already has a page (maybe under a slightly different name).
- **Keep the overview current.** `wiki/overview.md` should always reflect the current state of the whole wiki — update it when a new source meaningfully changes the picture.
- **Ask before assuming subject/course.** When ingesting, if the subject isn't clear, ask the user rather than guessing.
- **Save good answers.** When an analysis or synthesis is worth keeping, always offer to save it. Chat history disappears; the wiki doesn't.
- **Suggest next steps.** After every ingest, suggest 1–3 related sources or questions worth exploring next.
