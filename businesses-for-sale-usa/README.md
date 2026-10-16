# Businesses-for-sale data for the United States

Small-business listings are the closest thing to a public price list for private companies. Each one pairs an asking price with the seller's own financials. This guide covers what a typical listing contains, how searchers, brokers and lenders use it, and the ways to collect it.

## Contents

- [What fields you can get](#what-fields-you-can-get)
- [What people build with it](#what-people-build-with-it)
- [Options](#options)
- [Legal and etiquette notes](#legal-and-etiquette-notes)
- [Example record shape (mock)](#example-record-shape-mock)
- [Analysis tips](#analysis-tips)

## What fields you can get

A US business-for-sale listing typically carries:

- **The business**: headline, industry category, city and state (often only the county or state, to keep the sale confidential), year established, number of employees, home-based / relocatable / franchise flags
- **The ask**: asking price, and whether real estate, inventory or FF&E (furniture, fixtures and equipment) are included or extra
- **Seller-reported financials**: cash flow, usually SDE (seller's discretionary earnings), gross revenue, sometimes EBITDA
- **The deal story**: description, reason for selling, training and support offered, financing (for example "SBA pre-qualified"), lease, facilities, competition, growth ideas
- **The broker**: name, brokerage, phone and profile page
- **Listing signals**: newly added, recently updated, price reduced

Two fields need care. **Financials are self-reported and often "Not Disclosed"**, so keep the raw text next to the parsed number. **Cash flow (SDE) is not EBITDA**: SDE adds back one owner's salary, so SDE multiples look lower than EBITDA multiples for the same business.

## What people build with it

- **Deal flow for search funds and first-time buyers:** a weekly list of new listings that fit a buy box (industry, state, price, SDE)
- **Valuation comps:** median asking price / SDE by industry, state and size band
- **Broker intelligence:** listing volume, price cuts and time on market per brokerage
- **Lending pipelines:** deals likely to need SBA 7(a) acquisition loans
- **Market research:** which industries have the most owners selling, for example as owners retire

## Options

| Approach | Good for | Trade-off |
| --- | --- | --- |
| **[SBA 7(a) & 504 loan data](https://data.sba.gov/dataset/7a-504-foia)** (FOIA, free) | Real financed deals: loan size, industry (NAICS), state, lender | Loans, not listings or prices; change-of-ownership loans are mixed in with other uses |
| **[Census County Business Patterns](https://www.census.gov/programs-surveys/cbp.html)** and **[Statistics of US Businesses](https://www.census.gov/programs-surveys/susb.html)** | How many businesses of each industry and size exist where | Counts only, about two years behind |
| **[Census Annual Business Survey](https://www.census.gov/programs-surveys/abs.html)** | Owner demographics, including owner age | Survey aggregates |
| **Marketplace reports** (BizBuySell's quarterly Insight Report, broker associations such as the IBBA) | Headline multiples and sold-deal trends | Summary figures, no records |
| **Online-business marketplaces** ([Acquire.com](https://acquire.com/), Flippa) | SaaS, e-commerce and content sites | Different shape: MRR, traffic and churn instead of SDE |
| **Your own scraper** ([Crawlee](https://crawlee.dev/), Scrapy, Playwright) | Full control | Listing sites use bot protection, and you maintain it when they change |
| **A hosted scraper** such as [BizBuySell Scraper](https://apify.com/abotapi/bizbuysell-scraper?utm_source=github&utm_medium=web-scraping-guides&utm_campaign=content) ([examples](https://github.com/abotapi/bizbuysell-scraper)) | JSON/CSV with financials and broker contact, no code or a few lines of Python | Pay per use |

For franchises, the franchisor's Franchise Disclosure Document (FDD) is the primary source for fees and, in Item 19, unit financials. Several US states publish FDDs filed with them.

## Legal and etiquette notes

- Read the site's terms and robots.txt, and decide with that in mind. This is not legal advice.
- Collect public listing data only, at a modest rate.
- Broker names and phone numbers are published as business contacts for buyers. If you contact them, follow anti-spam rules (CAN-SPAM, TCPA for calls and texts) and stop when asked.
- Listings are often anonymized on purpose. Don't try to identify a confidential seller.
- Keep the source URL on every record so you can attribute and re-check it.

## Example record shape (mock)

An illustrative, simplified shape with made-up values. Field names vary by source.

```json
{
  "id": "9000001",
  "title": "Example Neighborhood Coffee Roastery",
  "category": "Restaurants & Food",
  "location": "Example City, OR",
  "askingPrice": 485000,
  "cashFlow": 132000,
  "grossRevenue": 910000,
  "establishedYear": 2014,
  "financingAvailable": "SBA pre-qualified",
  "realEstateIncluded": false,
  "priceReduced": false,
  "brokerName": "Jordan Example",
  "detailsRaw": { "ebitda": "Not Disclosed" },
  "url": "https://example.com/business-opportunity/9000001/"
}
```

## Analysis tips

1. De-duplicate on the listing ID. Brokers often re-post the same business under a new headline, so also check title + city + asking price.
2. Compute the multiple as asking price / cash flow only where both are disclosed, and report the median, not the mean: a few outliers distort averages.
3. Separate asset sales and start-ups from established businesses before comparing multiples.
4. Snapshot weekly. Price cuts and listings that disappear (likely sold or withdrawn) only show up across snapshots.
5. Load into DuckDB or pandas, group by category and state, and chart listing counts and median multiples over time.
