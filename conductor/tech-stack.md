# Technology Stack - Ghostship Homelab

## Infrastructure & Orchestration
- **Ansible:** Primary configuration management and orchestration tool. Used for system updates, service deployment, and configuration templating.
- **Docker & Docker Compose:** Containerization platform for all services. Utilizes `docker-compose.yml` templates for service definition and management.

## Operating System & Hardware
- **Asahi Linux:** Headless Linux distribution optimized for Apple Silicon.
- **Apple Silicon (M1 Max):** The target hardware architecture. All software must be ARM64 compatible.

## Networking & Security
- **Cloudflared:** Provides secure tunnels for exposing web services without opening firewall ports.
- **Gluetun:** VPN client container used to secure network traffic for specific services (e.g., download clients).
- **Docker Autoheal:** Monitor and automatically restart unhealthy containers.

## Data Management
- **MariaDB:** Relational database server used for multiple services.
- **PostgreSQL:** Relational database server used for services requiring it (e.g., Warracker, PriceGhost). Follows a "one database per service" strategy for isolation.
- **Environment Variables:** Used for all configuration secrets and host-specific settings, documented in `README.md`.

## Dashboards & UI
- **Homepage:** Centralized, widget-based dashboard for service health and navigation.
- **Muximux:** Portal for unified access to multiple web applications.
