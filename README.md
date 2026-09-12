# eeoon.github.io

CV, portfolio and paper-review site of Youngeon Kim, built on the [al-folio](https://github.com/alshedivat/al-folio) v1 theme (Jekyll).

## Layout

| Path | Contents |
| --- | --- |
| `_pages/about.md` | Landing page: intro, research interests, experience, ongoing national R&D projects; selected publications are appended automatically |
| `_projects/*.md` | Project cards. `category: company` (KETI) / `personal` (earlier work). Lower `importance` sorts first |
| `_bibliography/papers.bib` | Papers and patent. `selected={true}` shows on the landing page; `preview` images live in `assets/img/publication_preview/` |
| `_posts/*.md` | Blog. `categories: paper-review` (paper reviews) / `tech-note` (technical notes) |
| `_news/*.md` | Timeline shown at `/news/` |
| `_data/cv.yml` | CV page data (RenderCV-like format; `Patents` is a generic section) |
| `_data/socials.yml` | Email, GitHub and other social icons |
| `_config.yml` | Site settings (`url`, `lang`, scholar name filters, etc.) |
| `assets/img/` | `prof_pic.jpg`, `projects/<slug>/`, `publication_preview/`, `blog/` |

## Local build

```bash
bundle install
bundle exec jekyll serve --livereload   # http://localhost:4000
```

Ruby 3.3+ and ImageMagick (responsive images) recommended. With Docker: `docker compose up`.

## Deployment

Pushing to `main` runs `.github/workflows/deploy.yml`, which builds the site and pushes it to the `gh-pages` branch.
Repository **Settings → Pages → Source: Deploy from a branch → `gh-pages` / (root)**.

## Adding content

- Paper review: `_posts/YYYY-MM-DD-slug.md` with `categories: paper-review` and lowercase tags.
- Project: `_projects/slug.md`; put images in `assets/img/projects/slug/` and embed with `{% include figure.liquid path="..." %}`.
- Publication: add an entry to `papers.bib` plus `assets/img/publication_preview/<name>.png`.
