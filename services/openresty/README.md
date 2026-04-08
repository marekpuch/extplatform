# OpenResty Basic Auth Stack

This stack runs OpenResty on port `80` with HTTP basic authentication enabled.

## Files

- `docker-compose.yml`: container definition
- `conf/default.conf`: OpenResty virtual host
- `auth/users.htpasswd`: htpasswd file to create locally before start
- `html/index.html`: placeholder site content

## Prepare credentials

Create the htpasswd file before starting the stack:

```bash
mkdir -p auth
printf "admin:$(openssl passwd -apr1 'change-me')\n" > auth/users.htpasswd
```

## Start manually

```bash
docker compose up -d
```

This stack is present in the repository only. It is not wired into Ansible deployment.
