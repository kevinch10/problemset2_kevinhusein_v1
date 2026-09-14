# prompts.md

## 1. First attempt — build the HDB Resale Price Explorer

> ROLE: You are a senior front-end developer building a React web app.
>
> GOAL: Build the front end of HDB Resale Price Explorer, a web product for homebuyers and renters in Singapore who are comparing where to live and want to understand how HDB resale prices differ across towns and flat types. Their job on this product is “help me compare HDB resale prices by town and flat type so I can understand what homes cost and how prices have changed over time.”
>
> Screens:
>
> Explore Prices
>
> Show a clear overview of HDB resale transactions.
>
> Let the user filter by town and flat type.
>
> Show key summary information for the selected filters:
>
> * median resale price
> * number of transactions
> * highest transaction price
> * lowest transaction price
>
> Show a simple price trend chart over time.
>
> Show a list of recent resale transactions with:
>
> * town
> * flat type
> * resale price
> * floor area
> * storey range
> * remaining lease
> * transaction month
>
> The user knows it worked when they can immediately see recent transactions for their selected town and flat type and understand whether prices have been rising, falling, or staying relatively stable.
>
> Compare Towns
>
> Let the user select up to three towns to compare.
>
> Show each town side by side using:
>
> * median resale price
> * average price per square metre
> * number of transactions
> * recent price change
>
> Include a simple comparison chart so differences are easy to understand.
>
> The user knows it worked when they can quickly see which selected town is cheaper, more expensive, or has experienced stronger price growth.
>
> Transaction Detail
>
> Open when the user selects a transaction from the Explore Prices screen.
>
> Show the selected transaction clearly with:
>
> * town
> * flat type
> * resale price
> * floor area
> * price per square metre
> * storey range
> * remaining lease
> * transaction month
>
> Also show several similar invented transactions in the same town and flat type for context.
>
> The user knows it worked when they can understand whether the selected transaction looks relatively cheap, typical, or expensive compared with similar flats.
>
> OUTPUT: A running app. Keep every invented value in ONE data file of its own, with at least 10 rows, so the screen looks real. One component per screen or section. Move between screens without reloading the page. Readable on a phone at arm's length. When you are done, list the files you created and what each one holds.
>
> GUARDRAILS: Screens and invented data only. Do NOT call the Gemini API or any other model. Do NOT call any outside service or fetch from any URL. No database, no login, no user accounts, no analytics. No features I did not list. No real company's name, logo, or trademark. Invented names and numbers only, nothing confidential.
>
> CONTEXT: Individual Problem Set 1 for MGMT 6110 Human-AI Collaboration at SMU. Built in Google AI Studio, shared as a link, and opened on a phone by classmates in Week 3. I am not a programmer: when you make a choice I did not specify, say so in one line rather than burying it.

Came back with: a working three-screen React interface and 50 invented HDB transactions. The agent also made choices I had not specified, including default comparison towns, a ±3.5% threshold for deciding whether a transaction was cheap or expensive, and a persistent “Selected Flat” navigation tab.

Action: kept the overall structure but continued refining the interface because some of the agent's choices were broader than what I actually wanted.

Lesson: the agent was fast at producing a complete interface, but anything I left undecided became a design decision it made for me.

---

## 2. The transaction list was too long

> Please cap the number of recent resale transactions to just 30 recorded flats and if there is more provide a next page button

Came back with: pagination capped at 30 transactions per page. The agent also added a Previous Page button, page counter, and automatic reset to page 1 when filters changed.

Action: kept the change.

Lesson: a short prompt was enough because I had already decided exactly what behaviour I wanted. The agent could handle the production work without needing another long specification.

---

## 3. I simplified Compare Towns after seeing the first version

> For the screen two "Compare Towns" remove the quick presets and for comparison just compare between two towns only

Came back with: the three-town comparison was replaced with a direct two-town comparison. The agent removed the quick presets and added “First Town” and “Second Town” dropdowns plus a Swap Towns button.

