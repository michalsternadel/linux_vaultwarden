# Ansible Role: Linux Vaultwarden (Native Binary)

[![License: AGPL v3](https://img.shields.io/badge/License-AGPL%20v3-blue.svg)](https://opensource.org/licenses/AGPL-3.0)
[![Ansible Role](https://img.shields.io/badge/ansible--role-linux--vaultwarden-orange)](https://galaxy.ansible.com/)

An Ansible role that installs and configures **Vaultwarden** (formerly Bitwarden_RS) as a native systemd service using the compiled binary extracted from Official Docker images. This role is designed specifically for **non-containerized** deployments on Debian-family Linux distributions (Debian, Ubuntu).

## Description

While the official Vaultwarden project focuses on Docker, this role provides a clean, native installation. It is ideal for environments where Docker is not available or where a minimal footprint is required.

**Key Features:**
* Sets up a dedicated `vaultwarden` system user and group.
* Installs all required system dependencies (libssl, sqlite3, etc.).
* Deploys the Vaultwarden binary and the official Web Vault files.
* Configures environment via `/etc/vaultwarden/vaultwarden.conf`.
* Creates and manages a **systemd unit** for automatic startup and logging.



## Requirements

* **OS Family**: Debian (Debian 12/13, Ubuntu 22.04/24.04).
* **Architecture**: The role assumes an x86_64 architecture for binary compatibility but should work with arm64 as well.
* **SSL/TLS**: Vaultwarden **requires** HTTPS for most browser features to work. This role installs the backend service using SSL, but you should rather use a reverse proxy like Nginx, Apache, or Caddy to handle SSL termination.

## Role Variables

Settable variables (found in `defaults/main.yml`):

| Variable | Default Value | Description |
| :--- | :--- | :--- |
| `linux_vaultwarden_user`| `vaultwarden`| System user running the service. |
| `linux_vaultwarden_ssl_user_group`| `ssl-cert`| Additional group or proper SSL key permissions. |
| `linux_vaultwarden_app_directory`| `/usr/bin`| Directory, where vaultvarden binary will be installed. |
| `linux_vaultwarden_home_directory`| `/usr/lib/vaultwarden`| Directory for data, Web-Vault. |
| `linux_vaultwarden_config_directory`| `/etc/vaultwarden`| Directory, where vaultwarden.conf will be stored. |
| `linux_vaultwarden_log_directory`| `/var/log/vaultwarden`| Directory for logs. |
| `linux_vaultwarden_ssl`| `true`| If true Vaultwarden will be configured to use SSL Certificates |
| `linux_vaultwarden_ssl_source`| `selfsigned` | Could be: selfsigned/wildcard.example.org (if wildcard.example.org - role wants wildard_example_org_chain and example_org_key variables )`|
| `linux_vaultwarden_wildcard_example_org_chain`| `""`| Only used if linux_vaultwarden_ssl_source is set to wildcard_example_org |
| `linux_vaultwarden_wildcard_example_org_key`| `""`| Only used if linux_vaultwarden_ssl_source is set to wildcard_example_org |
| `linux_vaultwarden_ssl_certificate_location`| `/etc/ssl/certs`| Location to store certificate. |
| `linux_vaultwarden_ssl_certificate_key_location`| `/etc/ssl/private`| Location to store certificate key. |
| `linux_vaultwarden_configure`| `true`| Flag to configure Vaultwarden. If set to true - vaultwarden.conf will be replaced. |
| `linux_vaultwarden_database_url`| `"{{ linux_vaultwarden_home_directory }}/data/db.sqlite3"`| Could also be  `postgresql://vaultwarden:yourpassword@localhost:5432/vaultwarden` or `mysql://vaultwarden:yourpassword@localhost/vaultwarden` |
| `linux_vaultwarden_websocket_enabled`| `false`| Should websocket be enabled? |
| `linux_vaultwarden_websocket_address`| `0.0.0.0`| Websocket address. |
| `linux_vaultwarden_websocket_port`| `3012`| Websocket port. |
| `linux_vaultwarden_push_enabled`| `false`| Should push be enabled? |
| `linux_vaultwarden_push_id`| `CHANGEME`| Push id. |
| `linux_vaultwarden_push_key`| `CHANGEME`| Push key. |
| `linux_vaultwarden_sends_allowed`| `false`| Should sends be allowed? |
| `linux_vaultwarden_signups_allowed`| `true`| Should signups be allowed?  |
| `linux_vaultwarden_signups_verify`| `false`| Should signups verification be enabled? |
| `linux_vaultwarden_invitations_allowed`| `false`| Should invitation be allowed? |
| `linux_vaultwarden_invitations_org_name`| `Snakeoil`| Invitation organization name. |
| `linux_vaultwarden_email_change_allowed`| `false`| Should user can change email?  |
| `linux_vaultwarden_log_level`| `error`| Log level. |
| `linux_vaultwarden_admin_token`| `""` | If you want you can generate on using Argon2 PHC. If set admin panel will be enabled.|
| `linux_vaultwarden_signups_domains_whitelist`| `[localhost, localhost.localdoimain, localdomain]` | Signups are allowed domains. |
| `linux_vaultwarden_org_creation_users`| `[admin@localhost.localdomain, admin@localhost]` | Users that can create an organizations. |
| `linux_vaultwarden_domain`| `"{{ ansible_fqdn }}"` | Vaultwarden domain. |
| `linux_vaultwarden_listen_address`| `0.0.0.0`| Listen address. |
| `linux_vaultwarden_listen_port`| `8443`| Listen port. (Make sure it is higher than 1024 or use proper CAP.) |


## Dependencies

None. This role does not require Docker or any other Ansible roles.

## Example Playbook

```yaml
- hosts: vault_servers
  become: true
  roles:
    - role: linux_vaultwarden

## Example usage

````
ansible-playbook -i inventory ./site.yml
