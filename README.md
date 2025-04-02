# Acropolis - Integrated Data Platform

Acropolis is a comprehensive data platform that combines multiple open-source tools for data management, workflow automation, visualization, and security. This Docker-based stack provides a production-ready environment with proper networking, authentication, and web access through virtual hosts.

> Also described as: Set of Server Utilities for Container, User, Process, and Security Management

## Architecture Overview

![Acropolis Architecture](https://via.placeholder.com/800x400?text=Acropolis+Architecture+Diagram)

Acropolis uses a microservices architecture with the following components:

- **Authentik**: Identity provider for authentication and authorization
- **DataHub**: Data catalog and metadata management platform
- **n8n**: Workflow automation and integration platform
- **Portainer**: Docker container management UI
- **Security Monitor**: Security monitoring and alerting system
- **Superset**: Data visualization and analytics dashboard

All services are orchestrated using Docker Compose and communicate through a shared Docker network. Each service is exposed via its own subdomain through Apache reverse proxy.

## Network Architecture

The platform uses a dedicated Docker network (`acropolis_network`) that connects all services together. Apache virtual hosts route traffic from the public internet to the appropriate internal service.

```
Internet → Apache VirtualHosts → Docker Network → Individual Services
```

## Components

### Authentik

Authentik provides identity management, single sign-on (SSO), and access control for the platform. It manages user authentication for other services in the stack.

**Access URL**: https://authentik.acumenus.net

### DataHub

DataHub is a metadata platform for the modern data stack, enabling data discovery, data governance, and data observability. The DataHub deployment includes:

- Frontend UI
- Metadata service (GMS)
- Elasticsearch for search
- Kafka and Zookeeper for event streaming
- MySQL for metadata storage

**Access URL**: https://datahub.acumenus.net

### n8n

n8n is a workflow automation tool that allows you to connect various systems and automate workflows without coding. It's ideal for data pipelines, integrations, and process automation.

**Access URL**: https://n8n.acumenus.net

### Portainer

Portainer provides a user-friendly web interface for managing Docker environments, containers, images, networks, and volumes.

**Access URL**: https://portainer.acumenus.net

### Security Monitor

The Security Monitor service provides security monitoring, threat detection, and alerting for the platform.

**Access URL**: https://secmon.acumenus.net

### Superset

Apache Superset is a modern data exploration and visualization platform. The Superset deployment includes:

- Web application
- Database (PostgreSQL)
- Redis for caching
- Celery workers for task processing

**Access URL**: https://superset.acumenus.net

## Setup Instructions

### Prerequisites

- Docker Engine (version 20.10+)
- Docker Compose (version 2.0+)
- A server with at least 16GB RAM and 4 CPU cores
- Domain configured to point to your server (*.acumenus.net in this example)
- SSL certificates for your domains

### Network Setup

Create the external Docker network that all services will share:

```bash
docker network create acropolis_network
```

### Installation

1. Clone this repository:
```bash
git clone https://github.com/acumenus/acropolis.git
cd acropolis
```

2. Configure SSL certificates:
   - Place your SSL certificates in the appropriate location
   - Update the Apache configuration files in `apache_configs/` with the correct paths

3. Start the platform:
```bash
docker compose up -d
```

This will start all services in the correct order. Initial startup may take several minutes as images are downloaded and services initialized.

## Configuration

### Environment Variables

Each service has its own environment variables that can be configured. Refer to the individual service documentation for details.

### Apache Configuration

The `apache_configs/` directory contains virtual host configurations for each service. These configurations:

- Enable SSL for each subdomain
- Set up reverse proxying to the appropriate Docker service
- Configure WebSocket support where needed (e.g., for n8n)
- Set appropriate headers and logging

### Volume Management

The platform uses Docker volumes to persist data. All volumes are managed by Docker Compose and are defined in the respective service configuration files.

## Maintenance

### Updating Services

To update individual services:

```bash
# Pull latest images for a specific service
docker compose pull <service_name>

# Restart the service with the new image
docker compose up -d <service_name>
```

### Backups

It's recommended to set up regular backups of the Docker volumes:

```bash
# Example backup command for a volume
docker run --rm -v acropolis_volume_name:/data -v /backup/location:/backup alpine tar czf /backup/volume_name_$(date +%Y%m%d).tar.gz /data
```

## Troubleshooting

### Checking Service Status

```bash
docker compose ps
```

### Viewing Logs

```bash
# View logs for all services
docker compose logs

# View logs for a specific service
docker compose logs <service_name>

# Follow logs in real-time
docker compose logs -f <service_name>
```

### Common Issues

1. **Service won't start**: Check the logs for error messages
2. **Cannot access a service**: Verify Apache configuration and ensure the service is running
3. **Database connection issues**: Check if the database container is healthy

## License

This project combines multiple open-source tools, each with its own license. Please refer to the respective project documentation for license information.

## Contributors

- Acumenus Team

## Additional Resources

- [Authentik Documentation](https://goauthentik.io/docs/)
- [DataHub Documentation](https://datahubproject.io/docs/)
- [n8n Documentation](https://docs.n8n.io/)
- [Portainer Documentation](https://docs.portainer.io/)
- [Apache Superset Documentation](https://superset.apache.org/docs/intro)
