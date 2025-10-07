# Sieve - LLM-Powered Job & Content Filter

## Project Goal
Build a real-time data pipeline that ingests job postings, YouTube videos, and RSS feeds, filters content using LLM prompts, and serves results via API. Demonstrates DE/MLOps architecture patterns with polyglot microservices.

**Primary Use Case:** Filter job postings from multiple sources to find relevant opportunities  
**Target Timeline:** 3-4 weeks  
**Tech Stack:** Kotlin (pipeline) + Python (scraping) + Kafka + PostgreSQL

---

## Week 1: Core Pipeline Infrastructure

### Project Setup
- [ ] Initialize Git repo with .gitignore (Kotlin + Python)
- [ ] Create Kotlin project with Gradle (Kotlin DSL)
- [ ] Create Python project structure for scrapers
- [ ] Set up Docker Compose: Kafka + Zookeeper + PostgreSQL
- [ ] Verify all services running and accessible
- [ ] Create project directory structure
  ```
  /kotlin-pipeline    # Kotlin services
  /python-scrapers    # Python scraping services
  /docs               # Documentation
  /docker             # Docker configs
  ```

### Database Schema (PostgreSQL)
- [ ] Design and document schema (sources, filters, raw_items, filtered_items)
- [ ] Create migration script or use Flyway
- [ ] Create `sources` table (id, url, type, name, poll_interval, last_polled_at, active)
- [ ] Create `filters` table (id, source_id, name, prompt, active)
- [ ] Create `raw_items` table (id, source_id, title, url, content, published_at, guid)
- [ ] Create `filtered_items` table (id, raw_item_id, filter_id, llm_reasoning, score, filtered_at)
- [ ] Add indexes on commonly queried fields

### Kafka Topics Setup
- [ ] Create `raw_feeds` topic
- [ ] Create `parsed_items` topic
- [ ] Create `filtered_items` topic
- [ ] Document topic schemas and retention policies
- [ ] Test producing/consuming from topics

### RSS Ingestion (Kotlin)
- [ ] Add Rome library for RSS parsing
- [ ] Create RSS fetcher service
- [ ] Fetch single RSS feed (test with YouTube channel)
- [ ] Parse feed items into structured format
- [ ] Create Kafka producer for raw feed items
- [ ] Publish to `raw_feeds` topic
- [ ] Add error handling for fetch failures
- [ ] Test end-to-end: RSS → Kafka

### Basic Parser Consumer (Kotlin)
- [ ] Create Kafka consumer for `raw_feeds` topic
- [ ] Parse and normalize feed items
- [ ] Save to PostgreSQL `raw_items` table
- [ ] Deduplicate by guid/url
- [ ] Publish normalized items to `parsed_items` topic
- [ ] Add logging for consumer processing
- [ ] Test: RSS → Kafka → Postgres

### Documentation
- [ ] Write docs/setup.md (local dev setup)
- [ ] Create basic architecture diagram
- [ ] Start DECISIONS.md with initial choices
- [ ] Update README with project overview

---

## Week 2: Job Scraping & LLM Filtering

### Python Scraping Service Setup
- [ ] Set up Python virtual environment (Poetry or uv)
- [ ] Add dependencies: kafka-python, beautifulsoup4, requests, scrapy
- [ ] Create Kafka producer (Python)
- [ ] Test producing to `raw_feeds` from Python
- [ ] Create base scraper class/interface

### Job Board Scrapers (Python)
- [ ] Research job board structures (RemoteOK, We Work Remotely, etc.)
- [ ] Implement RemoteOK scraper (start with easiest)
- [ ] Implement We Work Remotely scraper
- [ ] Implement Indeed scraper (if feasible)
- [ ] Handle pagination and rate limiting
- [ ] Publish scraped jobs to `raw_feeds` Kafka topic
- [ ] Add retry logic and error handling
- [ ] Schedule scrapers (cron or while loop with sleep)

### YouTube Integration (Python)
- [ ] Add YouTube RSS feeds to sources
- [ ] Install youtube-transcript-api library
- [ ] Fetch video transcripts for new videos
- [ ] Publish video + transcript to Kafka
- [ ] Handle videos without transcripts gracefully

### LLM Filter Setup (Kotlin)
- [ ] Choose LLM provider (OpenAI or Anthropic Claude)
- [ ] Set up API credentials (environment variables)
- [ ] Create prompt template for filtering
- [ ] Test LLM API calls with sample job descriptions
- [ ] Add retry logic and rate limiting
- [ ] Handle LLM errors gracefully

