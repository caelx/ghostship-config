# Implementation Plan: Add PriceGhost Service

## Phase 1: Environment and Database Setup
- [x] Task: Define environment variables for PriceGhost [3cd250f]
    - [ ] Add PostgreSQL credentials and JWT secret to the environment template (`.env.j2`)
    - [ ] Update `README.md` with descriptions for all new environment variables
- [x] Task: Configure dedicated PostgreSQL container [2018510]
    - [ ] Create persistent data directory configuration in Ansible
    - [ ] Add the `postgres-priceghost` service definition to `docker-compose.yml.j2`
- [x] Task: Conductor - User Manual Verification 'Phase 1: Environment and Database Setup' (Protocol in workflow.md) [checkpoint: 810319c]

## Phase 2: PriceGhost Service Deployment
- [x] Task: Configure PriceGhost Backend [6d168d6]
    - [ ] Add the `priceghost-backend` service to `docker-compose.yml.j2`
    - [ ] Map necessary environment variables (`DATABASE_URL`, `JWT_SECRET`)
- [x] Task: Configure PriceGhost Frontend [6afba78]
    - [ ] Add the `priceghost` service to `docker-compose.yml.j2`
    - [ ] Ensure correct internal networking between frontend and backend
- [x] Task: Conductor - User Manual Verification 'Phase 2: PriceGhost Service Deployment' (Protocol in workflow.md) [checkpoint: e554bda]

## Phase 3: Dashboard and Network Integration
- [x] Task: Expose via Cloudflared [e07839e]
    - [ ] Update Cloudflared configuration to route traffic to the PriceGhost frontend
- [x] Task: Integrate with Muximux [e07839e]
    - [ ] Update the Muximux portal configuration template (`muximux/settings.ini.php.j2`)
- [x] Task: Integrate with Homepage [e07839e]
    - [ ] Update the Homepage services configuration (`homepage/services.yaml.j2`) with a PriceGhost widget
- [x] Task: Conductor - User Manual Verification 'Phase 3: Dashboard and Network Integration' (Protocol in workflow.md) [checkpoint: e07839e]

## Phase 4: Final Verification
- [~] Task: Execute System-Wide Dry Run
    - [ ] Run the Ansible playbook in check mode to verify all templates and configurations
- [ ] Task: Conductor - User Manual Verification 'Phase 4: Final Verification' (Protocol in workflow.md)
