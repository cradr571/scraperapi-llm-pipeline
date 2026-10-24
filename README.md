# llm web scraping python: skip the proxy pain, feed clean pages to your LLM with ScraperAPI — real credit math and plan-picking rules included

When people search for LLM web scraping in Python, the prompt is rarely the hard part. You can write an extraction prompt in ten minutes. The other eighty percent of the work goes elsewhere: the target site bans your IP, a React page comes back empty, and the raw HTML is stuffed with nav bars and scripts that burn your token budget before the model ever sees the useful text. That's the real problem behind this search — you need a reliable fetching layer that returns clean markdown or structured JSON, so your Python code and your LLM can focus on extraction.

This article breaks the task down: the three layers of an LLM scraping pipeline, how ScraperAPI handles the messiest layer (with verified credit-cost rules), the full plan and pricing table, and several cost details most tutorials never mention.

> **Quick take**: most developers running Python + LLM scraping should start on Hobby ($49/mo, 100,000 credits, or $44.10/mo billed annually). Not sure yet? Test first — 1,000 free credits on signup, plus a 7-day trial with 5,000 credits, no credit card required. 👉 [Start the 7-day free trial (5,000 credits, no credit card)](https://www.scraperapi.com/?fp_ref=coupons)

## An LLM scraping pipeline has three layers — you only need to write two of them

A typical LLM scraping pipeline looks like: fetch → clean → extract with a model. Open-source stacks (silkweb, ScrapeGraphAI, or a DIY combo of Playwright plus an OpenAI key) hand you all three layers to assemble yourself: you manage proxies, run headless rendering, and wire up the LLM API. Their upside is real — everything runs locally, and ScrapeGraphAI works with OpenAI, Groq, Azure, Gemini, or local models via Ollama. The downside shows up the moment a target site gets defensive: proxy rotation and anti-bot bypass are exactly the problems that eat your maintenance time.

ScraperAPI takes over the fetching layer only. You send one request; it returns clean HTML, markdown, or structured JSON, with proxy rotation, CAPTCHA bypass, and JS rendering handled on its side. The extraction layer stays yours — your code, your LLM key. If you already write Python, this division of labor usually wins: you don't maintain a proxy pool to feed one data pipeline.

One detail worth knowing before you fetch anything: letting an LLM chew raw HTML is a token disaster. The official Python SDK supports `output_format="markdown"`, and the docs describe it as useful for LLM pipelines; there's also `output_format="text"`. Both collapse thousands of tokens of markup noise into the actual content. Community tutorials keep telling you to prune the DOM before feeding the model — this parameter makes that step unnecessary.

## Full plan comparison

All plans currently listed on the official pricing page, with verified monthly and annual (billed annually, 10% off) prices:

| Plan | Monthly | Annual (per mo) | Credits | Threads | Geotargeting | Buy |
| --- | --- | --- | --- | --- | --- | --- |
| Free | $0 | — | 1,000 | 5 | None | [ Sign up free](https://www.scraperapi.com/?fp_ref=coupons) |
| Hobby | $49/mo | $44.10 | 100,000 | 20 | US & EU only | [ Get Hobby](https://www.scraperapi.com/pricing/?fp_ref=coupons) |
| Startup | $149/mo | $134.10 | 1,000,000 | 50 | US & EU only | [ Get Startup](https://www.scraperapi.com/pricing/?fp_ref=coupons) |
| Business | $299/mo | $269.10 | 3,000,000 | 100 | Country-level | [ Get Business](https://www.scraperapi.com/pricing/?fp_ref=coupons) |
| Scaling | $475/mo | $427.50 | 5,000,000 | 200 | Country-level | [ Get Scaling](https://www.scraperapi.com/pricing/?fp_ref=coupons) |
| Professional | $975/mo | $877.50 | 10,500,000 | 300 | Country-level | [ Get Professional](https://www.scraperapi.com/pricing/?fp_ref=coupons) |
| Advanced | $1,975/mo | $1,777.50 | 21,500,000 | 500 | Country-level | [ Get Advanced](https://www.scraperapi.com/pricing/?fp_ref=coupons) |
| Enterprise | Custom quote | — | 22,000,000+ | 500+ | Country-level | [ Contact sales](https://www.scraperapi.com/?fp_ref=coupons) |

Every paid plan ships the same feature set: JS rendering, premium residential and mobile IPs, advanced bypassing, Parsing & Structured Data APIs, DataPipeline, full crawler access, and pay as you go — plus unlimited bandwidth, automatic retries, and a 99.9% uptime guarantee across the board. The plans differ only in credits, concurrency, and geotargeting precision. A Hobby user and an Enterprise customer call the same API; the difference is how hard and how much you can hit it.

There's also a 7-day free trial with 5,000 credits, no credit card needed. If you need more credits for testing, you can contact support and ask.

## The real credit math: one request does not always cost one credit

This is the part no plan page puts in its headline, and it's the single most useful thing to understand before picking a plan. The official FAQ puts it plainly: one credit is the cost of a standard page request, but what you actually consume depends on the site you're scraping and the parameters you send.

Base multipliers by domain:

- Standard sites: 1 credit
- Amazon, eBay, Walmart and other e-commerce pages: 5 credits
- Google and Bing (including their subdomains): 25 credits
- LinkedIn: 30 credits
- Sites behind Cloudflare, Datadome, or PerimeterX: +10 credits per request when the bypass triggers — automatically, with no way to opt out

Parameter surcharges: `render=true` (JS rendering) +10; `premium=true` +10; `ultra_premium=true` +30. And the combinations are non-linear: premium plus rendering actually costs 25 credits, not 20; ultra_premium plus rendering costs 75, not 40.

The good news for LLM users: several parameters are free. `country_code`, `session_number`, `device_type`, `output_format`, `wait_for_selector`, `keep_headers`, and `autoparse` all cost zero extra credits. Converting a page to markdown before it goes to your model costs nothing.

Here's what the math works out to. An April 2026 third-party breakdown converted the multipliers into real per-thousand-request prices:

| Plan | Standard (per 1k) | JS rendering | SERP pages | Ultra-premium + JS |
| --- | --- | --- | --- | --- |
| Hobby ($49) | $0.49 | $4.90 | $12.25 | $36.75 |
| Startup ($149) | $0.15 | $1.49 | $3.73 | $11.18 |
| Business ($299) | $0.10 | $1.00 | $2.49 | $7.48 |

That review's blunt conclusion: "A $49/month plan advertised as '100,000 credits' delivers only 1,333 actual requests when scraping protected sites with ultra-premium plus JavaScript rendering." In other words, 100,000 credits sounds generous until your target is both hard to scrape and JS-heavy — then Hobby is roughly 1,333 requests for your $49.

You have two official defenses. The Domain Cost Estimator in your dashboard shows the exact credit cost for any URL before you burn anything. And the `max_cost` parameter caps what a single request can consume. Wire both into your pipeline and billing surprises mostly disappear.

👉 [Check current plan prices and size your usage](https://www.scraperapi.com/pricing/?fp_ref=coupons)

## Python code: from install to LLM-ready data

The SDK takes two steps. Copy your API key from the dashboard into the `SCRAPERAPI_API_KEY` environment variable — the docs are explicit about not hardcoding it:

python
pip install scraperapi-sdk


python
import scraperapi_sdk
scraperapi_sdk.set_key('YOUR_API_KEY')

# markdown output, ready for an LLM pipeline
result = scraperapi_sdk.get(
    'https://example.com/product-page',
    payload={'output_format': 'markdown'}
)


The official docs map each situation to the right call:

1. **Single URL, need the result now** → synchronous `client.get()`, 1 credit for a standard request
2. **JS-heavy pages (React, Vue, Angular) or empty responses** → add `render=True` (10 credits)
3. **Standard proxies blocked** → add `premium=True` (10 credits); if that fails, `ultra_premium=True` (30 credits)
4. **Amazon, Google, Walmart, eBay, or Redfin** → use the Structured Data endpoint (`api.scraperapi.com/structured/<vertical>`) instead — it returns clean JSON, usually costs fewer credits than `render=True` raw scraping, and requires zero parsing code
5. **20+ URLs** → switch to the async endpoint at `async.scraperapi.com/jobs`

Point 3 is your main money lever. The official recommended escalation ladder is: standard (1 cr) → render (10 cr) → premium (10 cr) → premium+render (25 cr) → ultra_premium (30 cr). Start cheap, escalate only when a target blocks you, and record which tier each domain needs so you skip the trial-and-error next time. Most credit overruns trace back to blanket `render=True` on everything.

Point 5 has a trap worth flagging: the synchronous SDK blocks on every call, up to 70 seconds per URL. For batches, the docs recommend hitting the async endpoint with `requests` directly — submit jobs in the background, then poll the `statusUrl`, or set up a webhook callback and skip polling entirely. Very large batches (up to 50,000 URLs) go to `async.scraperapi.com/batchjobs` with a URL array.

Error handling follows the status codes: 401 means the key is invalid — don't retry with the same key; 403 means the target blocked you — escalate the proxy tier or change `country_code`; 429 means you hit the concurrency cap — back off or go async; 500/503 are transient — retry with exponential backoff, 3–5 attempts.

A few parameter gotchas the docs mention in passing but that will save you a debugging session: `premium` and `ultra_premium` are mutually exclusive (never set both); `ultra_premium` is incompatible with custom headers, so `keep_headers` gets ignored; `wait_for_selector` only works alongside `render=True`; and sticky sessions via `session_number` expire 15 minutes after their last use, which matters for multi-step flows like pagination or login states.

## Three hard rules hidden in the fine print

The pricing page FAQ contains three rules that directly affect which plan makes sense — the kind of thing most reviews summarize in one line or skip:

1. **Credits don't roll over.** The FAQ states they expire at the end of each billing cycle. Unused credits are wasted, so buying "one tier up for safety" is usually the wrong move — size to your real usage.
2. **Pay-as-you-go is only available on Scaling ($475/mo) and above.** On Hobby, Startup, or Business, exhausting your credits means scraping pauses until the next billing period. Your only in-month options are upgrading or enabling auto-upgrade. If your usage swings wildly month to month, this matters more than per-credit price.
3. **Auto-upgrade is opt-in**, available from Hobby through Professional. Turn it on and your account moves to the next plan automatically when credits run out; leave it off and you wait for renewal. Annual plans can also renew early for a fresh batch of credits.

> Heads-up: exceeding your concurrency limit returns a 429 and the request is rejected — not queued, not billed. Set your concurrency in the scheduler rather than discovering the cap through timeouts.

Putting it into decision rules:

- A few hundred standard requests a week, easy targets → start on Free (1,000 credits), move to Hobby when you outgrow it
- Heavy SERP scraping (Google/Bing) or render + premium combos → at least Startup. On Hobby, SERP pages cost $12.25 per thousand; Startup drops that to $3.73
- Volatile usage you don't want interrupted → Scaling or above, where PAYG and bigger credit buffers live
- Mostly Amazon/e-commerce price data at volume → Business or higher, where e-commerce pages run $0.50 per thousand

One more cost trap worth knowing: DataPipeline, the scheduled-scraping feature in the dashboard, bills at higher multipliers than the standard API — a basic normal request costs 6 credits through DataPipeline versus 1 through the API, and e-commerce goes from 5 to 10. That ratio narrows on expensive request types (SERP: 30 vs 25), but if you assume pipeline jobs price like API calls, a no-code pipeline will quietly burn six times the credits on simple requests. It's documented, but you have to dig for it.

Also in your favor: ScraperAPI only charges for successful requests (HTTP 200 and 404 count; failures on hard targets don't), which takes some sting out of the retry-heavy early days of a new target.

## When an open-source stack is the better call

To be fair: not every project needs a paid API. If your targets have no anti-bot protection and no JS rendering, `requests` plus BeautifulSoup plus one LLM call is the whole program — zero monthly cost beyond the LLM API itself, and nothing at all if you run a local model. ScrapeGraphAI's pitch is packaging fetch-plus-extract into graph workflows, and its local-model support (Ollama) is genuinely useful if you don't want page content leaving your machine.

The dividing line is target-site defensiveness. Sites behind Cloudflare or Datadome, or anything needing residential IP rotation at volume, make the hidden costs of self-hosting — proxy subscriptions, ban handling, a rendering fleet — climb past the API's monthly fee fast. ScraperAPI publishes a network of 40M+ IPs across 50+ countries processing 36 billion API requests a month; that's infrastructure you don't rebuild for one project.

On user sentiment, a 2026 review aggregating G2, Capterra, and Reddit gives a two-sided picture. Praise clusters around fast setup — "Super easy to set up. You can start scraping in minutes," per one community post, and Capterra rates ease of use 4.9/5 — and reliable performance on popular targets like Amazon and Google. Complaints cluster around credit-cost confusion ("Breakdown of credit costs can be confusing" — a founder's February 2025 Capterra review) and shakier reliability on hard targets, with one Reddit user citing an 80% failure rate on some sites. That picture is consistent with the cost rules above: easy targets are a smooth ride; hard ones demand you watch credits closely.

## Letting AI agents call the scraper directly

If your llm web scraping project is an agent rather than a batch script, ScraperAPI ships two official integrations:

**LlamaIndex.** Install one package, pass `ScraperAPIToolSpec` your API key, and attach it to a `FunctionAgent` — the agent decides when to fetch which URL. The official framing is worth quoting in spirit: search APIs return snippets and extractors return pages your LLM must parse, while this integration hands agents structured JSON from Amazon, Google, eBay, Walmart, and Redfin, plus raw scraping of any URL, in one pip install.

**MCP server.** For Claude Code and other MCP-capable tools. The remote server (recommended, no installation) exposes 22 tools over a single connection — scrape, Google, Amazon, Walmart, eBay, Redfin, and crawler jobs — connected via `npx mcp-remote` with a Bearer token header. A local variant (`pip install scraperapi-mcp-server`, Python 3.11+) exists, but it exposes only the `scrape` tool; to get structured Google results through it, you request a Google URL with `autoparse: true` and `outputFormat: "json"`.

One agent-specific tip from the docs: for supported platforms, prefer structured data endpoints over raw HTML scraping — the JSON is clean, more reliable, and often costs fewer credits than `render=true`. When ordering an agent's tool list, put the platform-specific extractors before the generic scraper; it's a free cost optimization.

## FAQ

**Which Python versions does ScraperAPI support?**
The official SDK (`scraperapi-sdk` package) requires Python 3.7+; the local MCP server variant requires Python 3.11+.

**How does the free tier work?**
You get 1,000 free credits on signup (capped at 5 concurrent connections), plus a 7-day trial with 5,000 credits, no credit card required. Contact support if you need more credits for testing.

**How many credits does one Google search cost?**
25 — that's the fixed multiplier for Google, Bing, and their subdomains. Amazon costs 5, LinkedIn 30, and standard pages 1. Check any URL's exact cost with the Domain Cost Estimator in your dashboard.

**What's the difference between `output_format` and `autoparse`?**
`output_format="markdown"` returns the full page as markdown for your LLM to parse; `autoparse=True` has ScraperAPI extract fields like title, price, and reviews into structured JSON for supported platforms — at no extra credit cost.

**What happens when I run out of credits?**
Scraping pauses until the next billing period by default. Hobby through Professional can enable auto-upgrade to the next plan; Scaling and above can use pay-as-you-go at a fixed per-credit rate up to a monthly cap you set.

## Before you buy, check two things

First, look up your actual target domains in the dashboard's Domain Cost Estimator — the same plan can deliver 25 times fewer requests depending on what you're scraping, and that number alone decides Hobby versus Startup. Second, check your usage curve: if any month could spike, either enable auto-upgrade or start on Scaling for PAYG access, so a heavy week doesn't halt your pipeline mid-run.

Mostly standard requests on forgiving sites → try Hobby monthly for one cycle, then switch to annual at $44.10/mo. SERP-heavy or heavily protected targets → start at Startup. High and fluctuating volume → Scaling. 👉 [Claim your 1,000 free credits and start testing](https://www.scraperapi.com/?fp_ref=coupons)
