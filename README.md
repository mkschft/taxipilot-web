# TaxiPilot — Growth Site (AEO + funnel)

A small, fast, static site built to **win traffic and get cited by AI answer engines** (ChatGPT,
Perplexity, Google AI Overviews) — then funnel that traffic into the app. No build step, no framework.
Pure HTML/CSS/JS + Inter. Deploy anywhere static.

## The strategy (three acquisition angles, one funnel)

1. **Free practice test** (`practice-test.html`) — the **share/virality engine**. People take a 2-minute
   test, get a score, and share it. Proven #1 edtech funnel. Every result screen pushes to the app.
2. **AEO pillar guide** (`guide/how-to-get-a-taxi-licence-in-finland.html`) — the **organic/AI-answer
   engine**. Answers the exact questions drivers ask ("how many questions", "where do I take it",
   "can I prepare in English"), structured so search + LLMs quote it. Funnels to the test and the app.
3. **Conversion landing** (`index.html`) — ties it together. Test-led hook, official-fact trust strip,
   app proof, FAQ.

> A fourth, **emotional** landing lives in `../landing-v2/` (deep-navy, photo-led). Use it for paid/social
> traffic. The detailed marketing page is in `../landing/`. All point to the same app.

## What makes this AEO-optimized

- **JSON-LD structured data** on every page: `Organization`, `WebSite`, `Course`, `Article`,
  `FAQPage`, `BreadcrumbList`. This is what gets pulled into rich results and AI answers.
- **`llms.txt`** — an LLM-readable summary with the key facts and primary links (emerging standard;
  helps assistants describe and cite you accurately).
- **`robots.txt`** — explicitly **allows AI crawlers** (GPTBot, OAI-SearchBot, PerplexityBot, ClaudeBot,
  Google-Extended, Applebot-Extended, CCBot). You want them in.
- **`sitemap.xml`** — clean, prioritised.
- **Question-shaped headings + factual, citable answers.** AEO rewards content that answers a question
  directly in the first sentence. The guide and FAQs are written that way.
- **Accuracy.** Facts are grounded in Traficom / Suomi.fi (50 questions, 45 min, Ajovarma, 5-yr validity,
  ~€170). AI engines down-rank pages that contradict authoritative sources. Where the official pass
  threshold isn't published, we say so rather than inventing a number.

## Before you deploy — replace the placeholder domain

Everything uses `https://taxipilot.fi` as the canonical domain. Find-and-replace it with your real domain
across: `index.html`, `practice-test.html`, `guide/*.html`, `sitemap.xml`, `robots.txt`, `llms.txt`.

Add `hero.jpg` (see `../landing-v2/README.md` for the image prompt) for the hero photo; without it the hero
falls back to clean navy.

## Deploy
```
cd site
npx vercel --prod --yes
```
Then in Google Search Console: submit `sitemap.xml`. In Bing Webmaster Tools: same.

## The keyword / answer map (what we're ranking for)

| Intent | Query examples | Page that answers it |
|---|---|---|
| How-to / process | "how to get taxi licence in Finland", "taksinkuljettajan ajolupa" | guide |
| Exam logistics | "how many questions taxi exam Finland", "where take taxi exam Ajovarma" | guide + home FAQ |
| Language barrier | "Finnish taxi exam in English", "taxi exam English Helsinki" | home + guide |
| Practice intent | "Finnish taxi exam practice test", "taksikoe harjoittelu" | practice-test |
| Cost / validity | "taxi licence Finland cost", "how long is taxi licence valid" | guide |

## How to add a new article (keep the AEO compounding)

1. Copy `guide/how-to-get-a-taxi-licence-in-finland.html` as a template.
2. Pick ONE question/topic with search demand (e.g. "Group 2 medical certificate for taxi drivers",
   "what's on the Finnish taxi exam", "taxi licence renewal in Finland").
3. Lead each section with a **direct answer in the first sentence**, then expand.
4. Update the `Article` + `FAQPage` JSON-LD (headline, dates, questions).
5. Add the URL to `sitemap.xml` and link it from the guide hub and the home `guidecard`.
6. Cross-link to the practice test and the app.

A handful of these, updated a few times a year, is what builds durable organic + AI-answer traffic.

## Files
```
site/
├─ index.html                                    # conversion landing (test-led)
├─ practice-test.html                            # free 5-Q test (share/traffic engine)
├─ guide/
│  └─ how-to-get-a-taxi-licence-in-finland.html  # AEO pillar article
├─ sitemap.xml
├─ robots.txt                                     # AI crawlers allowed
├─ llms.txt
└─ README.md
```
