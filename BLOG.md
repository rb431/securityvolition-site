# Adding a blog post

The blog is built by GitHub Pages with Jekyll. New posts are Markdown files in `_posts/`. The landing page (`index.html`), `CNAME`, and the newsletter embed are static files. Leave them as they are.

## Add a post

1. Create `_posts/YYYY-MM-DD-slug.md`. The filename date and slug become the URL.
2. Use this front matter, then write the body in Markdown. The body is published as written.

```yaml
---
layout: post
title: Post title
date: 2026-11-09T08:32:00-05:00
tags: [dfir, example]
linkedin_url: https://www.linkedin.com/feed/update/...
source: https://example.com/optional-source
type: post
---

First paragraph. This is the excerpt on the blog index (trimmed to 320 characters).

Rest of the post.
```

- `type` is `post` or `article`.
- `source` is optional. Omit the line when there is no source link.
- `linkedin_url` is the original LinkedIn post or article. The post page links it as "Originally published on LinkedIn".
- `tags` is a list of labels.
- `date` is the sort key and the date shown on the post. Use an ISO 8601 timestamp with a numeric offset (`-05:00` or `-04:00` for US Eastern). Newest dates are listed first.
- The opening paragraph, up to the first blank line, is the index excerpt.
- Put only post files in `_posts/`. Do not add an `INDEX.md` or other notes there.

Open a pull request. After it is merged, GitHub Pages publishes:

`https://securityvolition.com/blog/YYYY/MM/DD/slug/`

The Atom feed is [https://securityvolition.com/blog/feed.xml](https://securityvolition.com/blog/feed.xml).

## Preview locally

```bash
bundle install
bundle exec jekyll build
bundle exec jekyll serve
```

Then open `http://127.0.0.1:4000/blog/`.

Do not add a `.nojekyll` file. That switches GitHub Pages to a static copy and these Markdown posts will not render.
