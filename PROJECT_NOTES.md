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
- Git deployment is now the intended workflow: connect `akantak/oloitpl-cf` to the existing `blog` Worker. ZIP upload remains a fallback. Do not create another Worker or a Sites-hosted replacement by default.
- Account namespace `ajkantak.workers.dev` is not itself a deployable Worker homepage; default Worker URLs include the Worker name.
- Preserve email DNS records when changing DNS.

At the last domain verification, the apex served the expected article through normal resolution, and direct Cloudflare HTTPS checks served it for both apex and www. A local DNS cache still reached the former server for www. Those checks preceded the homepage and multi-article updates. Do not assume the latest ZIP is deployed.

DigitalOcean deletion was discussed but never confirmed completed. Before deleting, verify the latest live site and preserve any required server data. No infrastructure deletion was performed by the assistant.

## Local files

Project: `/Users/akantak/PycharmProjects/blog/standalone` (now a Git repository).

- `dist/`: public deployment files; edit directly, no build step.
- `oloit-home-and-blog.zip`: current upload artifact, contents of `dist/` at ZIP root.
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

Latest requested change (two Medium imports and blog index) is prepared locally and packaged. User has not yet confirmed uploading this latest package. Next step is upload to existing `blog` Worker, then verify homepage, index, all article pages, assets, and old redirects on both domains.

## Session convention

User requests one completion ding immediately before every final response: `afplay /System/Library/Sounds/Glass.aiff`. No sounds for intermediate updates. If audio is unavailable, finish without it.

## Style update

All pages now use shared CSS variables, a consistent reading width, serif heading hierarchy, neutral paper background, teal links, responsive spacing, and keyboard focus styles. Blog index, articles, and 404 share Home/Blog navigation and author/All articles footers; the homepage retains only photo, name, and three requested links. Article headers match, original prose/dates/code/images remain unchanged. The 404 blog link label was corrected. RSS was recommended as a future addition, not implemented. Updated ZIP prepared; deployment and visual verification still pending.

## Git deployment setup

Remote discovered: `https://github.com/akantak/oloitpl-cf.git`, branch `main`. Added root `wrangler.jsonc` targeting Worker `blog`, assets `./dist`, custom 404 handling, and existing apex/www custom domains. Cloudflare Worker Settings → Build should connect that repository with an empty build command, deploy command `npx wrangler deploy`, and repository root as root directory. No application secrets required. Config and pending style changes have not been committed or pushed by the assistant. Git integration has not been verified connected.

## Whole-repository review

Reviewed all six HTML pages, shared CSS, images, seven Excalidraw JSON files, redirects, sitemap, Git state, Wrangler config and documentation. Added missing descriptions for imported articles and structured publication metadata for the proxy article; removed Medium RSS tracking parameters from source links and metadata; added image dimensions and more descriptive alt text; added missing homepage/404 skip links. Original article prose, examples and dates were preserved. README now leads with Git deployment and explains local-preview limitations. Added ignores for local Wrangler/node/Python output. The user's existing staged changes were preserved; review edits remain unstaged. Config syntax and local site checks passed; no live deployment performed.

Final review validation: six pages passed strict HTML nesting, local links and fragment resolution, image dimensions, page descriptions/canonicals, all original dates and JSON-LD, sitemap URLs, redirect destinations, Wrangler JSON, Excalidraw JSON, and exact ZIP-to-dist comparison. Both staged and unstaged whitespace checks passed. Safari preview attempt timed out; visual QA remains unverified.
