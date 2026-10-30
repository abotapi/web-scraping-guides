# Marketplace price data for Brazil

Brazilian marketplaces publish prices, installment terms, shipping promises and seller badges on every listing. That makes them a good source for price monitoring, assortment research and competitor tracking. This guide covers what a typical listing contains, what people build with it, and the ways to collect it.

## Contents

- [What fields you can get](#what-fields-you-can-get)
- [What people build with it](#what-people-build-with-it)
- [Options](#options)
- [Legal and etiquette notes](#legal-and-etiquette-notes)
- [Example record shape (mock)](#example-record-shape-mock)
- [Analysis tips](#analysis-tips)

## What fields you can get

A search-result or category listing on a Brazilian marketplace typically carries:

- **The product**: title, listing ID, link, main image, sometimes brand and condition
- **The price**: current price in BRL, an installments line (for example "12x R$ 75,00 sem juros"), and sometimes a struck-through previous price
- **Fulfilment signals**: free-shipping flag, whether the item ships from the marketplace's own warehouse program, estimated delivery
- **Social proof**: star rating, review count, a "sold" counter shown as text ("+500 vendidos"), best-seller badge
- **The seller**: name, reputation level, official-store flag (detail pages)

Three fields need care. **Installments are marketing text, not a second price**: compare the cash price, and parse the installment count separately if you need it. **"Sold" counters are bucketed text** (hundreds, thousands), so treat them as ranges. **Prices change often during promotion events** such as Black Friday, so store a timestamp with every record.

## What people build with it

- **Price monitoring:** daily price per listing ID, alerts on drops
- **Assortment gaps:** which brands and models a competitor lists that you don't
- **Promotion tracking:** share of listings with free shipping or interest-free installments over time
- **Market sizing:** listing counts and price distributions per category
- **Repricing inputs:** the lowest few offers for a product you also sell

## Options

| Approach | Good for | Trade-off |
| --- | --- | --- |
| **Marketplace developer APIs** (for example the [Mercado Libre developers site](https://developers.mercadolibre.com/)) | Your own listings, orders and catalog data, with sanctioned access | Scope and rate limits are set by the provider, and public search coverage may be restricted |
| **[IBGE SIDRA](https://sidra.ibge.gov.br/)** (free) | Official inflation indices (IPCA) to deflate or compare your price series | Aggregates, not product-level |
| **Price-comparison sites** | Quick manual checks | No bulk export |
| **Your own scraper** ([Crawlee](https://crawlee.dev/), Scrapy, Playwright) | Full control | Marketplaces change markup and use bot protection, so expect maintenance |
| **A hosted scraper** such as [Mercado Livre Scraper](https://apify.com/abotapi/mercadolivre-com-br-scraper?utm_source=github&utm_medium=web-scraping-guides&utm_campaign=content) | JSON/CSV from search results, no code or a few lines of Python | Pay per use |

## Legal and etiquette notes

- Read the site's terms and robots.txt, and decide with that in mind. This is not legal advice.
- Collect public listing data only, at a modest rate.
- Seller names can be personal data under Brazil's LGPD. If you don't need them, don't store them.
- Keep the source URL and a timestamp on every record so you can attribute and re-check it.

## Example record shape (mock)

An illustrative, simplified shape with made-up values. Field names vary by source.

```json
{
  "itemId": "mlb90000001",
  "title": "Example Stainless Steel Water Bottle 750 ml",
  "url": "https://example.com/item/90000001",
  "imageUrl": "https://example.com/img/90000001.jpg",
  "price": 89.9,
  "currency": "BRL",
  "installmentsText": "12x R$ 7,49 sem juros",
  "freeShipping": true,
  "rating": 4.6,
  "bestSeller": false,
  "soldText": "+500 vendidos",
  "scrapedAt": "2026-01-15T10:00:00Z"
}
```

## Analysis tips

- Key everything on the listing ID, not the title, which sellers edit constantly.
- Compute a daily median per category and look at price changes against that, not against single listings.
- Deflate long series with IPCA before comparing months.
- Flag price jumps above 10x or below 0.1x of the previous value as probable parsing errors or variant swaps.
- Snapshot the raw text next to parsed numbers so you can audit the parsing later.

---

<sub>Some guides mention scrapers maintained by [abotapi](https://abotapi.com/?utm_source=github&utm_medium=web-scraping-guides&utm_campaign=content) as one option among several.</sub>
