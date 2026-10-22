# Working Knowledge

My working notes, built with [Jekyll](https://jekyllrb.com/) and published on
[GitHub Pages](https://int0x03.github.io/int0x03/).

## Topics

- [Time Series Anomaly Detection](time-series-anomaly-detection/README.md)
- [Large Language Models](llm/README.md)
- [scikit-learn](sklearn/README.md)
- [LeetCode](leetcode/README.md)

Each topic has a Markdown `README.md` with YAML front matter so Jekyll renders it
as a page. The `permalink` gives each topic a clean URL.

## Preview locally

Install Ruby 3.1 or newer and Bundler (`gem install bundler`), then run these
commands from the repository root:

```sh
bundle install
bundle exec jekyll serve
```

Open <http://localhost:4000/int0x03/>. To check a production build:

```sh
JEKYLL_ENV=production bundle exec jekyll build --strict_front_matter
```

Generated files go in `_site/` and are not committed. Restart the preview server
after changing `_config.yml`.

## Publish on GitHub Pages

1. Merge these files into the branch you want to publish (usually `main`).
2. Open **Settings → Pages** in this repository.
3. Select **Deploy from a branch**, choose that branch and **/ (root)**, and save.
4. GitHub Pages will build Jekyll and publish at <https://int0x03.github.io/int0x03/>.

This profile repository is a project site, so `_config.yml` uses
`baseurl: "/int0x03"`. If you reuse the structure, set `baseurl` to
`/repository-name` (or `""` for a `username.github.io` repository) and adjust
`url` to your GitHub Pages origin.

## Add notes

Add Markdown files with YAML front matter in the appropriate topic folder:

```yaml
---
title: My Note
permalink: /llm/my-note/
---
```

Follow the front matter with your Markdown content. The default page layout is
set in `_config.yml`. Link new notes from their topic README using the note's
permalink with Jekyll's `relative_url` filter, for example:

```liquid
[My Note]({{ '/llm/my-note/' | relative_url }})
```

For a new topic, add its README to `header_pages` in `_config.yml` and add a link
to `index.html` and this README.
