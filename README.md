# LoanNest — Editorial Red v5.7.1

A local-first loan and EMI manager for Windows and iPhone, featuring an editorial red-and-black interface, flexible installment schedules, partial payments, corrections/undo, audit history, PWA support, offline storage, and optional Cloudflare Workers AI chat.

## Project layout

- `index.html` — hosted Progressive Web App (GitHub Pages / Cloudflare Pages)
- `manifest.webmanifest`, `sw.js`, `icons/` — installable PWA assets
- `LoanNest_Windows_Standalone.html` — single-file Windows version; double-click to open
- `cloudflare-worker/` — optional AI backend source; **deploy separately**

## Run locally

For the single-file app, open `LoanNest_Windows_Standalone.html` in Chrome or Edge. For a proper PWA preview with service worker support, from this folder run:

```bat
py -m http.server 8000
```

and open `http://localhost:8000/` in your browser. The loan data remains in the current browser/device, not in GitHub.

## Publish on GitHub Pages

1. Push this project to a GitHub repository (prefer a dedicated repository named `loannest`).
2. In GitHub, open **Settings → Pages**.
3. Choose **Deploy from a branch**, select `main`, and `/(root)`, then save.
4. Open the Pages URL on an iPhone in Safari and choose **Share → Add to Home Screen**.

The PWA manifest uses relative paths, so it can be hosted beneath a GitHub Pages repository path.

## Optional Cloudflare Workers AI

The `cloudflare-worker/` directory contains the Worker source (already compatible with this app). It intentionally does **not** contain the private access code or any user loan records.

```bat
cd cloudflare-worker
npm install
npx wrangler login
npm run deploy
npx wrangler secret put LOANNEST_ACCESS_TOKEN
```

If you have already deployed the Worker, **you don't need to redeploy** just to publish the web app. In LoanNest **Settings → Cloudflare AI connection**, enter your existing Worker URL and access code. For a *new* GitHub Pages hostname, add only the site **origin** (such as `https://USERNAME.github.io`) to the `ALLOWED_ORIGINS` setting in `cloudflare-worker/wrangler.jsonc`, and redeploy the Worker. Never commit an access code. To protect the backend, use a long random Worker secret.

## Storage and safety

- Loan and payment records live in browser local storage. This repository does not sync devices or back up financial data to GitHub.
- Export a JSON backup from the old LoanNest version before importing into a new one; standalone HTML files may not share storage.
- Keep personal loan exports out of source control. `.gitignore` ignores common backup names, but check every file before committing.
- If sharing loan snapshots with Cloudflare AI, do so only after reviewing the privacy controls. The Worker uses a shared secret, not multi-user account authentication.
- Preserve this app's local-first design when adding remote functionality.

## Version

- Web UI: Editorial Red v5.7.1
- Cloudflare AI Worker: v5.0 compatible backend
