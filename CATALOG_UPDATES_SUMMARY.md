# Catalog Updates Summary

## Changes Made

### 1. RabbitMQ Configuration Update

**File:** `services/queue/rabbitmq/versions/3.12/config.yaml`

**Changes:**
- Changed main port from `5672` (AMQP) to `15672` (Management UI)
- Changed protocol from `tcp` to `http`
- Removed `admin_port` field

**Before:**
```yaml
port: 5672
admin_port: 15672
protocol: tcp
```

**After:**
```yaml
port: 15672
protocol: http
```

**Impact:**
- RabbitMQ management UI is now accessible at `rabbitmq.doku.local` (cleaner URL)
- AMQP port (5672) is accessed via:
  - Direct connection: `rabbitmq:5672` (from other containers)
  - Port mapping: `doku install rabbitmq --port 5672` (from localhost)

### 2. README.md Comprehensive Update

**Added/Updated Sections:**

#### Complete Service List (24 services)
- **Databases (5)**: PostgreSQL, MySQL, MariaDB, MongoDB, ClickHouse
- **Cache (2)**: Redis, Memcached
- **Message Queues (2)**: RabbitMQ, Kafka
- **Search (1)**: Elasticsearch
- **Monitoring (6)**: Grafana, Prometheus, Dozzle, Jaeger, SigNoz, Sentry
- **Web Servers (1)**: Nginx
- **Development Tools (4)**: MailHog, Adminer, phpMyAdmin, LocalStack
- **Storage (1)**: MinIO
- **Security (1)**: Vault
- **Coordination (1)**: Zookeeper

#### Prerequisites Section
- Installation instructions for Doku CLI
- Using curl or Go

#### Initialize Doku Section
- First-time setup with `doku init`
- What happens during initialization

#### Enhanced Browse Services
- All catalog commands with examples
- Filtering and searching

#### Comprehensive Install Commands

**Basic Installation:**
```bash
doku install postgres
doku install postgres:16
doku install postgres:17-pgvector
```

**Advanced Options:**
```bash
# Custom instance name
doku install postgres:16 --name my-database

# Environment variables
doku install postgres \
  --env POSTGRES_USER=admin \
  --env POSTGRES_PASSWORD=secret

# Resource limits
doku install redis --memory 512m --cpu 1.0

# Port mapping
doku install postgres --port 5432
doku install rabbitmq --port 5672 --port 15672

# Custom domain
doku install postgres  # Prompts for domain

# Internal service
doku install redis --internal

# Skip prompts
doku install postgres --yes
```

#### Service Management
```bash
# List services
doku list
doku list --all

# Service info
doku info postgres
doku env postgres

# Control services
doku start postgres
doku stop postgres
doku restart postgres

# View logs
doku logs postgres
doku logs postgres -f
```

#### Uninstall Services Section

**Per-Service Removal:**
```bash
doku remove postgres

# The command will prompt:
# 1. Confirm service removal
# 2. Whether to remove volumes (data)
#    - Choose "no" to preserve data
#    - Choose "yes" to delete all data
```

**System Uninstall:**
```bash
doku uninstall

# This will prompt:
# 1. Confirm uninstallation (CANNOT be undone)
# 2. Whether to remove Docker volumes
#    - Choose "no" to preserve data
#    - Choose "yes" to delete all data
# 3. Show manual cleanup steps
```

#### Quick Start Examples
- PostgreSQL Development Setup
- Redis Cache
- RabbitMQ Message Queue
- Full Stack Setup

## Benefits

### For RabbitMQ Users
1. **Cleaner URLs**: `rabbitmq.doku.local` instead of `rabbitmq-admin.doku.local`
2. **Intuitive Access**: Management UI at main URL
3. **Clear Documentation**: AMQP access via port mapping or direct connection

### For Catalog Users
1. **Complete Service List**: All 24 services documented with emojis and versions
2. **Installation Guide**: From prerequisites to advanced configurations
3. **Uninstall Documentation**: Clear steps with volume preservation options
4. **Quick Start Examples**: Ready-to-use commands for common scenarios
5. **Service Management**: Complete lifecycle management commands

### For Contributors
1. **Clear Structure**: Service categories and organization
2. **Usage Examples**: How users interact with the catalog
3. **Installation Instructions**: How to get started with Doku

## Migration Notes

### For Existing RabbitMQ Users
If you have RabbitMQ already installed:

1. **Remove old instance:**
   ```bash
   doku remove rabbitmq
   # Choose to preserve volumes to keep data
   ```

2. **Update catalog:**
   ```bash
   doku catalog update
   ```

3. **Reinstall with new configuration:**
   ```bash
   doku install rabbitmq --port 5672 --port 15672
   ```

4. **Access:**
   - Management UI: `https://rabbitmq.doku.local`
   - AMQP: `localhost:5672`

### For New Users
Simply install with the updated catalog:
```bash
doku catalog update
doku install rabbitmq --port 5672 --port 15672
```

## Testing

### Verify RabbitMQ Configuration
```bash
# Install RabbitMQ
doku install rabbitmq --port 5672 --port 15672 --yes

# Check management UI
curl http://localhost:15672

# Check AMQP port
nc -zv localhost 5672

# Verify Traefik routing
curl https://rabbitmq.doku.local
# Should show RabbitMQ management UI login

# Check service info
doku info rabbitmq
# Should show:
# Management UI: https://rabbitmq.doku.local (port 15672)
```

## Documentation Links

- **CLI README**: [doku-cli/README.md](../doku-cli/README.md)
- **Catalog Structure**: Documented in `catalog.yaml`
- **Contributing Guide**: [CONTRIBUTING.md](CONTRIBUTING.md)
- **Gateway Pattern**: [gateway-pattern.md](gateway-pattern.md)

## Next Steps

1. ✅ Update catalog version in releases
2. ✅ Test RabbitMQ with new configuration
3. ✅ Verify all services listed in README are present
4. ✅ Update CHANGELOG.md with new version
5. ✅ Create new release with updated catalog

## Version

These changes should be included in the next catalog release (e.g., v1.1.0) with release notes documenting the RabbitMQ URL change.
