# Product Guidelines - Ghostship Homelab

## Documentation & Prose Style
- **Concise and Technical:** Documentation must be direct, focusing on facts, commands, and clear explanations. 
- **Code Comments:** Use comments sparingly to explain the "why" for complex logic. Focus on clarity for maintainability.

## Visual Identity & UX
- **Clean and Modern:** The 'Homepage' and 'Muximux' dashboards should follow Material Design principles. 
- **Consistency:** Use high-quality, consistent icons and high-contrast text for all service tiles and links.
- **Minimalist Dashboard:** Prioritize essential status indicators and navigation links to maintain a clutter-free interface.

## Naming Conventions
- **Project Identity:** Use the "Ghostship" prefix for custom administration users, scripts, or internal utilities (e.g., `ghost_admin`).
- **Service Naming:** Container names should use the base name of the software (e.g., `plex`, `sonarr`).

## Configuration & Templating
- **Separation of Concerns:** Keep service-specific logic isolated in its own Ansible template or configuration directory.
- **DRY (Don't Repeat Yourself):** Utilize global variables and shared templates for common configurations (e.g., standard Docker labels, restart policies).
- **Environment Driven:** All configurations that can vary must be driven by environment variables, which must be documented in the README.

## Service Integration
- **Muximux Portal:** Every new service must be integrated into the Muximux portal with a direct link.
- **Homepage Widgets:** Every service must have an appropriately configured widget on the Homepage dashboard to show its health or relevant metrics.
