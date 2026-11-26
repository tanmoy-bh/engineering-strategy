# Engineering Strategy

A reliability concept for a consumer mobile platform, built as a conversation starter: watch the whole trip from unlock to return, ship app releases behind an unlock-success guardrail, and let AI draft incidents for a person to approve.

Live at **https://projects.tanmoybh.com/engineering-strategy/** (passphrase protected).

| Tab | What it shows |
| --- | --- |
| Trip health | Success vs target for each step of a trip, and failed unlocks by cause |
| What users report | Six friction points from public app reviews and press, each with a hypothesis, a metric and a first move |
| Review sentiment | Sentiment of the latest 500 public reviews from each of the App Store and Google Play, with Combined, iOS and Android views and a side-by-side comparison: star distribution, half-year trend, sentiment by theme and what recent reviews complain about |
| Guarded release | A staged rollout that halts itself when unlock success drops past the guardrail |
| AI copilot | An incident draft scored by an LLM judge and approved by a person |
| How I'd measure | The numbers each change is tied to |

## What is real and what is sample

**Real:** the friction themes, taken from public App Store reviews and press coverage, and the Review sentiment tab, computed on 26 Nov 2026 from the latest 500 public reviews on each of the App Store and Google Play (only aggregates and paraphrased themes are stored). **Sample:** every metric, version, incident and score. The page is not affiliated with or endorsed by any company.

## Layout

```
public/index.html         the page (HTML, CSS and JS, no build step)
functions/_middleware.js  passphrase gate (Cloudflare Pages Function)
tests/                    tests for the gate
```

## Passphrase

The site is gated on the server by `functions/_middleware.js`. The passphrase is never in the code or the page:

- GitHub repository secret `ENGINEERING_STRATEGY_PASSPHRASE` holds it.
- The deploy workflow copies it into the Pages secret `ACCESS_PASSPHRASE`.
- A correct passphrase sets an HttpOnly cookie for 14 days. Changing the passphrase signs everyone out.

## Run locally

```bash
printf 'ACCESS_PASSPHRASE=your-passphrase\n' > .dev.vars   # git-ignored
npm test        # tests for the passphrase gate
npm run dev     # wrangler pages dev public
```

## Deploy

Pushing to `main` runs `.github/workflows/deploy-cloudflare.yml`, which tests and deploys `public/` and `functions/` to the Cloudflare Pages project `tanmoybh-engineering-strategy`. It uses:

- `CLOUDFLARE_API_TOKEN` and `CLOUDFLARE_ACCOUNT_ID` from the `tanmoy-bh` organization secrets
- `ENGINEERING_STRATEGY_PASSPHRASE`, a repository secret you add once

The [projects hub](https://github.com/tanmoy-bh/projects) serves it at `/engineering-strategy/` by proxying to `https://tanmoybh-engineering-strategy.pages.dev`.
