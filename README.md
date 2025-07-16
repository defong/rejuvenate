# Development Environment Setup

This Ansible playbook sets up a local development environment using Podman Quadlet with:
- PostgreSQL database
- pgAdmin web interface
- Dashy dashboard
- Caddy reverse proxy (running as user service)

## Prerequisites

- Arch Linux (uses `pacman`)
- Ansible installed
- User with sudo privileges

## Usage

1. Run the playbook:
   ```bash
   ansible-playbook -i inventory.yml playbook.yml -K
   ```

   Or with custom variables:
   ```bash
   ansible-playbook -i inventory.yml playbook.yml -K -e @custom-vars.yml
   ```

2. Access services:
   - **Dashboard**: http://dashy.localhost
   - **pgAdmin**: http://pgadmin.localhost
   - **PostgreSQL**: localhost:5432 (direct connection)

## Default Credentials

- **pgAdmin**: admin@test.com / admin
- **PostgreSQL**: postgres / postgres / postgres

## Key Improvements

- **Native User Caddy**: Caddy runs as a native user service, not containerized
- **Cleaner Structure**: Used loops to reduce repetition
- **Better Organization**: Configuration files organized under `~/.config/`
- **Simplified Network**: All services in one pod with proper port mapping
- **Standard Ports**: Web services accessible on standard ports (80)

## Service Management

All services run as user services and can be managed with:

```bash
# Check status
systemctl --user status dev-pod.service

# Stop/start individual services
systemctl --user stop postgres.service
systemctl --user start postgres.service

# Stop all services
systemctl --user stop dev-pod.service
```

## File Structure

```
~/.config/
├── containers/systemd/     # Quadlet files
│   ├── dev.pod
│   ├── postgres.container
│   ├── pgadmin.container
│   └── dashy.container
└── systemd/user/           # User systemd services
    └── caddy.service

~/.local/share/caddy/       # Caddy configuration
├── Caddyfile
└── dashy-config.yml
```