Action: kept the two-town version.

Lesson: my first prompt asked for too much because I had not yet seen the product. Once I could interact with the first version, I realised a two-town comparison was simpler and more useful.

---

## 4. The first dataset did not match the real product timeframe

> Change the date and user interface to everything from 2017 since we will be using "Every resale flat transaction since 2017 until present time"

Came back with: the interface and invented dataset were expanded to cover 2017 through the present, with a year filter, historical sorting, longer-term charts, and comparison metrics based on the 2017 baseline.

Action: kept the historical structure and continued refining the chart.

Lesson: I should have decided the real dataset's timeframe before asking the agent to create the first version. Changing it later required updates across multiple files instead of one.

---

## 5. I had to refine the price chart several times

> Re-edit the graph in screen one to yearly intervals on default instead of monthly, also add an option to view it per year, past three years, past five months

Came back with: a yearly default chart, Past 3 Years view and Past 5 Months view.

Action: changed my mind again because the time periods were not what I ultimately wanted.

I then sent:

> change it to 2017-2026, past year, past six months instead

Came back with: three chart options:

* 2017–2026
* Past Year
* Past Six Months

I then sent:

> Allow one more option to select the designated year lets say 2017, 2018, 2019, etc, create a drop down to select this

Came back with: a fourth Select Year option with a dropdown for 2017 through 2026 and a monthly breakdown for the selected year.

Action: kept the final version.

Lesson: this was time I partly caused myself to lose. The agent was not wrong; I was prompting before I had decided exactly what timeframes the chart should show. A more complete decision at the start would have avoided several rounds of edits.

---

## 6. I rewrote the master prompt to describe the website I had actually built

> ROLE: You are a senior front-end developer building a React web app.
>
> GOAL: Build the front end of HDB Resale Price Explorer, a web product for homebuyers and renters in Singapore who are comparing where to live and want to understand how HDB resale prices differ across towns and flat types. Their job on this product is “help me compare HDB resale prices by town and flat type so I can understand what homes cost and how prices have changed over time.”
>
> Screens:
>
> Explore Prices
>
> Show a clear overview of HDB resale transactions.
>
> Let the user filter by town and flat type.
>
> Show key summary information for the selected filters:
>
> * median resale price
> * number of transactions
> * highest transaction price
> * lowest transaction price
>
> Show a simple price trend chart over time.
>
> Show a list of recent resale transactions with:
>
> * town
> * flat type
> * resale price
> * floor area
> * storey range
> * remaining lease
> * transaction month
>
> Compare Towns
>
> Let the user select up to three towns to compare.
>
> Transaction Detail
>
> Open when the user selects a transaction from the Explore Prices screen.
>
> OUTPUT: A running app. Keep every invented value in ONE data file of its own, with at least 10 rows.
>
> GUARDRAILS: Screens and invented data only. Do NOT call the Gemini API or any other model. Do NOT call any outside service or fetch from any URL.
>
> CONTEXT: Individual Problem Set 1 for MGMT 6110 Human-AI Collaboration at SMU.

Came back with: a much more detailed master prompt describing the website as it existed at that point, including the final chart controls, mobile behaviour, metrics, and component structure.

Action: used that as the description of the existing front end before moving to live data.

Lesson: the prompt became much stronger after I had already built and tested the interface because I could describe actual decisions instead of hypothetical ones.

---

## 7. Replacing the invented data with the real data.gov.sg API

