# assessment.md

## What “good” means for my product

My product is for a homebuyer or renter in Singapore who wants to compare HDB resale prices by town and flat type and understand how prices have changed since 2017 until present time.

The criteria that I have thought of are as listed below.

## Front-end criteria

### 1. A new user can understand the purpose immediately

**Why it matters:** A homebuyer should not have to work out what the website does before using it.

**How to test it:** Open the website for the first time. Within five seconds, another person should be able to say that it is for exploring and comparing HDB resale prices in Singapore.

**My result: Met.**

**Evidence:** The page is clearly labelled “HDB Resale Price Explorer”, identifies the data as resale transactions from 2017 to the present, and immediately presents price filters and transaction information.

---

### 2. The main job can be completed without instructions

**Why it matters:** The main reason someone uses the product is to find out what flats cost in a particular town and flat type.

**How to test it:** Give a first-time user the task “Find the resale prices of 4-room flats in Tampines.” They should be able to select Tampines and 4 Room and reach the relevant prices without being told where to click.

**My result: Partly met.**

**Evidence:** The interface has clear town and flat-type controls and shows median, highest, lowest and transaction-level prices. However, the final live deployment still had a backend routing problem, so the interface can only complete the full job when `/api/hdb` is successfully deployed.

---

### 3. The price information is understandable, not just a list of numbers

**Why it matters:** A buyer needs context to understand whether a price is high, low or changing over time.

**How to test it:** Select a town and flat type. The screen should show the median price, highest and lowest prices, number of transactions and a price trend without requiring the user to calculate these themselves.

**My result: Met.**

**Evidence:** The Explore Prices screen contains summary measures and a price trend chart. The chart can show the long-term 2017–2026 trend, the past year, the past six months or a selected individual year.

---

### 4. Comparing two towns is simple enough to use without explanation

**Why it matters:** A buyer may be deciding between two locations rather than analysing the whole HDB market.

**How to test it:** Ask another person to compare Tampines with Punggol. They should be able to select the two towns and identify which has the lower median resale price without instructions.

**My result: Met.**

**Evidence:** The final version uses two dedicated town dropdowns rather than the earlier three-town design. It presents the towns side by side with median price, price per square metre, transaction count and price growth, followed by a direct comparison statement.

---

### 5. The user has a clear response when something goes wrong

**Why it matters:** A blank screen could make the user think there are no HDB transactions when the real problem is that the data service failed.

**How to test it:** Trigger loading, no-results, refused-request and unreachable-service conditions. Each one should produce a different sentence explaining what happened.

**My result: Met in the front-end code, but only partly met in production.**

**Evidence:** I created separate states for loading, empty results, an upstream refusal and an unreachable service. 

---

## Back-end criteria

### 1. The API must return the complete matching dataset, not merely believable results

**Why it matters:** A buyer could reach the wrong conclusion if the product silently analyses only part of the available HDB transactions.

**How to test it:** Request a known town such as Tampines. Compare the number of records returned by my API with `result.total` from data.gov.sg. They should agree after pagination.

**My result: Met in the code.**

**Evidence:** The backend uses the explicit `filters` parameter instead of the free-text `q` search, reads `result.total`, uses a limit of 10,000 and paginates with `offset` when more records exist. This was important because an earlier `q=TAMPINES` approach could return real-looking but incomplete results.

---

### 2. Numbers must be numbers before the product calculates with them

**Why it matters:** Prices received as strings can produce incorrect sorting or calculations even though the values look normal on screen.

**How to test it:** Inspect `api/hdb.js`. `resale_price`, `floor_area_sqm` and `lease_commence_date` should be converted with `Number(...)` before sorting or calculations, and price per square metre should be calculated only after those conversions.

**My result: Met.**

**Evidence:** The backend converts the numeric fields immediately after receiving the records and then calculates `price_per_sqm`. It does not add or sort resale prices while they are still strings.

---

### 3. Another person must be able to check whether the upstream service is working

**Why it matters:** If the product fails, somebody other than me should be able to separate “data.gov.sg is down” from “my application is broken.”

**How to test it:** Open `/api/health` on the deployed website. It should return JSON stating whether data.gov.sg answered and what HTTP status it returned.

**My result: Met.**

**Evidence:** I created `api/health.js`, and opening `/api/health` on the Vercel deployment was successful.

---

### 4. The application should not call the source more often than necessary

**Why it matters:** The HDB dataset does not need to be downloaded again every time somebody changes screens or refreshes the page.

**How to test it:** Inspect the API response headers. Successful responses should contain `Cache-Control: s-maxage=86400, stale-while-revalidate=172800`.

**My result: Met in the code.**

**Evidence:** Both API functions set a 24-hour shared cache with a stale-while-revalidate period. This matches the product's need for relatively current transaction data without repeatedly requesting the same dataset.

---

### 5. Failure must be distinguished from an empty result

**Why it matters:** “There are no matching flats” and “the data source failed” are completely different facts for a homebuyer.

**How to test it:** Test three cases: a valid search with zero records, a non-2xx response from data.gov.sg, and a network failure. The API should return different statuses and explanations for each.

**My result: Met in the API design, partly met end-to-end.**

**Evidence:** The backend checks `response.ok` before trying to read the response body and distinguishes refused, unreachable and empty states. The frontend also has different messages for them. However, these responses can only reach the user once Vercel successfully deploys the API routes.

---

## Overall assessment

The strongest part of my product is the front-end experience. The job is narrow and visible: choose a town and flat type, examine actual transactions and price trends, or compare two towns directly. I also improved the product by simplifying features that became too complicated during development such as comparing between two instead of three towns, and also optimising the graph so that it is easily readable for the user.

