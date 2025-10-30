# Contributing to Doku Catalog

Thank you for your interest in contributing to the Doku Service Catalog! This document provides guidelines for adding new services or improving existing ones.

## Quick Start

1. Fork the repository
2. Create a new branch: `git checkout -b add-myservice`
3. Add your service to `catalog.toml`
4. Test your changes
5. Submit a pull request

## Adding a New Service

### 1. Choose a Category

Services are organized by category:

- `database` - Relational and NoSQL databases
- `cache` - Caching systems and key-value stores
- `queue` - Message brokers and queue systems
- `search` - Search engines and analytics platforms
- `monitoring` - Monitoring and observability tools
- `webserver` - Web servers and reverse proxies
- `development` - Development and testing tools

If your service doesn't fit these categories, propose a new one in your PR.

### 2. Service Definition Template

```toml
[services.myservice]
name = "My Service"
description = "Brief description of what this service does (1-2 sentences)"
category = "database"
icon = "🚀"  # Choose an appropriate emoji
tags = ["relevant", "searchable", "tags"]

[services.myservice.links]
homepage = "https://myservice.io"
documentation = "https://docs.myservice.io"
repository = "https://github.com/org/myservice"

[services.myservice.versions."1.0"]
image = "myservice:1.0"
description = "What's special about this version"
port = 8080
admin_port = 8081  # Optional, only if service has separate admin interface
protocol = "http"  # or "tcp", "grpc", etc.

[services.myservice.versions."1.0".environment]
MY_VAR = "default_value"
ANOTHER_VAR = "value"

[services.myservice.versions."1.0".volumes]
volumes = ["/path/to/data", "/path/to/config"]

[services.myservice.versions."1.0".healthcheck]
test = ["CMD", "command", "args"]
interval = "10s"
timeout = "5s"
retries = 5
start_period = "10s"

[services.myservice.versions."1.0".resources]
memory_min = "256m"
memory_max = "1g"
cpu_min = "0.25"
cpu_max = "1.0"

# Optional: Interactive configuration
[[services.myservice.versions."1.0".configuration.options]]
name = "username"
description = "Admin username"
type = "string"
default = "admin"
required = true
env_var = "MY_SERVICE_USER"

[[services.myservice.versions."1.0".configuration.options]]
name = "mode"
description = "Operating mode"
type = "select"
default = "production"
required = false
env_var = "MY_SERVICE_MODE"
options = ["development", "production"]
```

### 3. Guidelines

#### Docker Images
- Use **official images** from Docker Hub when available
- Prefer **Alpine variants** for smaller image sizes
- Specify **exact version tags**, not `latest`
- Test images before submitting

#### Resource Limits
Set realistic resource limits:
- **Lightweight services** (Redis, Nginx): 64-256MB, 0.1-0.5 CPU
- **Medium services** (PostgreSQL, MySQL): 256MB-1GB, 0.25-2.0 CPU
- **Heavy services** (Elasticsearch): 1GB-2GB, 1.0-2.0 CPU

#### Health Checks
- Always include health checks
- Use service-specific health commands
- Set reasonable intervals (5-30 seconds)
- Allow adequate start period for slow-starting services

#### Environment Variables
- Document all environment variables
- Provide sensible defaults
- Mark required variables clearly
- Use consistent naming (UPPERCASE_WITH_UNDERSCORES)

#### Volumes
- List all important data directories
- Don't include log directories unless crucial
- Document what each volume contains

### 4. Multiple Versions

Include multiple versions when:
- Breaking changes exist between versions
- Users commonly run different versions
- LTS versions are available

Example:
```toml
[services.postgres.versions."16"]
image = "postgres:16-alpine"
description = "PostgreSQL 16 - Latest with improved performance"
# ... rest of config

[services.postgres.versions."15"]
image = "postgres:15-alpine"
description = "PostgreSQL 15 - Previous stable release"
# ... rest of config
```

### 5. Configuration Options

Add interactive configuration options for:
- Credentials (username, password)
- Database/project names
- Operating modes
- Important feature flags

Types supported:
- `string` - Text input
- `int` - Integer input
- `bool` - Yes/no checkbox
- `select` - Dropdown with options

### 6. Testing Your Service

Before submitting:

```bash
# Copy your local catalog
cp catalog.toml ~/.doku/catalog/catalog.toml

# Validate it loads
doku catalog list

# Check your service appears
doku catalog show myservice

# Test installation (if doku install is implemented)
doku install myservice
```

### 7. Documentation

Update `README.md`:
- Add your service to the appropriate category list
- Include the icon and name
- List available versions

## Service Quality Checklist

- [ ] Service name is lowercase, no spaces
- [ ] Display name is properly capitalized
- [ ] Description is clear and concise
- [ ] Appropriate category selected
- [ ] Icon emoji is relevant
- [ ] All links (homepage, docs, repo) provided
- [ ] At least one version defined
- [ ] Official Docker image used
- [ ] Health check included and tested
- [ ] Resource limits are realistic
- [ ] Environment variables documented
- [ ] Volumes specified for persistent data
- [ ] Configuration options for credentials
- [ ] README.md updated

## Pull Request Process

1. **Create PR** with descriptive title: "Add [Service Name] v[Version]"

2. **PR Description** should include:
   - Brief service description
   - Why it's useful for developers
   - Any special considerations
   - Link to official documentation

3. **Review Process**:
   - Maintainers will test your service
   - May request changes or improvements
   - Once approved, will be merged

4. **After Merge**:
   - Service will be included in next catalog release
   - Users can access via `doku catalog update`

## Common Issues

### TOML Syntax Errors
```bash
# Validate TOML syntax
toml-cli validate catalog.toml
```

### Image Not Found
- Verify image exists on Docker Hub
- Test pull locally: `docker pull myservice:1.0`

### Health Check Fails
- Test health check command in running container
- Ensure adequate start_period for initialization

### Resource Limits Too Low
- Monitor actual usage: `docker stats`
- Set minimums for reliable operation
- Set maximums to prevent resource exhaustion

## Getting Help

- **Questions**: Open a [Discussion](https://github.com/dokulabs/doku-catalog/discussions)
- **Bugs**: Report an [Issue](https://github.com/dokulabs/doku-catalog/issues)
- **Chat**: Join our community (link TBD)

## Code of Conduct

Please note we have a code of conduct. Follow it in all interactions:
- Be respectful and inclusive
- Provide constructive feedback
- Focus on what's best for the community
- Show empathy towards others

## Recognition

Contributors will be:
- Listed in release notes
- Credited in the repository
- Part of the Doku community

Thank you for contributing! 🎉
