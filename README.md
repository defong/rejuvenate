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
├── inventory.yml               # Ansible inventory
├── README.md                   # This file (auto-generated)
├── tasks/
│   ├── install_packages.yml    # Package installation
│   ├── setup_directories.yml   # Directory creation
│   ├── create_quadlets.yml     # Podman quadlet files
│   ├── setup_caddy.yml         # Caddy configuration
│   ├── configure_services.yml  # Service management
│   ├── display_info.yml        # Information display
│   └── generate_readme.yml     # README generation
├── templates/
│   ├── dev.pod.j2              # Pod configuration
│   ├── postgres.container.j2   # PostgreSQL container
│   ├── pgadmin.container.j2    # pgAdmin container
│   ├── dashy.container.j2      # Dashy container
│   ├── dashy-config.yml.j2     # Dashy dashboard config
│   ├── Caddyfile.j2            # Caddy reverse proxy config
│   ├── caddy.service.j2        # Caddy systemd service
│   └── README.md.j2            # README template
└── handlers/
    └── main.yml                # Ansible handlers
```

## Usage

### Basic Usage

Run the complete playbook:
```bash
ansible-playbook site.yml -K
```

With custom variables:
```bash
ansible-playbook site.yml -K -e @custom-vars.yml
```

### Tagged Execution

Run specific components using tags:

```bash
# Install packages only
ansible-playbook site.yml -K --tags "packages"

# Setup configuration only
ansible-playbook site.yml -K --tags "config"

# Start services only
ansible-playbook site.yml -K --tags "start"

# Setup Caddy components only
ansible-playbook site.yml -K --tags "caddy"

# Database-related tasks only
ansible-playbook site.yml -K --tags "database"

# Skip service starting
ansible-playbook site.yml -K --skip-tags "start"

# Multiple tags
ansible-playbook site.yml -K --tags "packages,directories,quadlets"
```

### Service Access

After successful deployment, access services at:
- **Dashboard**: http://dashy.localhost
- **pgAdmin**: http://pgadmin.localhost
- **Dashboard**: http://syncthing.localhost
- **PostgreSQL**: localhost:5432 (direct connection)

## Available Tags

### Functional Tags (by what they do)
- `packages` - Install system packages
- `directories` - Create config directories
- `quadlets` - Create all quadlet files
- `config` - All configuration tasks
- `services` - Service management tasks
- `start` - Start services
- `info` - Display information

### Component Tags (by what they affect)
- `install` - Installation tasks
- `setup` - Initial setup tasks
- `podman` - Podman-related tasks
- `caddy` - Caddy-related tasks
- `systemd` - Systemd-related tasks
- `database` - Database container tasks
- `containers` - Container-related tasks
- `pod` - Pod-related tasks
- `proxy` - Reverse proxy tasks
- `dashboard` - Dashboard tasks
- `hosts` - /etc/hosts modification
- `network` - Network-related tasks
- `system` - System-level tasks

### Special Tags
- `never` - Tasks that only run when explicitly called
- `reload` - Systemd daemon reload
- `wait` - Wait/pause tasks
- `display` - Display output tasks

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
- **Tag-based Execution**: Granular control over playbook execution
- **Auto-generated Documentation**: README is generated from template

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

# View logs
journalctl --user -u dev-pod.service -f
```

## Configuration Files Location

```
/home/defong/.config/
├── containers/systemd/     # Quadlet files
│   ├── dev.pod
│   ├── postgres.container
│   ├── pgadmin.container
│   └── dashy.container
└── systemd/user/           # User systemd services
    └── caddy.service

/home/defong/.local/share/caddy/       # Caddy configuration
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

## Troubleshooting

### Common Issues

1. **Services not starting**: Check systemd status and logs
   ```bash
   systemctl --user status dev-pod.service
   journalctl --user -u dev-pod.service
   ```

2. **Port conflicts**: Modify ports in `vars.yml` if default ports are in use

3. **Hostname resolution**: Ensure /etc/hosts entries were added correctly
   ```bash
   grep localhost /etc/hosts
   ```

4. **Caddy configuration**: Check Caddyfile syntax
   ```bash
   caddy validate --config /home/defong/.local/share/caddy/Caddyfile
   ```

### Cleanup

To remove all created resources:
```bash
# Stop services
systemctl --user stop dev-pod.service caddy.service

# Disable services
systemctl --user disable dev-pod.service caddy.service

# Remove configuration files
rm -rf /home/defong/.config/containers/systemd/dev.pod
rm -rf /home/defong/.config/containers/systemd/*.container
rm -rf /home/defong/.config/systemd/user/caddy.service
rm -rf /home/defong/.local/share/caddy/

# Remove /etc/hosts entries (manual)
sudo sed -i '/dashy.localhost\|pgadmin.localhost\|syncthing.localhost/d' /etc/hosts
```

## Generated Documentation

This README is automatically generated by the playbook. To regenerate:
```bash
ansible-playbook site.yml -K --tags "info"
```

---
*Last generated: 2025-07-26T12:58:08Z*
*Project path: /home/defong/Repos/Personal/rejuvenate*

