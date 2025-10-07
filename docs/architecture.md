# Sieve Architecture

## System Overview

Sieve is an event-driven data pipeline that ingests, filters, and serves job postings and content using LLM-based filtering.

```
┌─────────────────┐
│ Data Sources    │
│ - Job Boards    │
│ - RSS Feeds     │
│ - YouTube       │
└────────┬────────┘
         │
         ▼
┌─────────────────┐      ┌──────────────┐      ┌─────────────┐
│ Python Scrapers │─────▶│    Kafka     │─────▶│   Kotlin    │
│   (Producers)   │      │   Topics     │      │  Consumers  │
└─────────────────┘      └──────────────┘      └──────┬──────┘
                                                       │
                         ┌─────────────────────────────┘
                         │
                         ▼
                    ┌─────────┐
                    │   LLM   │
                    │ Filter  │
                    └────┬────┘
                         │
                         ▼
                  ┌─────────────┐
                  │ PostgreSQL  │
                  └──────┬──────┘
                         │
                         ▼
                  ┌─────────────┐
                  │  REST API   │
                  │  (Kotlin)   │
                  └─────────────┘
```

## Components

### 1. Python Scrapers (Producers)
- **Purpose**: Fetch content from external sources
- **Technology**: Python, BeautifulSoup, Scrapy, youtube-transcript-api
- **Output**: Publishes raw content to Kafka `raw_feeds` topic

**Scrapers**:
- RemoteOK job scraper
- We Work Remotely scraper
- RSS feed fetcher
- YouTube transcript fetcher

### 2. Kafka Message Bus
- **Topics**:
  - `raw_feeds`: Raw scraped content
  - `parsed_items`: Normalized, deduplicated items
  - `filtered_items`: LLM-filtered results

### 3. Kotlin Pipeline (Consumers)
- **RSS Parser Consumer**: Consumes `raw_feeds`, normalizes data, saves to `raw_items` table
- **LLM Filter Consumer**: Consumes `parsed_items`, applies LLM filtering, saves to `filtered_items` table

### 4. PostgreSQL Database
**Tables**:
- `sources`: Data source configurations
- `filters`: LLM filter prompts per source
- `raw_items`: All ingested items (deduplicated)
- `filtered_items`: Items that passed LLM filters

### 5. REST API (Kotlin)
- **Framework**: Spring Boot or Ktor
- **Endpoints**:
  - `GET /jobs`: List filtered jobs
  - `GET /jobs/{id}`: Job details
  - `POST /sources`: Add new source
  - `GET /feed/{filter_id}/rss`: RSS output

## Data Flow

1. **Ingestion**: Python scrapers fetch content → publish to Kafka `raw_feeds`
2. **Parsing**: Kotlin consumer reads `raw_feeds` → normalizes → saves to DB → publishes to `parsed_items`
3. **Filtering**: Kotlin consumer reads `parsed_items` → LLM filtering → saves matches to DB → publishes to `filtered_items`
4. **Serving**: API reads from `filtered_items` table → serves via REST/RSS

## Scaling Considerations

- **Kafka partitioning**: Partition by source_id for parallel processing
- **Consumer groups**: Multiple consumer instances for high throughput
- **Database indexing**: Index on `source_id`, `published_at`, `guid`
- **LLM rate limiting**: Batch requests, implement exponential backoff

## Technology Choices

See [DECISIONS.md](../DECISIONS.md) for detailed rationale.

**Why Kotlin for pipeline?**
- Type safety for data processing
- Excellent Kafka ecosystem
- JVM performance for high-throughput consumers

**Why Python for scraping?**
- Superior web scraping libraries (BeautifulSoup, Scrapy)
- youtube-transcript-api for video content
- Rapid development for scraper logic

**Why Kafka?**
- Decouples producers from consumers
- Reliable message delivery
- Natural fit for streaming data pipelines
- Demonstrates event-driven architecture for DE roles
