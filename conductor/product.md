# Initial Concept
This repository contains the Ansible playbooks and configurations for setting up a homelab on a Mac Studio with an M1 Max Chip running Asahi Linux.

# Product Definition - Ghostship Homelab

## Target Audience
The primary user is a self-hosting enthusiast aiming for a centralized media and automation hub. The setup is designed for someone who values a high-performance, ARM64-based server environment for personal and household use.

## Core Goals
- **Centralized Media Hub:** Provide a robust system for media acquisition, organization, and streaming.
- **Home Automation:** Serve as the brain for smart home devices and routines.
- **Secure Accessibility:** Enable remote access to services without compromising local network security.
- **Optimized Performance:** Leverage the M1 Max chip's efficiency and power using native ARM64 container images.
- **Resilience:** Implement automated health checks and recovery mechanisms to minimize downtime.

## Key Features
- **Media Management:** Automated acquisition and organization using Plex, Sonarr, Radarr, Prowlarr, Bazarr, Recyclarr, SABnzbd, qBittorrent, RomM, and PriceGhost.
- **Secure Networking:** Remote access via Cloudflared tunnels and secure download traffic through Gluetun VPN.
- **Unified Dashboards:**
    - **Homepage:** A centralized status dashboard with service-specific widgets.
    - **Muximux:** A lightweight portal for managing web applications, with links created for all services.
- **Smart Home Integration:** Deployment of Home Assistant for comprehensive IoT management.
- **Comprehensive Service Stack:**
    - **Utilities:**  Huntarr, FlareSolverr, MeTube, BentoPDF, ConvertX, IT-Tools, Warracker, FileFlows, Changedetection, Sockpuppet Browser, Arcane, Docker Autoheal, Activepieces, Zerobyte, Syncthing, Booklore, Manyfold, PriceGhost.
    - **Monitoring:** Tautulli for Plex monitoring.
    - **Infrastructure:** Docker Autoheal for container health management.
- **Configuration Management:** All environment variables are strictly documented. Services always target the "latest" best-in-class Docker images.
- **Database Strategy:** Prioritize a "one database per service" approach to ensure isolation and reliability.

## Success Criteria
- **Security & Privacy:** All services run under a restricted 'apps' user account. Minimal ports are exposed to the internet.
- **Hardware Optimization:** Every service utilizes ARM64 compatible images to run natively on Apple Silicon.
- **Maintainability:** Ansible playbooks are idempotent and environment variables drive all configurations.
- **Reliability:** Services are configured to autostart and restart on failure.
- **Documentation:**
    - All environment variables and configuration options are fully documented.
    - Complete documentation of internal Docker network hostnames and ports for every service (e.g., `gluetun:8080` for qBittorrent behind VPN).
