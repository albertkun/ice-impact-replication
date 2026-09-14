# Cost of Fear Replication Guide

A step-by-step guide for organizations who want to measure foot traffic changes and lost sales
around immigration enforcement actions in their own area.

Live site: https://albertkun.github.io/ice-impact-replication

## The one-pagers

Two versions of the engagement one-pager live in `docs/`. Both describe the same three options; they
differ in what they say about money.

| File | Rates shown | Use it for |
|---|---|---|
| `Cost_of_Fear_Replication_Options.docx` | Estimated cost per option, $200/hr | Internal scoping and budgeting |
| `Methodological_TA_Cost_of_Fear.docx` | None. Estimated hours only | Sending to partner jurisdictions |

The partner version lists the shared research inbox rather than a personal address, and carries a QR
code to the report page at
https://latino.ucla.edu/research/immigration-enforcement-small-businesses-los-angeles/

Neither file is part of the built site, so the priced version cannot be downloaded from the web by
accident. Move a file to `public/` only if it should be publicly downloadable.

The QR was generated from the report URL directly. To swap in a branded, click-tracked link instead,
make one at https://www.ucla.edu/uclain/ and regenerate.

## Editing the guide

All of the text lives in one file:

```
src/pages/index.md
```

It is plain Markdown. Edit it like any other document and the site updates when you push.

A few conventions the page design relies on:

| What you write | How it looks |
|---|---|
| `## Heading` | A new section, with a rule above it |
| `### Heading` | A step heading |
| `- [ ] Do the thing` | A checkbox readers can tick off |
| `> **Note:** ...` | An orange callout box |
| `**Decide:** ...` at the start of a line | A line with a blue rule beside it |
| A normal Markdown table | A styled table |

Colors, fonts, and spacing are in `src/styles/guide.css`. The page shell, the progress bar, and the
save-your-progress script are in `src/layouts/Guide.astro`.

## Running it locally

This site uses Astro 5, which needs Node 22. The version is pinned in `.nvmrc`, so:

```bash
nvm use          # switches to Node 22
npm install
npm run dev      # preview at http://localhost:4321/ice-impact-replication
npm run build    # build the site into dist/
```

If `nvm use` says the version is missing, run `nvm install 22` first.

## Publishing

Pushing to `main` builds and deploys the site automatically. One setup step is needed the first
time: in the repository settings, go to **Pages** and set **Source** to **GitHub Actions**.

If the repository is ever renamed, update `base` in `astro.config.mjs` to match the new name.

## How readers use it

- **Checkmarks are saved** in the reader's own browser. Nothing is sent anywhere, and each person
  sees only their own progress.
- **Save as PDF** opens the browser print dialog, where they can choose "Save as PDF". Checked boxes
  print as checked, so a partly finished checklist prints as a real record.
- Editing the guide does not erase anyone's saved progress, because each checkbox is remembered by
  its own text rather than its position on the page.
