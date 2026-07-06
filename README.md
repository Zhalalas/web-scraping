# Azerbaijani Regulatory News Monitor

A web scraping tool that monitors official Azerbaijani government and financial sources for news and legal documents relevant to regulatory, financial, and legal compliance topics. Articles from the last 90 days are collected, filtered against curated keyword lists, and exported to Excel for review.

---

## Overview

Keeping track of new decrees, regulations, and official announcements across multiple government portals is time-consuming when done manually. This project automates that process: it crawls several official sources, parses Azerbaijani-language dates, matches each article's title and body against a curated list of financial and regulatory keywords, and saves the relevant results into structured Excel reports.

## Monitored Sources

| Source | Content | Output |
|---|---|---|
| president.az | Presidential decrees | `decrees.xlsx` |
| mia.gov.az | Ministry of Internal Affairs news archive | `mia_news.xlsx` |
| bfb.az | Baku Stock Exchange press releases | `news.xlsx` |
| fhn.gov.az | Ministry of Emergency Situations daily chronicle | `fhn_news.xlsx` |

## How It Works

### 1. Keyword Filtering

Two curated keyword lists drive the filtering, covering topics such as **insurance, taxation, banking, credit, investment, payment systems, mortgages, auditing, budgets, digital signatures, and financial monitoring** — all in Azerbaijani. A separate exclusion list (for example, meetings, trainings, and competitions) removes irrelevant matches and reduces noise.

Each article is checked in two places:

- **Title match** — keywords found in the headline
- **Body match** — keywords found in the full article text

An article is kept if either check produces a hit, and the matched keywords are recorded alongside it.

### 2. Azerbaijani Date Parsing

Official sources publish dates in Azerbaijani (for example, "15 yanvar 2026"). A custom month-name dictionary maps Azerbaijani month names to numbers, and dedicated parsing functions handle the different date formats used by each site.

### 3. Scraping Strategy

- **Pagination crawling** — pages are crawled one by one until content older than the 90-day cutoff is reached, then the crawler stops early to avoid unnecessary requests
- **Detail page fetching** — for each matching headline, the full article page is fetched and its body text extracted
- **Polite scraping** — randomized delays of 1 to 3 seconds between requests to avoid overloading the servers
- **Custom headers** — a browser User-Agent and referer are used where required (fhn.gov.az)

### 4. Output

Each Excel report contains the article title, full body text, publication date, keywords matched in the title, and keywords matched in the body — ready for manual review or further processing.

## Tech Stack

Python, requests, BeautifulSoup4, pandas, openpyxl

## Getting Started

```bash
git clone https://github.com/<your-username>/az-regulatory-news-monitor.git
cd az-regulatory-news-monitor

pip install requests beautifulsoup4 pandas openpyxl

jupyter notebook scraper.ipynb
```

Run the cells top to bottom. The keyword and month dictionaries are defined first, then each scraper cell targets one source and writes its own Excel file.

## Configuration

- **Time window** — controlled by the `cutoff` variable (default: last 90 days)
- **Keywords** — edit the `sozler2` list to change which topics are captured
- **Exclusions** — edit the `notsozler2` list to filter out unwanted matches

## Possible Improvements

- Merge all sources into a single deduplicated report
- Schedule automated runs with cron or GitHub Actions
- Send alerts for new matches via Telegram or email
- Replace substring matching with stemming or fuzzy matching for better Azerbaijani morphology handling
- Add retry logic and error handling for failed requests
