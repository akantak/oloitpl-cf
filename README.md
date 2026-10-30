# Personal homepage and blog

A standalone, buildless HTML/CSS site. All deployment inputs live in this repository; Hugo and parent directories are not required.

## Deploy from GitHub

The user reports that Git deployment is connected and a build succeeded. Configuration for `akantak/oloitpl-cf` and the existing Cloudflare Worker **blog**, under **Settings → Build**:

| Setting | Value |
| --- | --- |
| Production branch | `main` |
| Root directory | Repository root (blank or `/`) |
| Build command | Empty |
| Deploy command | `npx wrangler deploy` |
| Application variables/secrets | None |

Use Cloudflare's default build authentication. `wrangler.jsonc` serves `dist/`, enables the custom 404 page, and declares `oloit.pl` and `www.oloit.pl`. Commit and push changes to `main` to trigger deployment. Do not set the root directory to `dist`.

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

Local checks cover HTML structure, local links and fragments, metadata, publication dates, structured data, sitemap and redirect targets. On 30 October 2026, both custom domains served the new homepage when connecting directly to Cloudflare with normal TLS verification. Their responses matched the Worker URL. Public DNS uses Cloudflare; the Mac/network resolver still returned the former droplet address. Browser rendering and a complete post-build check of article pages and redirects remain unverified. See `PROJECT_NOTES.md` for the precise evidence and status.

## DNS troubleshooting and former DigitalOcean host

The former droplet IP is `209.38.188.228`. Public DNS was verified to use Cloudflare nameservers `beau.ns.cloudflare.com` and `rachel.ns.cloudflare.com`, with Cloudflare IPv4 and IPv6 addresses. Do not hardcode observed Cloudflare IPs into DNS records or local files; they can change.

If the old welcome page still appears, compare public DNS and the local resolver before changing deployment settings. Try a phone on mobile data with Wi-Fi off. To clear a Mac's local DNS cache, run in Terminal and reopen the browser:

```sh
sudo dscacheutil -flushcache
sudo killall -HUP mDNSResponder
```

If needed, check VPN/router DNS or use a public DNS resolver. `/etc/hosts` had no matching domain/IP override during the investigation.

The website no longer needs the droplet. Before destroying it, save required server-only files/databases and confirm no other needed services depend on it. Destroying is permanent; powering off alone does not stop droplet billing. Review remaining paid snapshots, volumes, and other resources separately. Devices with stale DNS may briefly fail after deletion until their resolver refreshes. **The user confirmed the droplet was destroyed.** The website now runs on Cloudflare. Cleanup of any remaining separately billed DigitalOcean resources has not been confirmed.

References: [DigitalOcean destruction guide](https://docs.digitalocean.com/products/droplets/how-to/destroy/), [Droplet billing](https://docs.digitalocean.com/products/droplets/details/pricing/).
