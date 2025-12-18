# Gemini Instructions

You are building out a homelab on a Mac Studio with an M1 Max Chip running Asahi Linux.  We will host everything headless with docker used to run all the containerized services.

This project is meant to get a stock Mac Studio ready to act as a headless server that will host services.

The ansible playbook will be used to install, update, and run the services for the server.  The playbook must be able to run multiple times and only make changes where they are necessary.  It should detect when a service has been updated and make sure that it's working.

All docker services must be configured to autostart and restart on failure.  They need to be configured to use a less privileged account while running.

All variables including the host ip address should be configured via environment variables.  These environment variables must be documented in `Readme.md`.

Docker images should use the base name as the container name.  They should all be configured to automatically use the latest version of the software available.

The ansible account will be used to provision and manage the server.  All services should run using an apps service account which will be used to limit permissions for all installed apps.

All docker images should must use and have arm64 compatible images in order to be used.