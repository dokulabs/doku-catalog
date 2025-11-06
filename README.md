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

### Databases
- **PostgreSQL** 🐘 - Advanced relational database (versions: 15, 15-pgvector, 16, 16-pgvector, 17, 17-pgvector)
- **MySQL** 🐬 - Popular relational database (version: 8.0)
- **MongoDB** 🍃 - NoSQL document database (version: 7.0)

### Cache & Key-Value Stores
- **Redis** 🔴 - In-memory data structure store (versions: 6.2, 7.2)

### Message Queues
- **RabbitMQ** 🐰 - Message broker with AMQP (version: 3.12)

### Search & Analytics
- **Elasticsearch** 🔍 - Distributed search engine (version: 8.11)

### Monitoring & Observability
- **Grafana** 📊 - Analytics and monitoring platform (version: 10.2)
- **Prometheus** 🔥 - Monitoring and alerting toolkit (version: 2.48)

### Web Servers & Proxies
- **Nginx** 🌐 - High-performance web server (version: 1.25)

### Development Tools
- **MailHog** 📧 - Email testing tool
- **Adminer** 🗄️ - Database management interface

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

### Update Catalog

```bash
doku catalog update
```

This downloads the latest catalog from GitHub releases.

### Browse Services

```bash
# List all services
doku catalog list

# Filter by category
doku catalog list --category database

# Search services
doku catalog search postgres

# Show service details
doku catalog show postgres
doku catalog show postgres --verbose  # Show all versions
```

### Install Services

```bash
# Install latest version
doku install postgres

# Install specific version
doku install postgres:16

# Install PostgreSQL with pgvector extension (for vector similarity search)
doku install postgres:17-pgvector

# Install with custom instance name
doku install postgres:16 --name my-db

# Install with custom configuration
doku install postgres:16 --name my-db \
  --env POSTGRES_USER=admin \
  --env POSTGRES_PASSWORD=secret \
  --memory 2g \
  --cpu 1.0
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
