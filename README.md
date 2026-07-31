# nps-notes

Source for <https://nps-notes.bbingham.dev> — advising notes and
thesis-preparation guides for NPS students, independent of any one course.

## Working on it

```bash
quarto preview        # live preview at localhost, from the repo root
quarto render         # one-shot build into _site/
```

## Publishing

Push to `main`. A GitHub Actions workflow renders the site and deploys it to the
`gh-pages` branch, which GitHub Pages serves at the custom domain. There is no
manual build or upload step.

## Layout

```
_quarto.yml        site config (title, nav, theme, CNAME resource)
_variables.yml     name, course links, last-updated date used by pages
CNAME              custom domain
index.qmd          landing page — table of categories and their pages
about.qmd          what the site is, links out to courses
advising/          advising notes
thesis-prep/       tools and technique for thesis work
assets/            images and small PDFs
```
