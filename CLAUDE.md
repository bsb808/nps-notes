# nps-notes

Personal, course-independent student-facing site: advising notes and
thesis-preparation guides. Published at <https://nps-notes.bbingham.dev>.

## Structure

Quarto website with the project at the **repo root** (no `site/` nesting — this
differs from the ME 2801 course repo, which nests). Two content sections:

- `advising/` — expectations, thesis timeline
- `thesis-prep/` — tools and technique (dev environment, git, figures)

Navigation follows the old-MIT-homepage shape Brian asked for: categories in a
left sidebar, detail in the main column at right. The navbar carries only Home /
About / GitHub; the two categories live in the `sidebar:` block of `_quarto.yml`.
Keep the sidebar to **two levels** — category, then pages. If a page needs
subsections, use headings within it rather than a third nav level.

`_variables.yml` holds name, course links, and the last-updated date, referenced
from pages as `{{< var course_site >}}`. There are no per-quarter variables here —
this site does not follow a course cycle.

The site carries **no contact information** — no email, no office, no office hours.
That is deliberate: students find him in the NPS global directory, and he does not
hold office hours. Do not add a contact block back.

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

- One `.qmd` per topic. Adding a page means updating three places: the category
  landing page's list, the `sidebar:` contents in `_quarto.yml`, and the table on
  `index.qmd`.
- Front matter: `title` plus a short `subtitle`.
- Unwritten pages carry a `::: {.callout-warning appearance="simple"} **TODO**`
  block and a one-paragraph statement of intended contents.
- Links between pages use relative `.qmd` paths so Quarto can check them.
- Local preview: `quarto preview` from the repo root.
