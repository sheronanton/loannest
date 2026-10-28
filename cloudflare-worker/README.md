# Cloudflare Workers AI backend for LoanNest v5.0

This Worker provides `POST /chat` and `GET /health`. It uses the Cloudflare AI binding and the Cloudflare-hosted `@cf/meta/llama-3.1-8b-instruct-fp8` model. It does not require an OpenAI/Gemini API key. **Cloudflare account setup and deployment must be completed by you.**

## Deploy from Windows (PowerShell or Command Prompt)

Install the latest Node.js LTS from https://nodejs.org, create a Cloudflare account at https://dash.cloudflare.com, then unzip the downloaded project and open a terminal in `LoanNest-AI-v50-Cloudflare`.

```bat
npm install
npx wrangler login
npm run deploy
npx wrangler secret put LOANNEST_ACCESS_TOKEN
```

When the last command prompts for a value, enter a **strong, random secret** (at least 24 random characters). This is a new private access code that **you choose**. It is NOT a Cloudflare account password, an API token or an existing loan password. Do not commit it to source control. Keep it in a password manager.

The deploy command prints a URL similar to:

`https://loannest-ai.YOUR-CLOUDFLARE-SUBDOMAIN.workers.dev`

Open LoanNest → Settings → **Cloudflare AI connection**. Paste **only the base URL** (without `/chat`) and the new private access code. Click **Save AI connection**, then **Test connection** (this tests reachability and configuration; the code itself is checked when you chat). Open **AI Assistant** and ask a question.

## Allowed origins for Windows HTML versus hosted iPhone PWA

The initial `wrangler.jsonc` contains:

```json
"ALLOWED_ORIGINS": "null,http://localhost:8788,http://127.0.0.1:8788"
```

- The literal `null` is the `Origin` sent by many browsers when opening a standalone local HTML file. That is a convenience for personal Windows use, not a strong origin restriction.
- For an iPhone PWA hosted at `https://example.github.io/loannest/`, add its **origin**, `https://example.github.io` (without the path).
- Example: `"ALLOWED_ORIGINS": "null,https://example.github.io"`
- After editing `wrangler.jsonc`, run `npm run deploy` again. For a stricter production deployment, remove `null` once you are using only your HTTPS-hosted app.

**Never put `LOANNEST_ACCESS_TOKEN` into `wrangler.jsonc`, the HTML, or a GitHub repository.** Use `wrangler secret put` only. Restrict your Worker to your app's origins and protect the long secret. This simple personal-use shared access code is not user authentication for a multi-user production app.

## API and troubleshooting

- `GET /health` → `{ "ok": true, "configured": true }` when the AI binding and Worker secret exist. This endpoint does not validate your code.
- `POST /chat` → JSON `{"question":"...", "history":[], "snapshot":null}` with `X-LoanNest-Key` header. This endpoint validates the code before invoking AI.
- **403 Origin not allowed**: Add your site origin to `ALLOWED_ORIGINS` and redeploy. Web requests opened from a local HTML file often use `null`.
- **401 Invalid access code**: Enter the same secret that you set with `wrangler secret put`.
- **503 AI unavailable**: The model may be down, the quota may be exhausted, or the account has access restrictions. Offline Insights remain available.
- **404**: The URL in LoanNest must be the Worker base URL, not a path like `/chat`.

This Worker does not store loan or payment data. Cloudflare processes the prompts you send according to its service terms and settings. Local device storage is not a remote backup. AI answers are not authoritative repayment or financial advice; use your lender statement as the source of truth.

## Current provider documentation

- https://developers.cloudflare.com/workers-ai/configuration/bindings/
- https://developers.cloudflare.com/workers-ai/models/llama-3.1-8b-instruct-fp8/
- https://developers.cloudflare.com/workers-ai/platform/pricing/