### LLM Filter Consumer (Kotlin)
- [ ] Create consumer for `parsed_items` topic
- [ ] Fetch active filters for each item's source
- [ ] Pass item content through LLM with filter prompt
- [ ] Parse LLM response (keep/discard + reasoning)
- [ ] Save filtered items to `filtered_items` table
- [ ] Publish to `filtered_items` topic
- [ ] Handle cases where item matches multiple filters
- [ ] Add logging for filter decisions

### Configuration Management
- [ ] Design how to configure sources (DB or config files)
- [ ] Design how to configure filters (DB preferred)
- [ ] Seed database with initial sources and filters
- [ ] Test adding/updating sources and filters

### Monitoring & Observability
- [ ] Add structured logging (JSON format)
- [ ] Log key metrics: items processed, LLM calls, failures
- [ ] Add basic health checks for each service
- [ ] Consider simple metrics endpoint

---

## Week 3: API Layer & Deployment

### API Development (Kotlin - Spring Boot or Ktor)
- [ ] Choose framework (Spring Boot recommended)
- [ ] Set up basic Spring Boot REST API
- [ ] Configure database connection (use Exposed or jOOQ)
- [ ] Implement GET /jobs - list filtered items
- [ ] Implement GET /jobs/{id} - get single item detail
- [ ] Implement POST /sources - add new source
- [ ] Implement GET /sources - list sources
- [ ] Implement POST /filters - create new filter
- [ ] Implement GET /filters - list filters
- [ ] Implement PUT /filters/{id} - update filter
- [ ] Add OpenAPI/Swagger documentation
- [ ] Write docs/api.md with examples

### RSS Feed Output
- [ ] Generate RSS/Atom XML from filtered items
- [ ] Implement GET /feed/{filter_id}/rss
- [ ] Set proper Content-Type headers
- [ ] Test RSS output in feed reader (e.g., Feedly)

### Testing
- [ ] Unit tests for RSS parser (Kotlin)
- [ ] Unit tests for job scrapers (Python)
- [ ] Unit tests for LLM filtering logic (Kotlin)
- [ ] Integration test: full pipeline end-to-end
- [ ] Test error scenarios (source down, LLM timeout, Kafka down)
- [ ] Test deduplication logic

### Deployment Preparation
- [ ] Create production docker-compose.yml
- [ ] Separate docker-compose for dev vs prod
- [ ] Environment variable management (.env files)
- [ ] Choose deployment platform (Fly.io, Railway, Render, or VPS)
- [ ] Choose managed Kafka (Upstash, Confluent Cloud) or self-host
- [ ] Choose managed Postgres or self-host
- [ ] Create Dockerfiles for each service
- [ ] Test Docker builds locally

### Deployment
- [ ] Deploy PostgreSQL (managed or container)
- [ ] Deploy Kafka (managed or container)
- [ ] Deploy Kotlin pipeline services
- [ ] Deploy Python scraping service
- [ ] Deploy API service
- [ ] Configure networking between services
- [ ] Test all endpoints in production
- [ ] Set up SSL/TLS if needed
- [ ] Set up basic monitoring/alerting
- [ ] Write docs/deployment.md

---

## Week 4: Polish, Documentation & Job Search

### Code Quality
- [ ] Run Kotlin linter (ktlint)
- [ ] Run Python linter (ruff or black)
- [ ] Add type hints throughout Python code
- [ ] Ensure consistent formatting
- [ ] Remove dead code and TODOs
- [ ] Update dependencies to latest stable
- [ ] Security scan (if tools available)

### Comprehensive Documentation
- [ ] Write excellent README.md
  - [ ] Problem statement (why this exists)
  - [ ] Architecture overview with diagram
  - [ ] Technology choices and rationale
  - [ ] Quick start guide
  - [ ] API examples with curl commands
  - [ ] Configuration guide
  - [ ] Deployment instructions
- [ ] Complete docs/architecture.md
  - [ ] System architecture diagram
  - [ ] Data flow diagrams
  - [ ] Component interactions
  - [ ] Scaling considerations
- [ ] Complete DECISIONS.md with all ADRs
  - [ ] Why Kotlin for pipeline
  - [ ] Why Python for scraping
  - [ ] Why Kafka vs alternatives
  - [ ] LLM provider choice
  - [ ] Database schema decisions
