# Implementation Plan: Add PriceGhost Service

## Phase 1: Environment and Database Setup
- [x] Task: Define environment variables for PriceGhost [3cd250f]
    - [ ] Add PostgreSQL credentials and JWT secret to the environment template (`.env.j2`)
    - [ ] Update `README.md` with descriptions for all new environment variables
- [x] Task: Configure dedicated PostgreSQL container [2018510]
    - [ ] Create persistent data directory configuration in Ansible
    - [ ] Add the `postgres-priceghost` service definition to `docker-compose.yml.j2`
- [ ] Task: Conductor - User Manual Verification 'Phase 1: Environment and Database Setup' (Protocol in workflow.md)

## Phase 2: PriceGhost Service Deployment
- [ ] Task: Configure PriceGhost Backend
    - [ ] Add the `priceghost-backend` service to `docker-compose.yml.j2`
    - [ ] Map necessary environment variables (`DATABASE_URL`, `JWT_SECRET`)
- [ ] Task: Configure PriceGhost Frontend
    - [ ] Add the `priceghost-frontend` service to `docker-compose.yml.j2`
    - [ ] Ensure correct internal networking between frontend and backend
- [ ] Task: Conductor - User Manual Verification 'Phase 2: PriceGhost Service Deployment' (Protocol in workflow.md)

## Phase 3: Dashboard and Network Integration
- [ ] Task: Expose via Cloudflared
    - [ ] Update Cloudflared configuration to route traffic to the PriceGhost frontend
- [ ] Task: Integrate with Muximux
    - [ ] Update the Muximux portal configuration template (`muximux/settings.ini.php.j2`)
- [ ] Task: Integrate with Homepage
    - [ ] Update the Homepage services configuration (`homepage/services.yaml.j2`) with a PriceGhost widget
- [ ] Task: Conductor - User Manual Verification 'Phase 3: Dashboard and Network Integration' (Protocol in workflow.md)

## Phase 4: Final Verification
- [ ] Task: Execute System-Wide Dry Run
    - [ ] Run the Ansible playbook in check mode to verify all templates and configurations
- [ ] Task: Conductor - User Manual Verification 'Phase 4: Final Verification' (Protocol in workflow.md)
