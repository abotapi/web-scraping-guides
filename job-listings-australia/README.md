# Job listings data for Australia and New Zealand

Job ads are one of the best public signals of what employers are paying and hiring for. This guide covers what a typical job-board record contains, how people use it, and the ways to collect it.

## Contents

- [What fields you can get](#what-fields-you-can-get)
- [What people build with it](#what-people-build-with-it)
- [Options](#options)
- [Legal and etiquette notes](#legal-and-etiquette-notes)
- [Example record shape (mock)](#example-record-shape-mock)
- [Analysis tips](#analysis-tips)

## What fields you can get

A listing page typically carries:

- **The job**: title, short teaser, full description, bullet points, classification (industry category), work type (full time, contract), work arrangement (on-site, hybrid, remote)
- **Pay**: a salary label, which is free text such as a range or "plus super", and sometimes a structured min/max
- **Place**: suburb or city, state or region, country
- **The employer**: name, profile link, logo, and sometimes size, industry and website
- **Timing**: listing date and any closing date

Salary is the field to treat carefully. Many ads leave it blank, and the rest mix hourly, daily and annual figures. Store the raw label and parse it separately.

## What people build with it

- Salary benchmarks by role and city
- Hiring-demand trends: count new ads per classification per week
- Skills tracking: which tools appear in descriptions over time
- Lead lists of employers that are actively hiring
- Alerts for niche roles

## Options

| Approach | Good for | Trade-off |
| --- | --- | --- |
| Official feeds and partner APIs | Stable, permitted use | Usually limited to partners or paying customers |
| Government open data (for example [data.gov.au](https://data.gov.au/) and labour-market statistics) | Aggregate trends | No individual ads, delayed |
| Your own scraper ([Crawlee](https://crawlee.dev/), Scrapy, Playwright) | Full control | You maintain it when the site changes |
| A hosted scraper such as [SEEK Jobs Scraper](https://apify.com/abotapi/seek-scraper?utm_source=github&utm_medium=web-scraping-guides&utm_campaign=content) | Fast start, JSON/CSV export, no code or a few lines of Python | Pay per use |

## Legal and etiquette notes

- Read the site's terms and robots.txt, and decide with that in mind. This is not legal advice.
- Collect public listing data only, at a modest rate.
- Ads can name individuals (a recruiter, a contact person). Under Australia's Privacy Act and similar rules, don't build profiles of people from them.
- Keep the source link on every record so you can attribute and re-check it.

## Example record shape (mock)

An illustrative, simplified shape with made-up values. Field names vary by source.

```json
{
  "id": "10000001",
  "title": "Example Data Analyst",
  "companyName": "Example Pty Ltd",
  "location": "Example City NSW",
  "workType": "Full time",
  "salary": "$100,000 - $120,000 plus super",
  "listingDate": "2026-01-01T00:00:00Z",
  "url": "https://example.com/job/10000001"
}
```

## Analysis tips

1. De-duplicate on the listing ID; the same role is often re-posted.
2. Normalise pay to annual figures before comparing, and keep the original text.
3. Snapshot weekly. Trends come from comparing snapshots, not from one pull.
4. Load into DuckDB or pandas, group by classification and week, and chart the counts.
