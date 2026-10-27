# MERCADO — GitHub + Render deployment

## Files
- `server.js`: Express backend and API.
- `public/index.html`: MERCADO storefront.
- `package.json`: Node dependencies and scripts.
- `.env.example`: environment-variable template only; contains no real credentials.
- `render.yaml`: Render Blueprint configuration.
- `.gitignore`: excludes secrets, local data and dependencies.

## Deploy
1. Create a GitHub repository named `mercado`.
2. Upload the contents of this folder, keeping `server.js`, `package.json`, `render.yaml` and the `public` folder at the repository root.
3. In Render, choose **New + → Blueprint** and connect the repository, or create a **Web Service** with runtime Node, build command `npm install`, start command `npm start`, and health check path `/api/health`.
4. In Render → Environment, set `PUBLIC_BASE_URL` to the real Render service URL and add the provider credentials you actually use. Do not upload `.env` or real keys to GitHub.
5. Keep `PRICE_MARKUP_USD=10`; the code enforces a minimum of 10 USD markup.
6. Open `https://YOUR-SERVICE.onrender.com/api/health`. A healthy response should contain `"ok":true`.

## Important before production
- This package stores orders, inbox events and other records in JSON files. The free Render filesystem may be ephemeral; files can be lost during redeploy/restart. For durable data, use a persistent disk mounted at `/var/data` (if supported by your Render plan) and set `DATA_DIR=/var/data`, or migrate storage to PostgreSQL. The included free-plan Blueprint does not attach a paid disk automatically.
- Set `CAMERPAY_CALLBACK_SECRET` to the official secret from CamerPay before enabling live payments. Confirm all CamerPay URLs and paths against your provider account.
- Use a verified sender/domain for production Resend email; `onboarding@resend.dev` is only a test sender.
- Add CJ, CamerPay, Resend and Telegram credentials only in Render Environment. If any real keys were previously shared in chats or repositories, rotate them.
- Static file/configuration checks are not a live integration test. Before launch, test product categories, inbox persistence, a test order, email/Telegram notifications, and payment callbacks in test mode.

## Useful endpoint
- `GET /api/health`

See the code for the available API routes and admin key requirement.

## MERCADO AI / intelligence du bot

- L'endpoint `POST /api/ai` utilise Claude si `ANTHROPIC_API_KEY` est configurée côté Render.
- Sans clé IA, un mode local gratuit répond aux questions courantes (salutations, conseils à partir des produits visibles, prix du produit sélectionné, livraison, paiement et suivi de commande). Il ne confirme jamais un paiement, un stock ou une date de livraison sans donnée fiable.
- Pour obtenir des réponses génératives plus approfondies, configurez `ANTHROPIC_API_KEY` et éventuellement `ANTHROPIC_MODEL` dans Render Environment. Ne placez jamais ces clés dans le HTML ou dans GitHub.
- Le mode local est une intelligence à règles : il ne remplace pas un grand modèle de langage et n'effectue pas d'actions sur les commandes.
