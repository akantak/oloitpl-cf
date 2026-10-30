# Homepage and blog

Upload `oloit-home-and-blog.zip` as a new deployment of the existing Cloudflare `blog` Worker. Keep its custom domains. No build step is required.

- `/`: minimal homepage with GitHub avatar and profile links
- `/blog/`: article list, newest first
- `/blog/corporate-proxy/`: existing proxy article, 22 November 2025
- `/blog/forgot-to-start-tmux/`: Medium article, 5 October 2023
- `/blog/aliens-and-decimal-systems/`: Medium article, 13 October 2022

Medium articles were imported from the author's public RSS feed with their original publication dates and complete bodies. Four article images are hosted locally; original image credits and references are retained. Medium tracking pixels were removed. Both imported articles end with a Medium source link. Existing Hugo article URLs redirect to the proxy article.

Edit the HTML and CSS directly, then ZIP the contents of `dist`, with index.html at the root. Local links, images, publication dates, code blocks, and redirect targets were checked. Browser layout has not been verified in this environment.

## Deploy automatically from GitHub

Repository: `akantak/oloitpl-cf`, production branch: `main`. Connect the repository from the existing `blog` Worker's Settings → Build page.

- Root directory: repository root (`/` or leave blank)
- Build command: empty (the HTML/CSS files are ready to serve)
- Deploy command: `npx wrangler deploy`
- Build variables/secrets: none needed for this site; use Cloudflare's default build authentication

`wrangler.jsonc` targets Worker `blog`, serves `dist/`, and declares both existing custom domains. Commit and push the config and site changes before triggering deployment. Do not set the root directory to `dist`: Wrangler's config is at the repository root. ZIP uploads are optional once Git deployment is connected.
