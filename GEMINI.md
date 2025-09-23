# Gemini Instructions

You are building out a homelab on a Mac Studio with an M1 Max Chip.  We will be using Ansible to do all the configuration and updating of services.  Services will either be running inside of Docker containers or natively on the Mac Studio.

This project is meant to get a stock Mac Studio ready to act as a headless server that will host services.

The ansible playbook will be used to install, update, and run the services for the server.  The playbook must be able to run multiple times and only make changes where they are necessary.  It should detect when a service has been updated and make sure that it's working.

All docker services must be configured to autostart and restart on failure.  They need to be configured to use a less privileged account while running.

All variables including the host ip address should be configured via environment variables.  These environment variables must be documented in `Readme.md`.

Docker images should use the base name as the container name (example: caddy for the caddy image as the base name).  They should all be configured to automatically use the latest version of the software available.

All docker images that get added with a webui should be proxied through Caddy.  This means each docker image needs to be configured to use a lets encrypt certificate through Caddy.  Caddy should be configured to use cloudflare dns challenge to generate certificates and the certificates must automatically renew.  There should be a base domain and then each container will automatically use a subdomain on the base domain which will match the container name.

The ansible account will be used to provision and manage the server.  All services should run using an apps service account which will be used to limit permissions for all installed apps.

All services should be accessible through the Caddy image.  Each service should be linked to Organizr with an iframe.  Each service should have its widget (if it exists) configured in Homepage.
