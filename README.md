# apify web scraper: how Apify's browser-based scraper works, what it really costs, and when a request-based API is the simpler choice

Apify's Web Scraper is a ready-made, browser-based scraping tool: you give it a list of start URLs, it loads each page in a real Chrome browser, and you tell it what to extract with a small JavaScript function. It's a solid pick if you want a managed platform with a marketplace of pre-built scrapers and don't mind learning a bit of JavaScript. If you'd rather keep your existing code and just outsource the proxies, retries, and bot-blocking, a request-based API like ScraperAPI does that with a single endpoint — and the pricing math between the two models is very different, which is what most comparisons skip.

> **Quick pick:** If you want zero infrastructure and predictable per-request pricing, start with ScraperAPI's free trial — 5,000 credits for 7 days, no credit card required, and every paid plan carries a 7-day refund. The Hobby plan at **$49/month** covers most side projects. 👉 [Start the free trial and check current plan pricing](https://www.scraperapi.com/?fp_ref=coupons)

## What Apify's Web Scraper actually does

Web Scraper is one of the ready-made "actors" in Apify's store. Under the hood it runs a real Chrome browser, which means it handles JavaScript-heavy pages the same way your own browser would. The setup has two parts:

1. **Tell it which pages to load.** You enter start URLs, and optionally enable link-following so the scraper keeps crawling pages it discovers along the way.
2. **Tell it what to extract.** You write a `pageFunction` — a JavaScript function that runs on every page the scraper visits. To write one, you inspect the target page with your browser's DevTools to figure out its structure.

That second step is the real learning curve. The tool takes away all the browser setup and produces machine-readable output in several common formats, but you're still writing JavaScript against each site's DOM. Apify's own documentation is upfront that DevTools inspection is how you figure out the pageFunction.

If your target pages are simple and static, Apify also offers lighter options like its Cheerio Scraper, which skips the browser entirely — the company's own example prices extracting 10,000 pages at around $9 that way.

## Where the platform model gets expensive: Apify's pricing mechanics

Apify charges for compute resources, not requests. Every plan includes a prepaid monthly usage balance, and everything you run — actor compute time, proxy traffic, storage reads and writes — draws from that balance. The current plans:

| Plan | Prepaid monthly usage | Compute unit (CU) rate | Max concurrent runs | Residential proxies |
| --- | --- | --- | --- | --- |
| Free | $5/month | $0.20/CU | 5 | $8/GB |
| Starter | $19/month + pay-as-you-go | $0.20/CU | 32 | $8/GB |
| Scale | $199/month + pay-as-you-go | $0.16/CU | 128 | $7.5/GB |
| Business | $999/month + pay-as-you-go | $0.13/CU | 256 | $7/GB |
| Enterprise | Custom | Custom | Custom | Custom |

Two things in that structure matter more than people expect:

- **Credits don't roll over.** Apify states plainly that unused usage credits expire at the end of each billing cycle. If you sign up, use $2 of your $5, and come back three weeks later, that $3 is gone.
- **Residential proxies bill separately at $8/GB** on the lower tiers. A Web Scraper run that pulls 2 GB of pages through residential IPs eats $16 of your balance before compute time even enters the picture. Also note that runs keep consuming platform usage after they finish — reading and writing results from a dataset counts too.

None of this makes Apify a bad deal. It makes Apify a platform whose total cost depends on how efficiently your actors run and how much proxy traffic your targets demand. That's fine when you're committed to building on the platform. It's less fine when you just need pages fetched reliably.

## The simpler alternative: pay per request, not per compute-hour

ScraperAPI attacks the same problem from the other end. You send a URL to one API endpoint; it routes the request through a rotating pool of residential and mobile proxies, renders JavaScript in a headless browser, handles CAPTCHAs and anti-bot systems (Cloudflare, DataDome, PerimeterX, Akamai), retries automatically, and returns HTML or auto-parsed JSON. You never touch proxy configuration, and bandwidth is unlimited — you pay per request, not per gigabyte.

Every paid plan includes the same feature set: JS rendering, premium residential and mobile IPs, automatic retries, sticky sessions (`session_number`, kept for 15 minutes), structured-data endpoints for Amazon, Walmart, eBay, Home Depot, Google Search/News/Jobs/Shopping/Maps, Redfin, and AI answer engines like ChatGPT, Perplexity, and Gemini, plus an async batch API, a low-code DataPipeline (up to 100,000 URLs, keywords, ASINs, or Walmart IDs per project), a full crawler, and integrations with LangChain, LlamaIndex, n8n, Zapier, and an MCP server that works with Claude Code.

Here's the full current lineup, with annual billing shown since it knocks 10% off every paid tier:

