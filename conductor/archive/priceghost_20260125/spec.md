# Specification: Add PriceGhost Service

## Overview
This track involves adding **PriceGhost**, a self-hosted price tracking application, to the Ghostship homelab. PriceGhost monitors product prices and provides notifications for price drops and back-in-stock alerts.

## Functional Requirements
- **Containerized Deployment:** Deploy PriceGhost using Docker Compose within the Ansible orchestration.
    - **Frontend:** React-based web interface.
    - **Backend:** Node.js/Express API.
    - **Database:** Dedicated PostgreSQL instance (as required by PriceGhost).
- **Network Integration:**
    - Attach services to the existing internal Docker network.
    - Configure a Cloudflared tunnel to expose the frontend securely.
- **Dashboard Integration:**
    - Add a service tile/widget to the **Homepage** dashboard.
    - Create a link in the **Muximux** portal for unified access.
- **Configuration:**
    - Drive all secrets (Postgres credentials, JWT secret) and hostnames via environment variables.
    - Document all new environment variables in the project `README.md`.

## Non-Functional Requirements
- **Architecture:** Use ARM64 compatible images for all components (Frontend, Backend, Postgres).
- **Security:** Services should run as the restricted `apps` user where possible.
- **Persistence:** Ensure PostgreSQL data is persisted in a dedicated volume or host directory.
- **Resilience:** Configure containers with an `unless-stopped` restart policy.

## Acceptance Criteria
- [ ] PriceGhost frontend is accessible via the configured Cloudflared tunnel URL.
- [ ] PriceGhost can successfully connect to its dedicated PostgreSQL database.
- [ ] PriceGhost is listed on the Homepage dashboard with a functional link.
- [ ] Muximux contains a working link to the PriceGhost interface.
- [ ] All new environment variables are documented in `README.md`.

## Out of Scope
- Integration with AI services (Claude/OpenAI/Ollama) is optional and will not be configured in this initial track unless requested.
- Advanced notification provider setup (e.g., Discord/Telegram) beyond basic internal tracking.
