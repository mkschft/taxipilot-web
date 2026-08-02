# Deploy runbook — TaxiPilot landing (taxipilot.fi)

## Production deployment (GitHub Actions → VPS)

Every push to `main` runs `.github/workflows/deploy.yml`. The workflow connects to the VPS over SSH and
runs, in order:

```bash
cd /var/www/landing
git pull --ff-only origin main
sudo -n nginx -t
sudo -n systemctl reload nginx
```

The Nginx configuration is checked before it is reloaded, so an invalid configuration stops the
deployment without reloading the service. Deployments can also be started manually from the repository's
**Actions → Deploy landing → Run workflow** page.

### Required GitHub configuration

Create a `production` environment in the GitHub repository, then add these environment secrets:

| Secret | Value |
|---|---|
| `DEPLOY_HOST` | VPS hostname or IP address |
| `DEPLOY_PORT` | SSH port, normally `22` |
| `DEPLOY_USER` | Unprivileged SSH deployment user |
| `DEPLOY_SSH_PRIVATE_KEY` | Private key used only for GitHub Actions deployments |
| `DEPLOY_SSH_KNOWN_HOSTS` | The VPS host-key line from a trusted `known_hosts` file |

The checkout at `/var/www/landing` must be on `main`, have `origin` configured, and be able to read the
GitHub repository (for example, through a read-only repository deploy key).

The deployment user must be allowed to validate and reload Nginx without an interactive password. Grant
only those commands in `sudoers`; do not give the deployment user unrestricted passwordless sudo. The
exact executable paths can be found on the VPS with `command -v nginx` and `command -v systemctl`.

The previous Vercel instructions are retained below as a legacy/alternative deployment path.

This `site/` folder is a **complete, standalone static website** — no build step, no framework.
It deploys **separately** from the app (that's intentional: zero coupling to the Expo build).

## Legacy Vercel architecture

| Domain | Serves | Vercel project |
|---|---|---|
| `taxipilot.fi` + `www.taxipilot.fi` | this landing (`site/`) | **new** — `taxipilot-web` |
| `app.taxipilot.fi` | the app | **existing** — `taxiapp` |

The landing's buttons already point to **https://app.taxipilot.fi**. The SEO metadata
(`canonical`, `sitemap.xml`, `robots.txt`, `llms.txt`) already uses `taxipilot.fi`. No edits needed.

---

## Legacy Track A — Version control (new repo `taxipilot-web`)

Run on your machine (it has your GitHub login; this sandbox does not).

```bash
# from the workspace
cd ~/Developer/taxi-app
cp -R site ../taxipilot-web        # lift it out so it's its own repo, not nested
cd ../taxipilot-web

git init
git add .
git commit -m "TaxiPilot landing: hero + free practice test + AEO guide"

# create the GitHub repo and push (GitHub CLI):
gh repo create mkschft/taxipilot-web --private --source=. --remote=origin --push
# …or create an empty repo on github.com first, then:
# git remote add origin https://github.com/mkschft/taxipilot-web.git
# git branch -M main && git push -u origin main
```

> Keeping it inside `taxi-app/site` instead? Then init git directly in `site/` — but a top-level
> sibling repo (`taxipilot-web`) is cleaner and what we agreed on.

## Legacy Track B — Deploy the landing on Vercel

Option 1 — connect the repo (recommended): Vercel dashboard → **Add New → Project** → import
`taxipilot-web` → Framework preset **Other**, no build command, output = root → **Deploy**.

Option 2 — straight from the folder:
```bash
cd ~/Developer/taxipilot-web   # or taxi-app/site
npx vercel --prod
```
When prompted: new project, no framework, no build command.

## Legacy Track C — Attach the domains (this is what makes it the front door)

1. **Landing → apex.** In the `taxipilot-web` Vercel project → **Settings → Domains** → add
   `taxipilot.fi` and `www.taxipilot.fi`. Follow Vercel's DNS records (A / CNAME) at your registrar.
2. **App → subdomain.** In the **existing** `taxiapp` Vercel project → **Settings → Domains** →
   add `app.taxipilot.fi`. Add the CNAME Vercel shows you.
3. Wait for DNS to propagate (minutes to a couple of hours). Done — `taxipilot.fi` shows the landing,
   the buttons send users to `app.taxipilot.fi`.

## Track D — After it's live (SEO/AEO)

- Google Search Console: add `taxipilot.fi`, submit `https://taxipilot.fi/sitemap.xml`.
- Bing Webmaster Tools: same.
- Confirm `https://taxipilot.fi/robots.txt` and `/llms.txt` resolve.

---

## What's in this repo
```
.
├─ index.html                                    # homepage (test-led landing)
├─ practice-test.html                            # free 5-question test (share/traffic engine)
├─ guide/
│  └─ how-to-get-a-taxi-licence-in-finland.html  # AEO pillar article
├─ sitemap.xml
├─ robots.txt                                     # AI crawlers explicitly allowed
├─ llms.txt
├─ vercel.json
├─ .gitignore
├─ README.md                                      # growth strategy + how to add articles
└─ DEPLOY.md                                       # this file
```

> Add a `hero.jpg` here for the hero photo (prompt in `../taxiapp`… see the landing-v2 notes, or use any
> warm photo of a driver / Helsinki street). Without it, the hero shows clean navy — not broken, just plain.
```
