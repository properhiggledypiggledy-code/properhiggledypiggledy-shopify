# Proper Higgledy Piggledy Shopify Theme Workflow

This repository is set up for:

- local development with Shopify CLI
- collaborative GitHub workflow with pull requests
- automatic deploy to a staging theme on `main`
- controlled deploy to live theme from `release`

## 1) One-time local setup

```bash
cd /Users/coryfox/Desktop/properhiggledypiggledy-shopify
shopify auth logout --all
shopify auth login
shopify theme dev --store proper-higgledy-piggledy.myshopify.com
```

Use the preview URL from `shopify theme dev` while building features.

## 2) One-time Shopify setup for CI

1. In Shopify Admin, install/create the **Theme Access** app.
2. Create one password with access to the staging and live themes.
3. Note:
   - your `.myshopify.com` store domain
   - staging theme ID
   - live theme ID
   - Theme Access password

## 3) One-time GitHub setup

Add these repo secrets:

- `SHOPIFY_STORE` = `your-store.myshopify.com`
- `SHOPIFY_STORE` = `proper-higgledy-piggledy.myshopify.com`
- `SHOPIFY_API_PASSWORD` = (optional) Theme Access password or Admin API access token
- `SHOPIFY_CLIENT_ID` = (optional) Dev Dashboard app client ID
- `SHOPIFY_CLIENT_SECRET` = (optional) Dev Dashboard app client secret
- `SHOPIFY_STAGING_THEME_ID` = numeric ID for staging theme
- `SHOPIFY_LIVE_THEME_ID` = numeric ID for live theme

Authentication priority:

1. If `SHOPIFY_API_PASSWORD` (or legacy `SHOPIFY_CLI_THEME_TOKEN`) is set, workflows use it directly.
2. Otherwise workflows request a short-lived access token from Shopify using `SHOPIFY_CLIENT_ID` + `SHOPIFY_CLIENT_SECRET`.

Recommended protection:

- Protect `main` and `release` branches
- Require PR before merge
- Require status check `Theme Check`
- Add `production` environment approval before `deploy-live.yml` runs

## 4) Daily workflow

```bash
git checkout -b feature/your-change
shopify theme dev --store proper-higgledy-piggledy.myshopify.com
```

After changes:

```bash
git add .
git commit -m "Describe change"
git push -u origin feature/your-change
```

Then:

1. Open pull request to `main`
2. Merge after review and checks pass
3. GitHub Actions deploys to staging theme automatically
4. Promote by merging `main` into `release` (or cherry-picking), then push `release`
5. Live deploy runs via `deploy-live.yml`

## 5) Notes

- Do not commit Shopify credentials or any token files.
- Keep live-only edits disabled. Always edit through Git.
- If needed, create a snapshot by duplicating the live theme in Shopify before large releases.