| Plan | Monthly price | Annual (per month) | API credits | Concurrent threads | Get started |
| --- | --- | --- | --- | --- | --- |
| Free | $0 | — | 1,000 | 5 | [ Claim free credits](https://www.scraperapi.com/?fp_ref=coupons) |
| Hobby | $49 | $44.10 | 100,000 | 20 | [ Start Hobby](https://www.scraperapi.com/?fp_ref=coupons) |
| Startup | $149 | $134.10 | 1,000,000 | 50 | [ Start Startup](https://www.scraperapi.com/?fp_ref=coupons) |
| Business | $299 | $269.10 | 3,000,000 | 100 | [ Start Business](https://www.scraperapi.com/?fp_ref=coupons) |
| Scaling | $475 | $427.50 | 5,000,000 | 200 | [ Start Scaling](https://www.scraperapi.com/?fp_ref=coupons) |
| Professional | $975 | $877.50 | 10,500,000 | 300 | [ Start Professional](https://www.scraperapi.com/?fp_ref=coupons) |
| Advanced | $1,975 | $1,777.50 | 21,500,000 | 500 | [ Start Advanced](https://www.scraperapi.com/?fp_ref=coupons) |
| Enterprise | Custom | Custom | 22,000,000+ | 500+ | [ Talk to sales](https://www.scraperapi.com/?fp_ref=coupons) |

Per-request economics improve sharply as you go up: Hobby works out to $0.49 per 1,000 credits, Startup $0.15, Business $0.10, and the top tiers settle around $0.092–$0.095. What actually changes between tiers is credits, concurrency, geotargeting (Hobby and Startup are limited to US and EU; country-level global targeting starts at Business), analytics history (30 days on the lower two, unlimited from Business up), and support level.

New accounts get a 7-day trial with 5,000 credits and no credit card, and paid plans carry a 7-day refund, so you can test against your actual targets before committing. 👉 [Try ScraperAPI free for 7 days](https://www.scraperapi.com/?fp_ref=coupons)

## The credit-cost table most reviews leave out

The biggest source of "ScraperAPI is expensive" complaints is a pricing detail that's documented but easy to miss: **a credit is the cost of a standard page, and many popular targets cost more.** One credit buys you one normal page fetch. But:

| Request type | Credits per request |
| --- | --- |
| Standard page | 1 |
| Amazon, Walmart, eBay | 5 |
| Google, Bing (all subdomains) | 25 |
| LinkedIn | 30 |
| `render=true` (JS rendering) | +10 |
| `premium=true` proxies | +10 |
| `ultra_premium=true` | +30 (paid plans only) |
| `ultra_premium + render` | 75 (paid plans only) |
| Bot-protection bypass (Cloudflare, DataDome, PerimeterX, Akamai) | +10 |

Do the arithmetic before you pick a plan. On the Hobby plan's 100,000 credits, plain 1-credit fetches get you 100,000 pages. The same balance spent on Google SERP requests gets you 4,000 searches. Add rendering and ultra-premium proxies and a single request burns 75 credits — your 100,000 credits become about 1,300 fetches.

Three things make this manageable rather than scary:

- **You can price a URL before scraping it.** The dashboard has a Domain Cost Estimator, an API Playground for testing target URLs, and a cost endpoint (`/account/urlcost`) that accepts all parameters and returns the exact credit cost. There's also a `max_cost` parameter to cap what any single request can consume.
- **Several useful parameters are free:** `country_code`, `session_number`, `device_type`, `wait_for_selector`, `output_format`, and `autoparse` add nothing.
- **Failed requests are free.** ScraperAPI only charges for successful requests (HTTP 200 and 404) and for requests you cancel within 70 seconds. Every response header includes a `sa-credit-cost` field showing exactly what that request cost.

The same logic applies to Apify, by the way — a Web Scraper run against a heavily protected site will consume more CU and more unblocker/proxy budget than one against a static blog. Differential cost per target exists in both models; only ScraperAPI publishes it as a flat, predictable per-request number.

## Billing rules that change your real cost

A few terms-of-service-level details decide whether a plan is actually as cheap as its sticker price. These rarely make it into comparison posts.

**What happens when credits run out.** On ScraperAPI, scraping pauses by default until credits renew — nothing gets charged in the meantime. Hobby through Professional plans can opt into auto-upgrade, which moves you to the next tier automatically when you exhaust credits. Scaling and above can instead use pay-as-you-go: keep scraping at a fixed per-credit rate up to a monthly spending cap you set yourself. Apify handles overage differently on paid plans: usage beyond your prepaid balance is added to your next invoice, and the free plan simply gets locked until the next cycle starts.

**Concurrency overages cost nothing.** Exceed your thread limit on ScraperAPI and you get an HTTP 429 asking you to retry at your plan's concurrency — the request is refused, not queued, and not billed. There's no surprise surge billing for parallelism.

**Free tiers are structured differently.** ScraperAPI's free plan is a permanent 1,000 credits with 5 concurrent connections (its crawler limited to depth 1 and no scheduling), plus the 7-day 5,000-credit trial on top. Apify's free plan gives $5 in monthly platform credits — which the company estimates covers roughly 500 to 1,000 web pages — with no credit card required. Both are genuinely usable for evaluation; neither free balance rolls over.

## Apify Web Scraper vs. a scraping API: which one fits your job

| Dimension | Apify (Web Scraper) | ScraperAPI |
| --- | --- | --- |
| Core model | Managed platform + actor marketplace | Single API endpoint |
| What you write | JavaScript `pageFunction` per site | One HTTP request |
| Billing basis | Compute units + proxy GB + storage ops, from prepaid credits | Per-request credits |
| JS-heavy pages | Native (real Chrome) | Supported via `render=true` |
| Pre-built scrapers | Large marketplace of community actors | Structured-data endpoints for major sites |
| Runs your own code | Yes, on Apify infrastructure | No — your code stays on your servers |
| Result storage | Platform datasets (storage billed) | Delivered to your webhook or database |
| Cheapest paid entry | $19/month | $49/month |
| Geotargeting | Country selection on all plans (proxy-billed) | Country-level from Business ($299); US/EU below |

Be honest with yourself about where you sit, because both products are good and they reward different people:

- **Choose Apify when** you want ready-made scrapers from the marketplace, you're building scrapers as products or long-running automations on a platform, or you need to execute custom code in the cloud. You'll trade some cost predictability for that flexibility.
- **Choose ScraperAPI when** you already have scraping code and your problem is blocking: your requests are dying on Cloudflare walls, your proxies keep getting banned, and you don't want to maintain a proxy pool. One API key replaces that entire layer, and the per-request pricing makes monthly spend trivial to forecast.

One more scenario worth naming: if your targets are mostly Google, Bing, or LinkedIn, ScraperAPI's 25–30 credits per request adds up fast — at those rates, compare carefully against Apify's SERP proxy pricing ($2.50 per 1,000 SERPs on lower tiers) before choosing either. But for plain pages and e-commerce endpoints, ScraperAPI's flat model usually wins on simplicity.

## Frequently asked questions

**Does Apify's Web Scraper require coding?**
Some. The browser handling, crawling, and output formats are handled for you, but extracting data means writing a `pageFunction` in JavaScript for each site's structure. If you want no-code scraping on Apify, you'd look at marketplace actors built by others instead.

**Is ScraperAPI's free plan enough for real testing?**
The 7-day trial gives 5,000 credits with no credit card, which is enough to test several target sites including some premium and rendered requests. After the trial, the permanent free plan continues with 1,000 credits and 5 concurrent connections, and support can grant extra testing credits if you ask.

**Do unused credits roll over on either service?**
No, on both. Apify explicitly expires unused credits at the end of each billing cycle, and ScraperAPI credits reset on renewal. ScraperAPI's workaround for annual plans: you can renew early to get a fresh batch of credits.

**What happens if I exceed my plan's limits?**
On ScraperAPI, over-concurrency returns a 429 error at no charge, and running out of credits pauses scraping or triggers auto-upgrade/pay-as-you-go depending on your tier and settings. On Apify paid plans, overage bills on your next invoice; the free plan locks until the next cycle.

**Can ScraperAPI work with AI tools and workflow automation?**
Yes. It ships an MCP server (compatible with Claude Code), plus integrations for LangChain, LlamaIndex, n8n, and Zapier. In 2026 it also acquired Traject Data, folding the Rainforest and SerpWow SERP and e-commerce APIs into the same credit system.

## Picking your plan before you click buy

If you're still on the fence between the two models, use this:

- Testing the waters with plain pages → ScraperAPI Free (1,000 credits) or the 7-day trial; you lose nothing either way.
- Steady project volume under ~80,000 standard pages a month → Hobby at $49, or $44.10/month billed annually.
- Production pipelines, hundreds of thousands of pages, or global targeting outside the US and EU → Startup ($149) at minimum, and Business ($299) if you need country-level geotargeting and unlimited analytics history.
- Multi-million-page operations → Scaling ($475) and up, where pay-as-you-go overage and the best per-credit rates kick in.
- Marketplace scrapers, custom cloud code, or Apify as your platform of choice → the Apify route genuinely fits; its Starter tier is $19/month and its free plan's $5 monthly credits are a real (if small) working budget.

Before checkout, do two things: run your top three target URLs through the Domain Cost Estimator so you know your true credits-per-page rate, and start on monthly billing — you can switch to annual for the 10% discount once you've confirmed the volume math. 👉 [Check the full plan lineup and start your trial](https://www.scraperapi.com/?fp_ref=coupons)
