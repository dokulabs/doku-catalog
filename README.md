# Doku Service Catalog

Official service catalog for [Doku CLI](https://github.com/dokulabs/doku-cli) - a collection of pre-configured services ready to run locally.

## Overview

This repository contains service definitions for popular development tools and infrastructure services. Each service is defined in TOML format with complete configuration including:

- Docker images and versions
- Port configurations
- Environment variables
- Health checks
- Resource requirements
- Configuration options

## Available Services

### Databases (5 services)
- **PostgreSQL** 🐘 - Advanced relational database (versions: 15, 15-pgvector, 16, 16-pgvector, 17, 17-pgvector)
- **MySQL** 🐬 - Popular relational database (version: 8.0)
- **MariaDB** 🗄️ - MySQL fork with enhanced features (version: 11.2)
- **MongoDB** 🍃 - NoSQL document database (version: 7.0)
- **ClickHouse** ⚡ - Columnar database for analytics (version: 24.3)

### Cache & Key-Value Stores (2 services)
- **Redis** 🔴 - In-memory data structure store (versions: 6.2, 7.2)
- **Memcached** 💾 - High-performance distributed memory cache (version: 1.6)

### Message Queues (2 services)
- **RabbitMQ** 🐰 - Message broker with AMQP (version: 3.12)
- **Apache Kafka** 📨 - Distributed event streaming platform (version: 3.6)

### Search & Analytics (1 service)
- **Elasticsearch** 🔍 - Distributed search engine (version: 8.11)

### Monitoring & Observability (6 services)
- **Grafana** 📊 - Analytics and monitoring platform (version: 10.2)
- **Prometheus** 🔥 - Monitoring and alerting toolkit (version: 2.48)
- **Dozzle** 🐳 - Real-time Docker log viewer (version: latest)
- **Jaeger** 🔎 - Distributed tracing platform (version: 1.52)
- **SigNoz** 📈 - Full-stack observability platform (version: 0.36)
- **Sentry** 🐛 - Error tracking and performance monitoring (version: 24.1)

### Web Servers & Proxies (1 service)
- **Nginx** 🌐 - High-performance web server (version: 1.25)

### Development Tools (4 services)
- **MailHog** 📧 - Email testing tool (version: latest)
- **Adminer** 🗄️ - Database management interface (version: 4.8)
- **phpMyAdmin** 🐘 - MySQL/MariaDB administration tool (version: 5.2)
- **LocalStack** ☁️ - AWS cloud services emulator (version: 3.0)

### Storage (1 service)
- **MinIO** 💿 - S3-compatible object storage (version: latest)

### Security (1 service)
- **HashiCorp Vault** 🔐 - Secrets management and encryption (version: 1.15)

### Coordination (1 service)
- **Apache Zookeeper** 🦓 - Distributed coordination service (version: 3.9)

**Total: 24 services across 9 categories**

## Catalog Structure

The catalog is defined in `catalog.toml` with the following structure:

```toml
version = "1.0.0"

[services.<service-name>]
name = "Display Name"
description = "Service description"
category = "database|cache|queue|monitoring|webserver|development"
icon = "emoji"
tags = ["tag1", "tag2"]

[services.<service-name>.links]
homepage = "URL"
documentation = "URL"
repository = "URL"

[services.<service-name>.versions."<version>"]
image = "docker-image:tag"
description = "Version description"
port = 5432
admin_port = 8080  # Optional
protocol = "tcp|http|grpc"

[services.<service-name>.versions."<version>".environment]
ENV_VAR = "default-value"

[services.<service-name>.versions."<version>".volumes]
volumes = ["/path/in/container"]

[services.<service-name>.versions."<version>".healthcheck]
test = ["CMD", "health-check-command"]
interval = "10s"
timeout = "5s"
retries = 5
start_period = "10s"

[services.<service-name>.versions."<version>".resources]
memory_min = "256m"
memory_max = "1g"
cpu_min = "0.25"
cpu_max = "2.0"

[[services.<service-name>.versions."<version>".configuration.options]]
name = "option_name"
description = "Option description"
type = "string|int|bool|select"
default = "default-value"
required = true
env_var = "ENV_VAR_NAME"
```

## Usage with Doku CLI

### Prerequisites

Install the Doku CLI first:

```bash
# Using curl
curl -fsSL https://raw.githubusercontent.com/dokulabs/doku-cli/main/scripts/install.sh | bash

# Or using Go
go install github.com/dokulabs/doku-cli/cmd/doku@latest
```

### Initialize Doku

```bash
# First-time setup
doku init

# This will:
# - Install SSL certificates (mkcert)
# - Configure DNS for *.doku.local
# - Set up Traefik reverse proxy
# - Download service catalog
```

### Update Catalog

```bash
# Download the latest catalog
doku catalog update

# View catalog version
doku version
```

### Browse Services

```bash
# List all services in a table
doku catalog

# List with detailed information
doku catalog --verbose

# Filter by category
doku catalog --category database
doku catalog --category monitoring

# Search for services
doku catalog search postgres
doku catalog search redis

# Show detailed service information
doku catalog show postgres
doku catalog show postgres --verbose  # Show all versions
```

### Install Services

#### Basic Installation

```bash
# Install latest version
doku install postgres

# Install specific version
doku install postgres:16
doku install redis:7.2

# Install PostgreSQL with pgvector extension (for vector similarity search)
doku install postgres:17-pgvector
```

#### Advanced Installation Options

```bash
# Custom instance name
doku install postgres:16 --name my-database

# Environment variables
doku install postgres \
  --env POSTGRES_USER=admin \
  --env POSTGRES_PASSWORD=secret \
  --env POSTGRES_DB=myapp

# Resource limits
doku install redis \
  --memory 512m \
  --cpu 1.0

# Port mapping (for direct localhost access)
doku install postgres --port 5432
doku install rabbitmq --port 5672 --port 15672

# Custom domain
doku install postgres  # Will prompt for domain (default: doku.local)

# Install as internal service (no external access)
doku install redis --internal

# Skip confirmation prompts
doku install postgres --yes

# Combined example
doku install postgres:16 \
  --name production-db \
  --env POSTGRES_PASSWORD=securepass \
  --memory 2g \
  --cpu 2.0 \
  --port 5432 \
  --yes
```

### Manage Services

```bash
# List all running services
doku list

# List all services (including stopped)
doku list --all

# View detailed service information
doku info postgres

# View environment variables
doku env postgres
doku env postgres --raw  # Show actual values

# Start/stop/restart services
doku start postgres
doku stop postgres
doku restart postgres

# View logs
doku logs postgres
doku logs postgres -f  # Follow logs in real-time
```

### Uninstall Services

```bash
# Remove a service (interactive - will prompt about volumes)
doku remove postgres

# Remove with confirmation prompt
doku remove postgres --yes

# Force remove (even if running)
doku remove postgres --force

# The command will ask:
# 1. Confirm service removal
# 2. Whether to remove volumes (data)
#    - Choose "no" to preserve data
#    - Choose "yes" to delete all data
```

### System Uninstall

```bash
# Uninstall Doku completely (interactive)
doku uninstall

# This will prompt:
# 1. Confirm uninstallation (CANNOT be undone)
# 2. Whether to remove Docker volumes
#    - Choose "no" to preserve data
#    - Choose "yes" to delete all data
# 3. Show manual cleanup steps for:
#    - DNS entries (/etc/hosts)
#    - mkcert CA certificates

# Skip prompts (use default - preserves volumes)
doku uninstall --force

# Remove everything including mkcert CA
doku uninstall --all
```

### Quick Start Examples

#### PostgreSQL Development Setup

```bash
# Install PostgreSQL
doku install postgres

# Create database and user
doku info postgres  # Get connection info

# Use in your application
DATABASE_URL=postgresql://postgres@postgres.doku.local:5432/myapp
```

#### Redis Cache

```bash
# Install Redis
doku install redis

# Connect from your app
redis://redis.doku.local:6379
```

#### RabbitMQ Message Queue

```bash
# Install RabbitMQ with port mappings
doku install rabbitmq --port 5672 --port 15672

# Access:
# - Management UI: https://rabbitmq.doku.local
# - AMQP: localhost:5672 or rabbitmq:5672
```

#### Full Stack Setup

```bash
# Install multiple services
doku install postgres
doku install redis
doku install rabbitmq
doku install mailhog

# List all services
doku list

# View logs for all services
doku logs postgres &
doku logs redis &
doku logs rabbitmq &
```

## Contributing

We welcome contributions! Here's how to add a new service:

### 1. Add Service Definition

Add your service to `catalog.toml`:

```toml
[services.myservice]
name = "My Service"
description = "What this service does"
category = "database"  # or cache, queue, monitoring, etc.
icon = "🚀"
tags = ["tag1", "tag2"]

[services.myservice.links]
homepage = "https://example.com"
documentation = "https://docs.example.com"
repository = "https://github.com/org/repo"

[services.myservice.versions."1.0"]
image = "myservice:1.0"
description = "Version 1.0"
port = 8080
protocol = "http"

[services.myservice.versions."1.0".environment]
MY_VAR = "default"

[services.myservice.versions."1.0".healthcheck]
test = ["CMD", "curl", "-f", "http://localhost:8080/health"]
interval = "10s"
timeout = "5s"
retries = 3

[services.myservice.versions."1.0".resources]
memory_min = "256m"
memory_max = "1g"
cpu_min = "0.5"
cpu_max = "1.0"
```

### 2. Test Locally

Test your service definition:

```bash
# Point doku to your local catalog
export DOKU_CATALOG_URL="file:///path/to/catalog.toml"

# Update catalog
doku catalog update

# Test the service
doku catalog show myservice
doku install myservice
```

### 3. Submit Pull Request

1. Fork this repository
2. Add your service definition to `catalog.toml`
3. Update this README with your service in the appropriate category
4. Submit a pull request

### Service Guidelines

- **Use official Docker images** when available
- **Include health checks** for proper service monitoring
- **Specify resource limits** appropriate for the service
- **Document environment variables** with clear descriptions
- **Test multiple versions** if commonly used
- **Use Alpine images** when possible for smaller size
- **Follow naming conventions**:
  - Service names: lowercase, no spaces
  - Display names: Proper case with spaces
  - Environment variables: UPPERCASE_WITH_UNDERSCORES

## Categories

Services are organized into these categories:

- **database** - Relational and NoSQL databases
- **cache** - Caching systems and key-value stores
- **queue** - Message brokers and queue systems
- **search** - Search engines and analytics platforms
- **monitoring** - Monitoring and observability tools
- **webserver** - Web servers and reverse proxies
- **development** - Development and testing tools

## Releases

The catalog is released through GitHub Releases. Each release includes:

- `catalog.toml` - The complete service catalog
- Version number following semantic versioning
- Release notes with changes

The Doku CLI automatically downloads the latest release.

## License

MIT License - see [LICENSE](LICENSE) file for details.

## Support

- **Issues**: [Report issues](https://github.com/dokulabs/doku-catalog/issues)
- **Discussions**: [Ask questions](https://github.com/dokulabs/doku-catalog/discussions)
- **CLI Docs**: [Doku CLI Documentation](https://github.com/dokulabs/doku-cli)

## Version History

### 1.0.0 (2025-10-30)
- Initial catalog release
- 12 services across 7 categories
- Support for multiple service versions
- Complete health checks and resource specifications
