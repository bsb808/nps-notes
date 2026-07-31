# nps-notes

Personal, course-independent student-facing site: advising notes and technical
how-tos. Published at <https://nps-notes.bbingham.dev>.

## Structure

Quarto website with the project at the **repo root** (no `site/` nesting — this
differs from the ME 2801 course repo, which nests). Two content sections:

- `advising/` — expectations, thesis timeline, recommendation letters
- `howto/` — technical guides (dev environment, git, figures)

`_variables.yml` holds contact and course-link values, referenced from pages as
`{{< var email >}}`. There are no per-quarter variables here — this site does not
follow a course cycle.

## Publishing

Push to `main` → `.github/workflows/publish-site.yml` renders with
`quarto-actions/publish@v2` and deploys to the `gh-pages` branch. No manual build
step. The workflow's `paths:` filter is root-scoped (`**/*.qmd`, `_quarto.yml`,
…) — extend it if new top-level content directories appear.

## Gotcha: CNAME must stay under `resources:`

`CNAME` carries the custom domain. If it is dropped from the `resources:` list in
`_quarto.yml`, Quarto will not copy it into `_site/`, and GitHub Pages unbinds
the custom domain on the next deploy. After any `_quarto.yml` change, verify:

```bash
quarto render && cat _site/CNAME    # → nps-notes.bbingham.dev
```

## Authoring conventions

- One `.qmd` per topic; section landing pages (`advising/index.qmd`,
  `howto/index.qmd`) carry the page list and must be updated when a page is added.
- Front matter: `title` plus a short `subtitle`.
- Unwritten pages carry a `::: {.callout-warning appearance="simple"} **TODO**`
  block and a one-paragraph statement of intended contents.
- Links between pages use relative `.qmd` paths so Quarto can check them.
- Local preview: `quarto preview` from the repo root.
