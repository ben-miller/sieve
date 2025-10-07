# Architecture Decision Records (ADRs)

## 2025-10-06: Initial Technology Choices

### Context
Building a portfolio project to demonstrate DE/MLOps skills for job applications. Need to showcase real-time data pipeline patterns, polyglot microservices, and practical LLM integration.

### Decisions

#### 1. Kotlin for Pipeline Processing
**Decision**: Use Kotlin for Kafka consumers and API layer
**Rationale**:
- Native Kafka integration with strong ecosystem
- Type safety prevents runtime errors in data processing
- JVM performance for high-throughput consumers
- Showcases JVM expertise beyond Java
- Concurrency primitives (coroutines) for efficient I/O

**Alternatives Considered**:
- Python: Simpler but slower for high-volume stream processing
- Java: More verbose, Kotlin offers better DX with same JVM benefits

#### 2. Python for Web Scraping
**Decision**: Use Python for all web scrapers
**Rationale**:
- BeautifulSoup, Scrapy are industry-standard scraping tools
- youtube-transcript-api has no Kotlin equivalent
- Rapid development for evolving scraper logic
- Demonstrates polyglot architecture

**Alternatives Considered**:
- Kotlin with jsoup: Less mature scraping ecosystem
- All-Kotlin: Harder to scrape dynamic content

#### 3. Apache Kafka for Message Bus
**Decision**: Use Kafka for event streaming
**Rationale**:
- Industry standard for real-time pipelines (shows DE expertise)
- Decouples producers from consumers
- Built-in durability and replayability
- Easy to add new consumers without changing producers
- Resume/portfolio value (Kafka experience highly valued)

**Alternatives Considered**:
- RabbitMQ: Less common in DE roles, fewer features
- Direct DB writes: Tight coupling, no streaming semantics
- Redis Streams: Less mature ecosystem

#### 4. PostgreSQL for Persistence
**Decision**: Use PostgreSQL as source of truth
**Rationale**:
- Mature, reliable, ubiquitous
- JSONB support for flexible content storage
- pgvector extension available for future semantic search
- Strong support in Kotlin (Exposed, jOOQ)

**Alternatives Considered**:
- MongoDB: Less suitable for structured job data
- SQLite: Not production-ready for multi-service architecture

#### 5. LLM Provider: TBD (OpenAI or Anthropic)
**Decision**: Defer to implementation phase
**Rationale**:
- Both have good Kotlin/JVM clients
- Will evaluate based on:
  - Cost per request
  - Quality of filtering results
  - API latency
  - Rate limits

#### 6. Deployment: Docker Compose
**Decision**: Use Docker Compose for local dev and initial deployment
**Rationale**:
- Simplifies local development (Kafka + Postgres + services)
- Easy to migrate to Kubernetes later
- Can deploy on any VPS or Fly.io
- Low cost for MVP

**Future**: Consider Kubernetes for resume value if time permits

#### 7. No Web UI (Initially)
**Decision**: API-first, no frontend initially
**Rationale**:
- Focus on backend/pipeline skills (more relevant for DE roles)
- API + RSS feed output sufficient for MVP
- Can add UI later if needed

#### 8. Project Timeline: 3-4 Weeks
**Decision**: Aggressive timeline to complete before job search intensifies
**Rationale**:
- Week 1: Infrastructure + basic RSS pipeline
- Week 2: Job scrapers + LLM filtering
- Week 3: API + deployment
- Week 4: Polish + documentation + blog post

### Open Questions
- Which LLM provider to use?
- Managed Kafka (Upstash/Confluent Cloud) vs self-hosted?
- Add vector DB for semantic search? (Post-MVP)

---

## Future ADRs

### When to Add

- [ ] LLM provider selection (Week 2)
- [ ] API framework choice: Spring Boot vs Ktor (Week 3)
- [ ] Deployment platform selection (Week 3)
- [ ] Authentication strategy (if needed)