- [ ] Add example configurations
  - [ ] Sample sources
  - [ ] Sample filter prompts
  - [ ] Environment variables template
- [ ] Add troubleshooting guide

### Blog Post & Marketing
- [ ] Draft blog post: "Building an LLM-Powered Job Filter with Kafka"
- [ ] Include architecture diagrams
- [ ] Discuss polyglot microservices design
- [ ] Code snippets and examples
- [ ] Challenges faced and solutions
- [ ] Performance considerations
- [ ] Publish on Medium/Dev.to/personal blog
- [ ] Share on LinkedIn with project link
- [ ] Consider "Show HN" on Hacker News

### Portfolio & Resume Updates
- [ ] Push final code to GitHub with excellent README
- [ ] Add project to LinkedIn profile
- [ ] Update resume with project bullet:
  - "Built real-time job filtering pipeline using Kafka, Kotlin, and LLMs"
  - "Designed polyglot microservices architecture (Kotlin + Python)"
  - "Integrated multiple data sources (job boards, RSS, YouTube transcripts)"
- [ ] Prepare demo for interviews
- [ ] Create 2-minute explanation of architecture
- [ ] Prepare to discuss technical decisions

### Actually Use It For Job Search
- [ ] Configure filters for your target roles (DE, MLOps, AI Ops)
- [ ] Add all relevant job boards as sources
- [ ] Add relevant YouTube channels (tech talks, company channels)
- [ ] Tune LLM prompts for best results
- [ ] Check filtered feed daily
- [ ] Apply to filtered jobs!

---

## Parking Lot (Post-MVP Enhancements)

### If time permits or after landing job:
- [ ] Vector database for semantic search (pgvector or Pinecone)
- [ ] Similarity search: "find jobs like this one"
- [ ] More job boards (Greenhouse, Lever, company pages)
- [ ] LinkedIn Jobs scraper (challenging)
- [ ] Web UI for managing sources and filters
- [ ] Email/Slack notifications for new filtered items
- [ ] Multiple filter chains per source
- [ ] Batch processing mode for cost optimization
- [ ] Prometheus + Grafana monitoring
- [ ] Airflow/Prefect orchestration
- [ ] A/B testing different LLM prompts
- [ ] Multi-user support with authentication
- [ ] Mobile app or Telegram bot interface
- [ ] Export filtered items (CSV, JSON)
- [ ] Archive/bookmark functionality

---

## Daily Workflow

At end of each coding session:
- [ ] Commit code with descriptive message
- [ ] Update this TODO with progress
- [ ] Note any blockers or learnings
- [ ] Plan next session's focus (max 3 items)

---

## Success Criteria

**Week 1 Complete:**
- ✅ Can ingest RSS feed → Kafka → Postgres
- ✅ Basic pipeline infrastructure running

**Week 2 Complete:**
- ✅ Job scrapers fetching real job postings
- ✅ LLM filtering working end-to-end
- ✅ Can see filtered jobs in database

**Week 3 Complete:**
- ✅ API deployed and accessible
- ✅ Can query filtered jobs via REST API
- ✅ RSS feed output working

**Week 4 Complete:**
- ✅ Blog post published
- ✅ GitHub repo polished
- ✅ Resume updated
- ✅ Actively using tool for job search
- ✅ Applying to jobs with project on resume

---

## Key Technical Decisions

**2025-10-06:**
- **Language choice:** Kotlin for pipeline (your strength + Kafka native), Python for scraping (better ecosystem)
- **Event streaming:** Kafka for demonstrating real-time data pipeline patterns
- **Primary use case:** Job posting aggregation and filtering (solves your own problem)
- **Data sources:** Job boards, YouTube channels, RSS feeds
- **Persistence:** PostgreSQL as source of truth, Kafka as message bus only
- **LLM:** OpenAI or Anthropic for filtering content
- **Deployment target:** Containerized services on Fly.io or similar

**Architecture Pattern:**
```
Python Scrapers → Kafka → Kotlin Consumers → PostgreSQL
                                              ↓
                                      Kotlin API Layer
```

**Why this architecture:**
- Demonstrates polyglot microservices
- Uses right tool for each job (Python=scraping, Kotlin=data processing)
- Shows understanding of event-driven architecture
- Kafka demonstrates streaming data patterns for DE roles
- Actually useful for your job search

---

## Notes Section

_(Use this space for ongoing notes, ideas, blockers)_

### Week 1 Notes:

### Week 2 Notes:

### Week 3 Notes:

### Week 4 Notes:
