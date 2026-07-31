# Scaffold `nps-notes.bbingham.dev` — a personal student-facing site

## Context

The ME 2801 course site (`controls-intro.bbingham.dev`) works well: Quarto → GitHub Actions → `gh-pages`, custom subdomain, no manual build step. The need now is a second, course-independent site holding the material a student needs from *me* rather than from a course — general advising notes and technical how-tos. Rather than invent a new stack, this reuses the course site's publishing machinery verbatim and changes only identity (title, URL, nav) and content.

Naming follows the plain-functional convention that dominates academic personal sites (Ernst's `/advice/`, Broman's `tutorials`, Leek's guides), scoped to the institution: the site is `nps-notes`, a sibling subdomain to the existing course site, with two nav sections — Advising and How-Tos.

Decided with the user:
- Name: **nps-notes** — applied consistently to subdomain, repo, and title
- Address: `https://nps-notes.bbingham.dev`, repo `bsb808/nps-notes`
- Nav sections: **Advising** and **How-Tos**
- Site title: "NPS Notes — Brian Bingham"

## What is reused, and from where

All four of these come from `me2801/introduction-to-feedback-control/` and need only identity edits:

| Source | Reuse |
|---|---|
| `site/_quarto.yml` | Whole file — theme `cosmo`, `toc`, MathJax, `link-external-newwindow`, the wide `grid` block (`body-width: 1400px`). Change title / `site-url` / `repo-url` / navbar / footer. |
| `.github/workflows/publish-site.yml` | Whole file — `quarto-actions/setup@v2` + `publish@v2` to `gh-pages` on push to `main`. Change `path:` and the path filter (see below). |
| `site/CNAME` + `resources: [CNAME]` in `_quarto.yml` | The custom-domain mechanism. `CNAME` **must** stay listed under `resources:` or Quarto drops it from `_site/` and the domain unbinds on every deploy. |

| `site/.gitignore` | `/.quarto/`, `**/*.quarto_ipynb`, plus `/_site/`. |

Not reused: `_variables.yml`'s per-quarter fields (`quarter`, `term_start`, `term_end`) — those are course-cycle values with no analogue here.

## Repo layout

This repo hosts only a site, so the Quarto project sits at the repo root — no `site/` nesting. That is the one structural difference from the course repo and it costs two lines in the workflow (`path: .`, and a path filter of `"**/*.qmd"` / `_quarto.yml` rather than `site/**`).

```
nps-notes/                     # bsb808/nps-notes  →  clone to ~/WorkingCopies/nps-notes/
  _quarto.yml
  _variables.yml               # name, email, office, office hours, last-updated
  CNAME                        # nps-notes.bbingham.dev
  .gitignore
  index.qmd                    # landing: who this is for, two section cards
  about.qmd                    # contact, office hours, links out to courses
  advising/
    index.qmd                  # section landing + page list
    working-with-me.qmd
    thesis-timeline.qmd
    recommendation-letters.qmd
  howto/
    index.qmd                  # section landing + page list
    dev-environment.qmd
    git-for-thesis-work.qmd
    figures-for-writing.qmd
  assets/                      # images, small PDFs
  .github/workflows/publish-site.yml
  CLAUDE.md
  README.md
```

The advising/how-to page lists above are starting stubs — each gets a title, a one-line purpose, and a `TODO` callout so the nav is real and the structure is visible immediately. Prose is authored later, by you.

## Implementation steps

1. **Scaffold the tree** at `~/WorkingCopies/nps-notes/` (adjust if you want an umbrella + `nps-notes-claude` session sibling like the course repo — say so and I'll mirror that layout instead).

2. **`_quarto.yml`** — course config with identity swapped:
   ```yaml
   project:
     type: website
     output-dir: _site
     render: ["*.qmd", "advising/**/*.qmd", "howto/**/*.qmd"]
     resources: [CNAME]
   website:
     title: "NPS Notes — Brian Bingham"
     site-url: https://nps-notes.bbingham.dev
     repo-url: https://github.com/bsb808/nps-notes
     search: true                     # ON here — a reference site is searched, not read through
     navbar:
       pinned: true
       left: [Home, Advising, How-Tos, About]
       right: [github icon]
     page-footer: same shape as course site
   format:
     html: {theme: cosmo, toc: true, html-math-method: mathjax,
            link-external-newwindow: true, link-external-icon: true,
            grid: {body-width: 1400px, margin-width: 200px, sidebar-width: 200px}}
   ```
   Two deliberate departures from the course config: `search: true` (the course site sets `false`; a how-to collection is entered by search), and section-scoped `render` globs matching the new folders.

3. **`_variables.yml`** — `name`, `email`, `office`, `office_hours`, `courses` (link to `controls-intro.bbingham.dev`). Referenced from pages as `{{< var email >}}` exactly as the course site does in `index.qmd`.

4. **`CNAME`** — single line `nps-notes.bbingham.dev`.

5. **`.github/workflows/publish-site.yml`** — copy of the course workflow with `path: .` and the `paths:` trigger filter widened to the repo root.

6. **Content stubs** — `index.qmd` landing page and the section indexes with real page lists; each leaf page a titled stub.

7. **`CLAUDE.md`** — short: repo purpose, "Quarto site at root, published to `gh-pages` by Actions", the CNAME-under-`resources` gotcha, and the `.qmd` authoring conventions. Not a copy of the course `CLAUDE.md` — none of the MATLAB / LaTeX / ArduPilot sections apply.

8. **`README.md`** — one screen: what the site is, `quarto preview` to work on it, push to `main` to publish.

## Steps only you can do

- Create `bsb808/nps-notes` on GitHub (empty, no README) before the first push.
- Add DNS at the registrar: `CNAME` record, host `nps-notes`, value `bsb808.github.io`.
- After the first successful Actions run: repo Settings → Pages → source `gh-pages` / root, custom domain `nps-notes.bbingham.dev`, then Enforce HTTPS once the cert issues (a few minutes).

## Verification

1. Local: `quarto preview` from the repo root — confirm the navbar shows Home / Advising / How-Tos / About, every nav link resolves, and search returns results. `quarto render` should exit clean with no broken-link warnings.
2. Confirm `CNAME` survives the render: `cat _site/CNAME` → `nps-notes.bbingham.dev`. This is the single most common way this setup breaks.
3. Push to `main`; watch the Actions run finish and the `gh-pages` branch appear.
4. `curl -sI https://nps-notes.bbingham.dev` → `200`, and the page loads over HTTPS without a cert warning. (Compare against `controls-intro.bbingham.dev`, which returns `200` today; the apex `bbingham.dev` returns nothing and stays unused.)
5. Check the footer "Source on GitHub" link and the navbar GitHub icon both point at `bsb808/nps-notes`.

## Noted for later, not in scope

The course site's `site/autopilot/` section (ArduPilot parameters, PID architecture, waypoint navigation, with `.drawio`/`.svg` figure sources) is general reference material rather than ME 2801 material. It is the strongest candidate to migrate into `howto/` once this site exists — but moving it breaks inbound links from the course site, so it should be a deliberate, separate decision.
