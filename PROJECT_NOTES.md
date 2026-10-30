# Project knowledge

Updated: 30 October 2026.

## Purpose and user preferences

The user retired the `oloitpl` project and wants to stop paying for the DigitalOcean droplet. They disliked maintaining the Hugo blog and wanted to retain their articles. The replacement is a buildless static HTML/CSS site hosted in their own Cloudflare account.

The homepage should contain only Aleksander Kantak's name, a profile photo, and GitHub, LinkedIn, and Blog links. Avoid extra descriptions or promotional content.

- GitHub: https://github.com/akantak
- LinkedIn: https://www.linkedin.com/in/ajkantak/
- Photo: https://github.com/akantak.png?size=320 (linked directly and wrapped in a GitHub profile link)

## Hosting and domains

- Cloudflare Worker name: `blog` (originally `proxy`).
- Worker URL: https://blog.ajkantak.workers.dev
- Custom domains: https://oloit.pl and https://www.oloit.pl
- Domain registrar: OVH; registration remains there and renewal still costs money.
- Nameservers moved to Cloudflare: `beau.ns.cloudflare.com` and `rachel.ns.cloudflare.com`.
- Git deployment is connected to `akantak/oloitpl-cf` on `main`; user reports committing the changes and a successful build. ZIP upload is only a fallback. Do not create another Worker or a Sites-hosted replacement by default.
- Account namespace `ajkantak.workers.dev` is not itself a deployable Worker homepage; default Worker URLs include the Worker name.
- Preserve email DNS records when changing DNS.

Latest production checks on 30 October 2026: public DNS-over-HTTPS returned Cloudflare nameservers and Cloudflare A/AAAA records for the apex; www A records also returned Cloudflare. Direct HTTPS requests to both custom domains through a currently resolved Cloudflare address succeeded (HTTP 200, normal TLS validation) and served the new personal homepage. Their HTML matched https://blog.ajkantak.workers.dev. These are response/content checks, not browser layout verification.

The Mac/network's normal resolution still connected to former droplet `209.38.188.228` and displayed `Welcome to oloit.pl!`, despite correct public DNS. This points to stale local/upstream DNS. No matching `/etc/hosts` override was found. Suggested flushing macOS DNS (`sudo dscacheutil -flushcache`, `sudo killall -HUP mDNSResponder`), reopening the browser, comparing with mobile data, and checking configured DNS/VPN/router if needed. These privileged cache-clearing commands were provided to the user, not executed by the assistant. Do not change correct Cloudflare routing merely because a cached resolver still reaches the old server.

The website no longer requires DigitalOcean. User asked whether it was safe to destroy the droplet; assistant confirmed the website migration, conditional on saving server-only data and checking other services. **The user subsequently confirmed the droplet was removed.** No infrastructure deletion was performed by the assistant. Destroying is irreversible; power-off alone continues billing. Remaining paid resources (snapshots/volumes/etc.) require separate review. Clients with stale DNS can temporarily fail after destruction.

## Local files

Project: `/Users/akantak/PycharmProjects/blog/standalone` (now a Git repository).

- `dist/`: public deployment files; edit directly, no build step.
- `oloit-home-and-blog.zip`: ignored optional manual-upload artifact, contents of `dist/` at ZIP root. Git deployment uses `dist/` directly; regenerate the ZIP before any fallback upload.
- `others/excalidraws/proxy/`: editable sources for the seven proxy diagrams; not deployed.
- `README.md`: brief operating instructions.

Original Hugo source was at `/Users/akantak/PycharmProjects/blog/blog`; article at `content/posts/proxy.md`, diagrams in `static/proxy/`. User may reorganize old files; verify paths before relying on them. Original Hugo working tree had unrelated changes; it was not cleaned or committed.

## Content and URLs

`/` is the minimal personal homepage. `/blog/` is a blog index listing all three articles, newest first.

1. `/blog/corporate-proxy/`
   - Title: All those things about corpo proxy I wish I knew from the day one
   - Original date: 22 November 2025, from Hugo frontmatter.
   - Seven local PNG diagrams in `/proxy/`.
2. `/blog/forgot-to-start-tmux/`
   - Title: Did you forget to start tmux/screen before running a long process? Again?
   - Original publication: 5 October 2023, 09:11:27 UTC.
   - Source: https://medium.com/@akantak/did-you-forget-to-start-tmux-screen-before-running-a-long-process-again-d00f22f19cbd
   - One local image, seven code blocks, original image attribution and reference links preserved.
3. `/blog/aliens-and-decimal-systems/`
   - Title: How am I sure that human-like aliens do count in the decimal system?
   - Original publication: 13 October 2022, 21:25:35 UTC.
   - Source: https://medium.com/@akantak/how-am-i-sure-that-human-like-aliens-do-count-in-the-decimal-system-ec87af089d9f
   - Three local images.

