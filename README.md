# AI Stack

A comprehensive AI stack featuring LiteLLM proxy, Ollama, Open-WebUI, PostgreSQL database, and Prometheus monitoring.

## Services Overview

- **LiteLLM Proxy** - API gateway for multiple LLM providers (port 4000)
- **Ollama** - Local LLM runtime with model management (port 11434)
- **Open-WebUI** - Web interface for interacting with AI models (port 3000)

Supporting services:

- **PostgreSQL** - Database for storing LiteLLM data (port 5432)
- **Prometheus** - Metrics collection and monitoring (port 9090)

## Prerequisites

- Docker and Docker Compose
- Absolute minimum is 8GB RAM (16GB+ or more highly recommended)
- GPU support (recommended an nVidia or AMD GPU for better performance)

## Quick Start

### 1. Clone and Setup

```bash
git clone <repository-url>
cd ai_stack
```

### 2. Create Environment File

Create a `.env` file in the root directory with the following variables:

```env
LITELLM_MASTER_KEY="sk-[put 10-16 random numbers here]"
LITELLM_SALT_KEY="sk-[put 10-16 random numbers here]"
```

**LITELLM_MASTER_KEY** is your starting **admin** password for **liteLLM**

### 3. Security Configuration

**IMPORTANT**: Change the default database passwords in `docker-compose.yml`:

```yaml
# In the db service section, change:
POSTGRES_PASSWORD: YOUR_SECURE_PASSWORD_HERE

# In the proxy service section, update DATABASE_URL:
DATABASE_URL: "postgresql://llmproxy:YOUR_SECURE_PASSWORD_HERE@db:5432/litellm"
```

### 4. Start the Stack

```bash
docker-compose up -d
```

### 5. Verify Services

Check that all services are running:

```bash
docker-compose ps
```

## Service Access

- **Open-WebUI**: http://localhost:3000
- **LiteLLM Proxy**: http://localhost:4000
- **Prometheus**: http://localhost:9090
- **Ollama API**: http://localhost:11434
- **PostgreSQL**: localhost:5432

## Configuration

### LiteLLM Models

The system is configured to automatically proxy all Ollama models through LiteLLM. Models are configured in `litellm_config.yaml`.

### Ollama Models

Pull models using the Ollama service:

```bash
docker exec ollama ollama pull qwen3:latest
docker exec ollama ollama pull gemma3:latest
```

### GPU Support

The configuration includes GPU support for AMD/Intel GPUs. For NVIDIA GPUs, uncomment the relevant sections in `docker-compose.yml`.

## Data Persistence

- Ollama models: `./ollama/ollama`
- Open-WebUI data: `./data/open-webui`
- PostgreSQL data: Docker volume `litellm_postgres_data`
- Prometheus data: Docker volume `prometheus_data`

## Monitoring

Prometheus is configured to collect metrics from the LiteLLM proxy. Access the Prometheus web interface at http://localhost:9090.

## Troubleshooting

### Common Issues

1. **Port conflicts**: Ensure ports 3000, 4000, 5432, 9090, and 11434 are available
2. **Database connection issues**: Verify the DATABASE_URL in your `.env` file matches the database credentials
3. **GPU access**: Ensure your user is in the `docker` group and has access to `/dev/dri`

### Logs

View service logs:

```bash
docker-compose logs [service-name]
```

### Health Checks

The stack includes health checks for critical services. Check service health:

```bash
docker-compose ps
```

## Security Notes

- Change all default passwords before production use
- Consider using environment variables for sensitive configuration
- Regularly update container images for security patches
- Limit network access to required ports only

## Development

To modify the configuration:

1. Edit the relevant configuration files (`docker-compose.yml`, `litellm_config.yaml`, etc.)
2. Restart the affected services: `docker-compose restart [service-name]`

## Contributing

Please ensure any changes maintain security best practices and update this README accordingly.