The strongest part of the backend code is data correctness. I learned not to treat a successful response as proof that the data was complete. Exact field filters, `result.total`, pagination and numeric conversion are now explicit parts of the implementation.

The main weakness is deployment rather than the intended application logic. During the process, the Vercel URL returned `404 NOT_FOUND` for `/api/health`, but it was resolved using the help of AI and making some tweaks.

## Q1. Where did the agent make me faster, and by how much?

The agent made me much faster when I had to build the website and write code that I did not really know how to write by myself yet. For example, my first prompt created most of the React front end in only a few minutes. It created the different screens, the filters, the charts and the transaction list. Later, it also helped me create the `api/hdb.js` and `api/health.js` files and connect the website to data.gov.sg.

If I had to do all of this by myself, I think it would have taken me many hours, or even a full day, because I am still learning coding and I would have needed to search for tutorials for almost every step. This was not only work that I could do slowly. Some of it was work that I did not know how to do at all before this project.

The time I saved was useful because I could spend more time thinking about what I actually wanted the product to do. For example, I decided to change Compare Towns from three towns to two towns, limit the transaction list to 30 flats per page, and change the different time periods on the price chart.

There were also some things that were faster to do myself. Vercel settings were one example. Sometimes it was easier for me to click through the Vercel dashboard directly instead of having a long conversation about where the setting was.

## Q2. Where did it cost me time, and whose fault was that?

The biggest place where I lost time was changing the price trend chart several times. At first, I asked for yearly intervals, the past three years and the past five months. After the agent built that, I changed my mind and asked for 2017–2026, the past year and the past six months. After that, I added another option to select one specific year.

I do not think this was really the agent's fault because it followed what I asked for each time. The problem was that I started giving instructions before I had completely decided what I wanted. I had a similar experience with Compare Towns. My first version allowed three towns, but after seeing it, I realised that comparing only two towns would be simpler for the user.

This taught me that a good prompt is not only about explaining things clearly. I also need to decide what I actually want before asking the agent to build it. If my own decision is unfinished, even a good agent can still produce something that I later need to redo.

## Q3. Did it ever hand me something that looked right and was not?

Yes. The most important example was how the HDB data was filtered. At one point, using `q=TAMPINES` seemed to work because the API returned real Tampines transactions. The results looked believable, so at first I did not think there was a problem.

Later, I checked the API more carefully and realised that `q` was doing a free-text search instead of filtering the actual `town` field. This meant it was returning fewer records than it should. That was a useful lesson for me because the mistake was not obvious. The numbers and transactions looked realistic, so I could easily have accepted them.

After discovering this, I changed my prompt so that the agent had to use the `filters` parameter with the exact field names, read `result.total`, and paginate if there were more than 10,000 results. This showed me that something can look correct on the screen and still be technically wrong underneath.

## Q4. What did I have to know in order to supervise it?

To catch the filtering problem, I had to understand a little bit about how the API worked. I needed to know that getting a successful response does not automatically mean that all the correct data was returned.

I also had to learn the difference between a free-text search using `q` and an exact field filter using `filters`.

Another thing I learned was that some values from an API can come back as text instead of numbers. For example, `resale_price` needed to be converted into a number before the program used it for calculations or sorting.

The mistake I did not catch early enough was the Vercel deployment problem. I saw that the API files were in GitHub and that the preview worked, so I assumed the backend was working. Later, when I opened `/api/health` on the live Vercel website, I got a `404_NOT_FOUND` which was able to be resolved.

I learned that checking the code is not enough. I also need to test the real deployed website. Because I am still learning, I think the most important skill for me is knowing what I need to check instead of assuming the agent is correct just because the answer looks professional.

## Q5. Which decisions did I keep, and should I have kept more or fewer?

I kept most of the important product decisions myself.

I decided that the website was for homebuyers and renters who want to compare HDB resale prices. I decided what the main screens should be, changed Compare Towns from three towns to two, limited the transaction list to 30 results per page, and chose the different time periods for the price chart.

I also chose data.gov.sg as the real data source and decided that the API call should happen through a serverless function instead of directly from the browser. Some technical decisions were better left to the agent because I did not know enough about React or serverless functions to make every small coding decision myself. For example, I did not need to decide exactly how every React state variable or helper function should be written.

However, I also noticed that the agent sometimes made product decisions without me noticing. In the first version, it created its own percentage threshold for deciding whether a flat was cheap, typical or expensive. I had not asked for that.

The four messages for loading, empty data, refused requests and unreachable services are another example. I asked for four different messages, but I mostly accepted the wording the agent created.

Looking back, I think I should let the agent handle more of the coding work, but I should keep more control over decisions that affect what the user sees, believes or understands.

## Q6. Now scale it up: what does this mean for a team of thirty?

If thirty people in a company were using AI agents like this, I think there would need to be a clear review process before anything went live.

I would not expect a human to check every single line of AI-generated code, but I would require people to check the important parts, especially data sources, calculations, customer-facing statements, security and error handling.

I would also create a common checklist for everyone. For example, teams should check that field names match the real API, record counts make sense, secrets are not exposed, errors are handled clearly, and the actual production website has been tested.

I would also require one review before deployment and another quick check after deployment. My own project showed me why this matters because the code looked correct in GitHub, but `/api/health` still did not work on the live Vercel site.

The main thing I would not allow the agent to decide by itself is what a number means or what claim we make to the user. Those decisions should still belong to people because the agent can create something that looks convincing even when it is wrong.

As someone who is still learning AI and coding, this project made me realise that using AI does not remove the need to understand the work. It actually makes checking and judgement more important.


