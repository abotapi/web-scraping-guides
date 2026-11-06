# Football (soccer) statistics data

Football data comes in layers: results and fixtures, team and player season stats, and detailed event data (every pass and shot). Each layer has different sources, licences and costs. This guide maps them out and shows where scraping fits.

## Contents

- [The layers of football data](#the-layers-of-football-data)
- [What people build with it](#what-people-build-with-it)
- [Options](#options)
- [Python tools worth knowing](#python-tools-worth-knowing)
- [Legal and etiquette notes](#legal-and-etiquette-notes)
- [Example record shape (mock)](#example-record-shape-mock)
- [Analysis tips](#analysis-tips)

## The layers of football data

| Layer | Typical fields | Where it comes from |
| --- | --- | --- |
| **Results and fixtures** | date, competition, season, round, home and away team, score, venue | Free open datasets and APIs |
| **Standings and form** | position, points, goal difference, last five results | Derived from results, or read from live-score sites |
| **Team and player stats** | goals, assists, minutes, shots, cards, ratings, market value | Stats sites and commercial providers |
| **Event data** | every pass, shot, tackle with x/y location, timestamp, player, outcome | Commercial providers; a few free releases |
| **Tracking data** | positions of all 22 players and the ball many times a second | Clubs and leagues; almost never public |
| **Odds** | pre-match and closing prices per bookmaker | Historical CSVs and odds APIs |

Most hobby and business projects need the first three layers. Event data is what academic and club analytics are built on.

## What people build with it

- Match-prediction and expected-goals models
- Fantasy football helpers and team pickers
- Scouting shortlists: filter players by age, minutes and per-90 stats
- Live dashboards and bots for fan communities
- Betting research: compare model probabilities with historical odds
- Content: automated match previews and season reports

## Options

**Open data (free):**

- **[StatsBomb Open Data](https://github.com/hudl/open-data)** - Free event data (and some 360 freeze-frames) for selected competitions, such as several World Cups and women's leagues. The standard starting point for learning event-data analysis
- **[openfootball](https://github.com/openfootball)** - Public-domain fixtures and results for many leagues as plain text, with JSON exports in [football.json](https://github.com/openfootball/football.json)
- **[Football-Data.co.uk](https://www.football-data.co.uk/data.php)** - Historical results, match stats and bookmaker odds as CSV for European leagues going back decades
- **[transfermarkt-datasets](https://github.com/dcaribou/transfermarkt-datasets)** - Players, clubs, games and valuations, rebuilt regularly and published under CC0
- **[European Soccer Database](https://www.kaggle.com/datasets/hugomathien/soccer)** - A classic Kaggle SQLite file with matches, players and team attributes for 2008-2016. Old, but good for practice

**APIs:**

- **[football-data.org](https://www.football-data.org/)** - Fixtures, results, standings and squads over REST, with a free tier for major competitions
- Commercial data feeds (Opta/Stats Perform, Hudl StatsBomb, Sportradar, API-Football and others) - Event data, live data and wide coverage, licensed per competition. The right choice when you need guaranteed accuracy and rights

**Scraping:**

| Approach | Good for | Trade-off |
| --- | --- | --- |
| Your own scraper ([Crawlee](https://crawlee.dev/), Scrapy, Playwright) | Exactly the pages and fields you need | You maintain it, and stats sites change their front ends often |
| Python wrappers (see below) | Quick pulls from several sites into pandas | They break when a site changes; check each site's terms |
| A hosted scraper such as [SofaScore Scraper](https://apify.com/abotapi/sofascore-scraper?utm_source=github&utm_medium=web-scraping-guides&utm_campaign=content) ([code & samples](https://github.com/abotapi/sofascore-scraper)) | Live scores, fixtures, standings, team and player data across many sports, JSON/CSV export, no code | Pay per use |

## Python tools worth knowing

- **[soccerdata](https://github.com/probberechts/soccerdata)** - One interface to read several public football sites into pandas DataFrames, with local caching
- **[statsbombpy](https://github.com/hudl/statsbombpy)** - Load StatsBomb open data (and paid data, with credentials)
- **[kloppy](https://github.com/PySport/kloppy)** - Load event and tracking data from many providers into one standard model
- **[socceraction](https://github.com/ML-KULeuven/socceraction)** - Convert event data to SPADL actions and value them with VAEP or xT
- **[mplsoccer](https://github.com/andrewRowlinson/mplsoccer)** - Pitch plots, shot maps, pass networks and radar charts in Matplotlib

## Legal and etiquette notes

- This is not legal advice. Read each site's terms and robots.txt, and decide with that in mind.
- Facts like a final score are generally not owned by anyone, but compiled databases and detailed stats can be protected (for example by EU database rights), and many stats sites forbid automated access in their terms.
- League fixture lists are licensed in some countries (the English Premier League's are, for example). Publishing them commercially can need a licence even when collecting them does not.
- Open datasets come with their own licences. StatsBomb's open data asks for attribution; read the licence in each repo.
- Scrape modestly: cache responses, avoid live polling every second, and don't republish a site's data wholesale.

## Example record shape (mock)

A simplified match record with made-up values. Field names vary by source.

```json
{
  "id": 10000001,
  "sport": "football",
  "tournament": "Example Premier League",
  "season": "2025/2026",
  "round": 12,
  "startTimestamp": 1767268800,
  "status": "finished",
  "homeTeam": "Riverside FC",
  "awayTeam": "Northgate United",
  "homeScore": 2,
  "awayScore": 1,
  "venue": "Riverside Arena",
  "url": "https://example.com/match/10000001"
}
```

## Analysis tips

1. Pick one team ID scheme early. Names differ between sources ("Man Utd", "Manchester United"), so keep a mapping table when you join datasets.
2. Store timestamps in UTC and convert per league when displaying.
3. Compare players per 90 minutes, not on totals, and set a minimum-minutes cut-off.
4. For models, split training and test data by season, not randomly, to avoid leaking future form.
5. DuckDB reads the Football-Data.co.uk CSVs directly: `SELECT HomeTeam, AVG(FTHG) FROM 'E0.csv' GROUP BY 1`.
