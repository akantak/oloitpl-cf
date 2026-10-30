# Personal homepage and blog

A standalone, buildless HTML/CSS site. All deployment inputs live in this repository; Hugo and parent directories are not required.

## Deploy from GitHub

Connect `akantak/oloitpl-cf` to the existing Cloudflare Worker **blog** under **Settings → Build**:

| Setting | Value |
| --- | --- |
| Production branch | `main` |
| Root directory | Repository root (blank or `/`) |
| Build command | Empty |
| Deploy command | `npx wrangler deploy` |
| Application variables/secrets | None |

Use Cloudflare's default build authentication. `wrangler.jsonc` serves `dist/`, enables the custom 404 page, and declares `oloit.pl` and `www.oloit.pl`. Commit and push changes to trigger deployment once the Git integration is connected. Do not set the root directory to `dist`.

## Edit and preview

- `dist/index.html`: homepage, profile photo, GitHub/LinkedIn/Blog links.
- `dist/blog/index.html`: article index, newest first.
- `dist/blog/*/index.html`: articles; preserve original dates, code examples, credits, and source links.
- `dist/style.css`: shared styles.
- `dist/images/` and `dist/proxy/`: public images.
- `others/excalidraws/proxy/`: editable diagram sources, excluded from deployment.
- `dist/_redirects`: old article URLs pointing to the proxy article.
- `dist/sitemap.xml`: update when adding or removing a page.

From the repository root, preview the static pages with:

```sh
python3 -m http.server 8765 --directory dist
```

Open `http://localhost:8765`. This simple server does not process Cloudflare `_redirects` or replicate its custom 404 routing; check those after deployment. If Wrangler is available, `npx wrangler dev` previews the actual Worker configuration.

## Articles

| URL | Original date |
| --- | --- |
| `/blog/corporate-proxy/` | 22 November 2025 |
| `/blog/forgot-to-start-tmux/` | 5 October 2023 |
| `/blog/aliens-and-decimal-systems/` | 13 October 2022 |

The two older articles were imported from the author's Medium RSS feed. Each keeps its original text, date, images and references, and ends with a Medium source link. Article images are hosted locally; the homepage avatar loads directly from GitHub.

## Optional manual upload

`oloit-home-and-blog.zip` is an ignored fallback artifact. Package only the contents of `dist/`, with `index.html` at the ZIP root. Upload as a new deployment of the existing `blog` Worker. Git deployment does not need this ZIP.

## Verification

Local checks cover HTML structure, local links and fragments, metadata, publication dates, structured data, sitemap and redirect targets. Browser rendering and Cloudflare deployment behavior still require preview/live checks. See `PROJECT_NOTES.md` for the last verified state and infrastructure context.
