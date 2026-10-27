# James W. Cannon — Engineering Portfolio

Mechanical engineering project portfolio, live at **https://jacan20.github.io/portfolio/**.

The site is a [Jekyll](https://jekyllrb.com/) blog built on a customized copy of the
[Minima](https://github.com/jekyll/minima) theme. Each project is a post, tagged as either an
academic (coursework) or personal project; the home page lists the two side by side.

## Structure

| Path | Purpose |
|---|---|
| `_posts/` | One Markdown file per project |
| `Media/` | Images used by posts, one folder per project |
| `_config.yml` | Site title, bio, social links |
| `about.md` | About page |
| `academic-projects.html`, `personal-projects.html` | Category list pages |
| `_layouts/home.html` | Two-column home page (Academic / Personal) |
| `_includes/header.html` | Header with profile picture and title |
| `_includes/head.html` | `<head>`, including MathJax for LaTeX math |
| `_sass/minima/custom-styles.scss` | Custom styling |

## Adding a project

Create `_posts/YYYY-MM-DD-short-name.md`:

```markdown
---
layout: post
title: "MECH 1234 - Project Name"
date: 2025-01-31
categories: [Academic Project]   # or [Personal Project]
---

Write-up goes here. Inline math: $x^2$, display math: $$ \pi \approx 3.14 $$

![Figure caption](/portfolio/Media/my-project/figure.png)
```

Put images in `Media/<project>/`. The `categories` value decides which column and list page
the post appears in.

Project pages are served at `/projects/<name>/`, where `<name>` comes from the filename (or a
`slug:` in the front matter). If you ever rename a post, add its old URL under `redirect_from:`
so existing links keep working.

## Local preview

Requires Ruby and Bundler.

```sh
bundle install
bundle exec jekyll serve --baseurl /portfolio
```

Then open http://localhost:4000/portfolio/. The `--baseurl` flag matches the `/portfolio/...`
image paths used in posts.

## Deployment

Every push to `gh-pages` runs `.github/workflows/jekyll.yml`, which builds the site with the
Gemfile's Jekyll version and deploys it to GitHub Pages.

## License

Theme code is from Jekyll Minima, used under the MIT License (see `LICENSE.txt`).