Both Medium articles retain full text and end with a link to the original Medium page. Publication dates appear visibly and in structured metadata. Do not silently rewrite or fact-correct the articles during migration.

Direct Medium HTTP requests returned 403, but https://medium.com/feed/@akantak supplied complete HTML bodies and original dates. Images were downloaded from Medium's CDN to `dist/images/`; photo credits retained. Tracking pixels were removed.

Legacy paths `/blog/posts/proxy`, `/posts/proxy`, and `/blog/proxy` (with and without trailing slash) redirect to `/blog/corporate-proxy/` through `dist/_redirects`. `/blog/` must remain the index, not redirect to an article. Canonicals and sitemap target oloit.pl.

## Packaging and validation

Recreate the ZIP with Python's standard library from project root:

```python
from pathlib import Path
from zipfile import ZipFile, ZIP_DEFLATED
with ZipFile('oloit-home-and-blog.zip', 'w', ZIP_DEFLATED) as archive:
    for path in sorted(Path('dist').rglob('*')):
        if path.is_file():
            archive.write(path, path.relative_to('dist'))
```

Latest package checks passed for all local links, three article dates, original Medium source links, four imported images, seven proxy diagrams, seven tmux code blocks, redirect destinations, sitemap, and ZIP integrity. Homepage retains its photo and profile links.

Browser appearance has not been verified. The sandbox blocked binding a local HTTP preview server and `dig`; curl and DNS-over-HTTPS worked. `curl --resolve` to a current Cloudflare A record allowed checking the new host while local DNS remained cached (keep normal TLS verification enabled). Discover current IPs instead of reusing stale ones.

User reports committing the repository changes and a successful Cloudflare Git build. Latest homepage is confirmed at Worker and both custom domains via direct Cloudflare requests. A full post-build production check of the blog index, three articles, assets, redirects, and 404 handling is still outstanding; do not infer those checks from the homepage result.

## Session convention

User requests one completion ding immediately before every final response: `afplay /System/Library/Sounds/Glass.aiff`. No sounds for intermediate updates. If audio is unavailable, finish without it.

## Style update

All pages now use shared CSS variables, a consistent reading width, serif heading hierarchy, neutral paper background, teal links, responsive spacing, and keyboard focus styles. Blog index, articles, and 404 share Home/Blog navigation and author/All articles footers; the homepage retains only photo, name, and three requested links. Article headers match, original prose/dates/code/images remain unchanged. The 404 blog link label was corrected. RSS was recommended as a future addition, not implemented. The user subsequently committed changes and reported a successful Git build; visual verification remains pending.

## Git deployment setup

Remote discovered: `https://github.com/akantak/oloitpl-cf.git`, branch `main`. Added root `wrangler.jsonc` targeting Worker `blog`, assets `./dist`, custom 404 handling, and existing apex/www custom domains. Cloudflare Worker Settings → Build should connect that repository with an empty build command, deploy command `npx wrangler deploy`, and repository root as root directory. No application secrets required. The user subsequently confirmed committing changes and a successful build. The assistant did not commit or push. Git deployment is the active workflow.

## Whole-repository review

Reviewed all six HTML pages, shared CSS, images, seven Excalidraw JSON files, redirects, sitemap, Git state, Wrangler config and documentation. Added missing descriptions for imported articles and structured publication metadata for the proxy article; removed Medium RSS tracking parameters from source links and metadata; added image dimensions and more descriptive alt text; added missing homepage/404 skip links. Original article prose, examples and dates were preserved. README now leads with Git deployment and explains local-preview limitations. Added ignores for local Wrangler/node/Python output. The user's staged changes were preserved during review. The user subsequently committed changes and reported a successful build. Config syntax and local site checks passed; the assistant did not perform that deployment.

Final review validation: six pages passed strict HTML nesting, local links and fragment resolution, image dimensions, page descriptions/canonicals, all original dates and JSON-LD, sitemap URLs, redirect destinations, Wrangler JSON, Excalidraw JSON, and exact ZIP-to-dist comparison. Both staged and unstaged whitespace checks passed. Safari preview attempt timed out; visual QA remains unverified.

## Documentation handoff

Updated README, AGENTS and these notes after the successful-build and DNS discussion. Git is the primary workflow, ZIP packaging is optional, and the user has now confirmed droplet removal. Do not assume the user's local DNS cache has cleared. Documentation changes were left for the user to commit; no site or infrastructure changes were made in this documentation update.

## DigitalOcean retirement confirmed

On 30 October 2026, the user explicitly confirmed droplet removal. Treat the former server at `209.38.188.228` as retired. Do not suggest retaining it or using it for rollback. Cloudflare is the website host; OVH remains the domain registrar. Removal of any separate billable snapshots, volumes, or other DigitalOcean resources has not been confirmed. Local DNS cache clearance has also not been confirmed.
