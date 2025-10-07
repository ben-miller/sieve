# Sieve - LLM-Powered Job & Content Filter

Real-time data pipeline that ingests job postings, YouTube videos, and RSS feeds, filters content using LLM prompts, and serves results via API.

**Status:** Week 1 - Core Infrastructure ✅

## Architecture

```
Python Scrapers → Kafka → Kotlin Consumers → PostgreSQL
                                              ↓
                                      Kotlin API Layer
```

## Tech Stack

- **Pipeline**: Kotlin + Gradle
- **Scrapers**: Python + Poetry
- **Messaging**: Apache Kafka
- **Database**: PostgreSQL
- **LLM**: OpenAI/Anthropic (TBD)
- **Deployment**: Docker Compose

## Project Structure

```
/kotlin-pipeline    # Kotlin services (consumers, API)
/python-scrapers    # Python scraping services
/docs               # Documentation
/docker             # Docker Compose configuration
```

## Quick Start

See [docs/setup.md](docs/setup.md) for detailed setup instructions.

```bash
# 1. Start infrastructure
docker compose -f docker/docker-compose.yml up -d

# 2. Build Kotlin pipeline
cd kotlin-pipeline && ./gradlew build

# 3. Install Python dependencies
cd python-scrapers && poetry install
```

## Project Goals

- ✅ Build real-time data pipeline with Kafka
- ✅ Demonstrate polyglot microservices (Kotlin + Python)
- 🔄 Filter job postings using LLM prompts
- 🔄 Aggregate content from multiple sources
- 🔄 Serve filtered results via REST API

## Documentation

- [Setup Guide](docs/setup.md)
- [TODO Checklist](TODO.md)

## License

MIT
