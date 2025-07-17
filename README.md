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

## Project Structure

```
ansible-project/
├── site.yml                    # Main playbook
├── vars.yml                    # Variables configuration
├── tasks/
│   ├── install_packages.yml    # Package installation
│   ├── setup_directories.yml   # Directory creation
│   ├── create_quadlets.yml     # Podman quadlet files
│   ├── setup_caddy.yml         # Caddy configuration
│   ├── configure_services.yml  # Service management
│   └── display_info.yml        # Information display
├── templates/
│   ├── dev.pod.j2              # Pod configuration
│   ├── postgres.container.j2   # PostgreSQL container
│   ├── pgadmin.container.j2    # pgAdmin container
│   ├── dashy.container.j2      # Dashy container
│   ├── dashy-config.yml.j2     # Dashy dashboard config
│   ├── Caddyfile.j2            # Caddy reverse proxy config
│   └── caddy.service.j2        # Caddy systemd service
└── handlers/
    └── main.yml                # Ansible handlers
```

## Usage

1. Run the playbook:
   ```bash
   ansible-playbook site.yml -K
   ```

   Or with custom variables:
   ```bash
   ansible-playbook site.yml -K -e @custom-vars.yml
   ```

2. Access services:
   - **Dashboard**: http://dashy.localhost
   - **pgAdmin**: http://pgadmin.localhost
   - **PostgreSQL**: localhost:5432 (direct connection)

## Default Credentials

- **pgAdmin**: admin@test.com / admin
- **PostgreSQL**: postgres / postgres / postgres

## Key Features

- **Modular Design**: Separated tasks for better maintainability
- **Template-based Configuration**: All configs use Jinja2 templates
- **Automated Caddy Formatting**: Runs `caddy fmt --overwrite` after Caddyfile creation
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

# Restart Caddy after configuration changes
systemctl --user restart caddy.service
```

## Configuration Files Location

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
├── Caddyfile               # Auto-formatted by handler
└── dashy-config.yml
```

## Customization

Edit `vars.yml` to customize:
- Database credentials
- Service ports
- Hostnames
- Directory paths

The playbook will automatically format the Caddyfile and reload services as needed.
