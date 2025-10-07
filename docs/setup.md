# Sieve - Local Development Setup

## Prerequisites

- Java 17+
- Python 3.11+
- Docker & Docker Compose
- Poetry (for Python dependency management)

## Quick Start

### 1. Start Infrastructure Services

```bash
docker compose -f docker/docker-compose.yml up -d
```

This starts:
- **Kafka** on `localhost:9092`
- **Zookeeper** on `localhost:2181`
- **PostgreSQL** on `localhost:5432`

### 2. Verify Services

```bash
# Check all containers are running
docker compose -f docker/docker-compose.yml ps

# Test Kafka
docker exec -it sieve-kafka kafka-topics --list --bootstrap-server localhost:9092

# Test PostgreSQL
docker exec -it sieve-postgres psql -U sieve -d sieve -c '\dt'
```

### 3. Set Up Environment Variables

```bash
cp .env.example .env
# Edit .env with your API keys
```

### 4. Build Kotlin Pipeline

```bash
cd kotlin-pipeline
./gradlew build
```

### 5. Install Python Dependencies

```bash
cd python-scrapers
poetry install
```

## Running the Pipeline

### Start RSS Ingestion (Kotlin)
```bash
cd kotlin-pipeline
./gradlew run
```

### Start Job Scrapers (Python)
```bash
cd python-scrapers
poetry run python -m sieve_scrapers.scrapers.remoteok
```

## Stopping Services

```bash
docker compose -f docker/docker-compose.yml down
```

To remove volumes (clears all data):
```bash
docker compose -f docker/docker-compose.yml down -v
```

## Troubleshooting

### Kafka Connection Issues
- Ensure Docker containers are running: `docker ps`
- Check Kafka logs: `docker logs sieve-kafka`

### Database Connection Issues
- Verify PostgreSQL is healthy: `docker exec sieve-postgres pg_isready -U sieve`
- Check credentials match `.env` file

### Gradle Build Issues
- Ensure Java 17+ is installed: `java -version`
- Clean build: `./gradlew clean build`
