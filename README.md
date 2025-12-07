# Doku Service Catalog

Official service catalog for [Doku CLI](https://github.com/dokulabs/doku-cli) - a curated collection of 31 pre-configured services ready to run locally with a single command.

## Quick Start

```bash
# Install Doku CLI
curl -fsSL https://raw.githubusercontent.com/dokulabs/doku-cli/main/scripts/install.sh | bash

# Initialize Doku
doku init

# Install any service
doku install postgres
doku install redis
doku install rabbitmq
```

## Available Services

### Databases

| Service | Description | Versions | Install Command |
|---------|-------------|----------|-----------------|
| **PostgreSQL** | Advanced open-source relational database | 18, 18-pgvector, 17, 17-pgvector, 16, 16-pgvector, 15, 15-pgvector | `doku install postgres` |
| **MySQL** | Popular open-source relational database | 8.0 | `doku install mysql` |
| **MariaDB** | MySQL-compatible relational database | 11.2 | `doku install mariadb` |
| **MongoDB** | NoSQL document database | 7.0 | `doku install mongodb` |
| **ClickHouse** | Fast column-oriented database for analytics | latest | `doku install clickhouse` |
| **Milvus** | Open-source vector database for AI/ML | 2.4 | `doku install milvus` |

### Cache & Key-Value Stores

| Service | Description | Versions | Install Command |
|---------|-------------|----------|-----------------|
| **Redis** | In-memory data structure store | 8.0, 7.4, 7.2 | `doku install redis` |
| **Memcached** | High-performance distributed memory caching | 1.6 | `doku install memcached` |

### Message Queues

| Service | Description | Versions | Install Command |
|---------|-------------|----------|-----------------|
| **RabbitMQ** | Message broker with management UI | 4.0, 3.12 | `doku install rabbitmq` |
| **Apache Kafka** | Distributed event streaming platform | 3.8, 3.6 | `doku install kafka` |
| **NATS** | Cloud-native messaging system | 2.10 | `doku install nats` |

### Search & Analytics

| Service | Description | Versions | Install Command |
|---------|-------------|----------|-----------------|
| **Elasticsearch** | Distributed search and analytics engine | 8.15, 8.11 | `doku install elasticsearch` |

### Monitoring & Observability

| Service | Description | Versions | Install Command |
|---------|-------------|----------|-----------------|
| **Grafana** | Analytics and monitoring visualization | 11.2, 10.2 | `doku install grafana` |
| **Prometheus** | Monitoring system and time series database | 2.54, 2.48 | `doku install prometheus` |
| **Loki** | Log aggregation system (pairs with Grafana) | 2.9 | `doku install loki` |
| **Jaeger** | Distributed tracing platform | 1.52 | `doku install jaeger` |
| **SigNoz** | Open-source APM and observability platform | latest | `doku install signoz` |
| **Sentry** | Error tracking and performance monitoring | latest, 24.1 | `doku install sentry` |
| **Dozzle** | Real-time Docker log viewer | latest | `doku install dozzle` |

### Web Servers

| Service | Description | Versions | Install Command |
|---------|-------------|----------|-----------------|
| **Nginx** | High-performance HTTP server and reverse proxy | 1.27, 1.25 | `doku install nginx` |

### Development Tools

| Service | Description | Versions | Install Command |
|---------|-------------|----------|-----------------|
| **MailHog** | Email testing tool with web UI | latest | `doku install mailhog` |
| **Adminer** | Database management tool | latest | `doku install adminer` |
| **phpMyAdmin** | MySQL/MariaDB administration tool | latest | `doku install phpmyadmin` |
| **RedisInsight** | Official Redis GUI for data management | latest | `doku install redisinsight` |
| **Tabix** | ClickHouse web UI and SQL editor | stable | `doku install tabix` |
| **LocalStack** | Local AWS cloud stack for development | 3.9, 3.0 | `doku install localstack` |
| **Portainer** | Docker management UI | latest | `doku install portainer` |

### Storage

| Service | Description | Versions | Install Command |
|---------|-------------|----------|-----------------|
| **MinIO** | S3-compatible object storage | latest | `doku install minio` |

### Security

| Service | Description | Versions | Install Command |
|---------|-------------|----------|-----------------|
| **HashiCorp Vault** | Secrets management and encryption | 1.18, 1.15 | `doku install vault` |
| **Keycloak** | Open source identity and access management | 26.0 | `doku install keycloak` |

### Coordination

| Service | Description | Versions | Install Command |
|---------|-------------|----------|-----------------|
| **Apache Zookeeper** | Distributed coordination service | latest | `doku install zookeeper` |

## Usage

### Browse Services

```bash
# List all services
doku catalog

# List with detailed information
doku catalog --verbose

# Filter by category
doku catalog --category database
doku catalog --category monitoring

# Search for services
doku catalog search postgres

# Show service details
doku catalog show postgres
doku catalog show postgres --verbose
```

### Install Services

```bash
# Install latest version
doku install postgres

# Install specific version
doku install postgres:16
doku install redis:7.2

# Install with custom name
doku install postgres:16 --name my-database

# Install with environment variables
doku install postgres \
  --env POSTGRES_USER=admin \
  --env POSTGRES_PASSWORD=secret \
  --env POSTGRES_DB=myapp

# Install with resource limits
doku install redis --memory 512m --cpu 1.0

# Install with port mapping (direct localhost access)
doku install postgres --port 5432
doku install rabbitmq --port 5672 --port 15672

# Install as internal service (no external access)
doku install redis --internal

# Skip confirmation prompts
doku install postgres --yes
```

### Manage Services

```bash
# List running services
doku list

# List all services (including stopped)
doku list --all

# View service information
doku info postgres

# View environment variables
doku env postgres
doku env postgres --show-values

# Start/stop/restart services
doku start postgres
doku stop postgres
doku restart postgres

# View logs
doku logs postgres
doku logs postgres -f

# Remove a service
doku remove postgres
```

### Update Catalog

```bash
# Download the latest catalog
doku catalog update
```

## Catalog Structure

```
doku-catalog/
├── catalog.yaml              # Catalog metadata
└── services/                 # Service definitions by category
    ├── database/
    │   ├── postgres/
    │   │   ├── service.yaml  # Service metadata
    │   │   └── versions/
    │   │       └── 17/
    │   │           └── config.yaml
    │   ├── mysql/
    │   └── ...
    ├── cache/
    ├── queue/
    ├── monitoring/
    ├── security/
    └── ...
```

## Contributing

We welcome contributions! To add a new service:

1. Fork this repository
2. Create service directory: `services/<category>/<service-name>/`
3. Add `service.yaml` with metadata
4. Add version configs in `versions/<version>/config.yaml`
5. Submit a pull request

### Service Definition Format

**`service.yaml`:**
```yaml
name: ServiceName
description: Brief description
category: database
icon: "🐘"
tags:
  - tag1
  - tag2
links:
  homepage: https://example.com
  documentation: https://docs.example.com
  repository: https://github.com/org/repo
available_versions:
  - "1.0"
latest_version: "1.0"
```

**`versions/{version}/config.yaml`:**
```yaml
version: "1.0"
image: service:1.0
description: Version description
port: 8080
protocol: http
environment:
  VAR_NAME: default_value
healthcheck:
  test: ["CMD", "curl", "-f", "http://localhost:8080/health"]
  interval: 10s
  timeout: 5s
  retries: 3
resources:
  memory_min: 256m
  memory_max: 1g
  cpu_min: "0.25"
  cpu_max: "1.0"
```

## License

MIT License - see [LICENSE](LICENSE) file for details.

## Support

- [Report Issues](https://github.com/dokulabs/doku-catalog/issues)
- [Discussions](https://github.com/dokulabs/doku-catalog/discussions)
- [Doku CLI Documentation](https://github.com/dokulabs/doku-cli)
