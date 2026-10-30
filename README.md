# Homepage and blog

Upload `oloit-home-and-blog.zip` as a new deployment of the existing Cloudflare `blog` Worker. Keep its custom domains. No build step is required.

- `/`: minimal homepage with GitHub avatar and profile links
- `/blog/`: article list, newest first
- `/blog/corporate-proxy/`: existing proxy article, 22 November 2025
- `/blog/forgot-to-start-tmux/`: Medium article, 5 October 2023
- `/blog/aliens-and-decimal-systems/`: Medium article, 13 October 2022

Medium articles were imported from the author's public RSS feed with their original publication dates and complete bodies. Four article images are hosted locally; original image credits and references are retained. Medium tracking pixels were removed. Both imported articles end with a Medium source link. Existing Hugo article URLs redirect to the proxy article.

Edit the HTML and CSS directly, then ZIP the contents of `dist`, with index.html at the root. Local links, images, publication dates, code blocks, and redirect targets were checked. Browser layout has not been verified in this environment.