> ROLE: You are a senior full-stack developer working in my existing project. Do not rewrite what is already there; add to it.
>
> GOAL: My screen currently shows HDB resale prices, transaction counts, price trends, and transaction details as hard-coded values. Replace them with real data from data.gov.sg's HDB resale flat prices dataset, fetched through a serverless function of my own.
>
> 1. api/hdb.js—calls https://data.gov.sg/api/action/datastore_search?resource_id=d_8b84c4ee58e3cfc0ece0d773c8ca6abc, returns only the fields my screen needs, and nothing else.
>
> 2. api/health.js—reports whether data.gov.sg answered successfully and includes the HTTP status it returned. This API requires no credential or API key.
>
> 3. On the screen, replace the hard-coded values with the live ones, and decide what the user sees in each of these four cases: the data is loading, the data is empty, the upstream refused, and the upstream is unreachable. I want four different sentences, not one spinner.
>
> OUTPUT: Both functions at api/ in the PROJECT ROOT, siblings of package.json, never inside src/. If this project has a server entry file, register the same two routes there too, because that is the shape the preview can answer. If it has no server file, skip that and tell me so rather than inventing one.
>
> Make sure package.json contains "type": "module".
>
> This data.gov.sg endpoint requires no credential or signup, so do not add any environment variable or API-key check.
>
> AFTER the fetch, check response.ok before reading the body. A refusal often has an empty body, so calling .json() on it throws and my function dies with a 500 instead of telling me what happened.
>
> Cache the response for 24 hours with Cache-Control: s-maxage=86400, stale-while-revalidate=172800.
>
> In the footer, credit data.gov.sg as the source.
>
> For api/hdb.js, follow these data rules exactly:
>
> * Read transaction data from result.records.
> * Never use q for filtering.
> * Filter using the filters parameter with explicit field names.
> * URL-encode the filters JSON.
> * Use limit=10000.
> * Read result.total.
> * Paginate using offset in batches of 10000 when required.
> * Convert numeric fields before calculations.
> * Calculate price per sqm only after resale_price and floor_area_sqm have been converted.
> * Return only fields the existing UI needs.
>
> GUARDRAILS: Never create or use an API credential because this endpoint requires none. Never create a variable whose name starts with VITE_. Never call the upstream from browser code; every call happens inside api/. No new npm packages. No database, no login. Leave every screen I already have working exactly as it is.
>
> CONTEXT: Deployed on Vercel from GitHub. The data source requires no signup, no API key, and no Vercel environment variable.

Came back with: `api/hdb.js`, `api/health.js`, local Vite middleware, a frontend API service, four separate data-state messages, and the existing Explore Prices screen connected to the new `/api/hdb` route.

The implementation followed the important data rules: it used `result.records`, exact `filters` instead of `q`, `limit=10000`, `result.total`, pagination with `offset`, numeric conversion before calculations, and the required caching headers.

Action: kept this version because it replaced the invented data with the official data source while preserving the existing interface.

Lesson: this prompt worked much better than my earlier prompts because the important technical decisions were already made. I specified where the API files belonged, what the real response looked like, which field names to use, how filtering should work, what not to do, and how errors should appear to the user.

---

## 8. The important mistake I learned to guard against

Earlier in the process, using a query such as `q=TAMPINES` could appear to work because it returned real-looking HDB records, but it was a free-text search and silently under-returned the data.

Action: I changed the master prompt to explicitly say:

> Never use q for filtering.

and:

> Filter using the filters parameter with explicit field names, for example:
> filters={"town":"TAMPINES","flat_type":"4 ROOM"}.

I also required the code to read `result.total` and paginate until all matching records were collected.

Lesson: a successful HTTP response and believable-looking rows do not prove the data is complete. I had to understand the API's filtering behaviour and check the record count rather than judging the result from the interface alone.

---

## 9. Where I stopped prompting

Once the code had been generated, some deployment work was faster to do directly in GitHub and Vercel than by repeatedly describing dashboard actions to the agent.

The useful boundary for me became:

* use the agent for writing and modifying code;
* use the agent for diagnosing why an API route or deployment is failing;
* use the Vercel dashboard directly for simple project settings and deployment checks when they only take a few clicks.

Lesson: the agent saved the most time on code and unfamiliar technical implementation. It saved much less time on simple settings that were already visible in a dashboard.

