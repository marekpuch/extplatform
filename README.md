# External OVH VPS Ansible Platform

This repository manages the external OVH VPS at `57.128.245.174` as user `ubuntu`.

The playbook enforces:

- SSH public key authentication only
- Password-based SSH login disabled
- Fail2ban with a basic SSH jail enabled
- Docker Engine and Docker Compose plugin installed

## Repository layout

- `inventory/hosts.yml`: target hosts
- `inventory/group_vars/all.yml`: global variables, including SSH public keys
- `inventory/host_vars/ext_ovh_vps.yml`: host-specific variables
- `playbooks/site.yml`: main entrypoint
- `playbooks/openresty.yml`: deploy the OpenResty basic-auth compose stack
- `playbooks/observability.yml`: deploy the Prometheus, Loki, and Grafana monitoring stack
- `playbooks/postgresql.yml`: deploy the PostgreSQL database stack
- `playbooks/wireguard.yml`: configure the WireGuard VPN server
- `roles/ssh_hardening`: SSH key management and password-login hardening
- `roles/fail2ban`: basic SSH brute-force protection
- `roles/docker`: Docker installation and service setup
- `roles/postgresql_compose`: deploy PostgreSQL in a private 'internal' network
- `roles/cloudflared_tunnel`: deploy a Cloudflare Tunnel for secure access
- `roles/openresty_compose`: deploy the OpenResty compose service
- `roles/observability_compose`: deploy the metrics and logging stack
- `roles/wireguard_server`: install and configure the WireGuard VPN server

## Before first run

Add at least one SSH public key and a Cloudflare Tunnel token to the inventory.

Example `inventory/group_vars/all.yml`:
```yaml
ubuntu_authorized_keys:
  - "ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAA... your-key-comment"
```

Example `inventory/host_vars/ext_ovh_vps.yml`:
```yaml
openresty_cloudflare_tunnel_token: "your-tunnel-token-here"
openresty_basic_auth_users:
  - "user:$apr1$..."
postgresql_password: "your-database-password-here"
```

**Note on Cloudflare Tunnel**: Ensure that in the Cloudflare Dashboard, the tunnel's Public Hostname is configured to point to `http://openresty:80`.

If the server still accepts password authentication for the first bootstrap run, you can connect with:

```bash
ansible-playbook playbooks/site.yml -k
```

After the playbook completes, password-based SSH logins will be disabled.

## Usage

Install the required Ansible collection:

```bash
ansible-galaxy collection install -r collections/requirements.yml
```

Run the full configuration:

```bash
ansible-playbook playbooks/site.yml
```

Deploy the OpenResty compose stack:

```bash
ansible-playbook playbooks/openresty.yml
```

Deploy the Observability stack (Prometheus, Loki, Grafana):

```bash
ansible-playbook playbooks/observability.yml
```

Configure the WireGuard VPN server:

```bash
ansible-playbook playbooks/wireguard.yml
```

Docker Compose projects are deployed under `/opt/compose/`.

Before running the WireGuard playbook, define peer entries in `inventory/host_vars/ext_ovh_vps.yml`.

Validate connectivity:

```bash
ansible all -m ping
```
