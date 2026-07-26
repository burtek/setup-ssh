# Setup SSH

Configure SSH for subsequent GitHub Actions workflow steps.

The action:

- creates `~/.ssh` if needed
- stores a private SSH key
- validates the key before use
- configures `known_hosts`
- writes a host entry to `~/.ssh/config`
- optionally tests the connection

## Usage

```yaml
- name: Setup SSH
  uses: dotryw/setup-ssh@v1
  with:
    host: ${{ secrets.SSH_HOST }}
    port: ${{ secrets.SSH_PORT }}
    user: ${{ secrets.SSH_USER }}
    private_key: ${{ secrets.SSH_PRIVATE_KEY }}
    known_hosts: ${{ secrets.SSH_KNOWN_HOSTS }}

- name: Deploy
  run: ssh production hostname
```

---

## Inputs

| Name | Required | Default | Description |
|------|:--------:|:-------:|-------------|
| `host` | ✅ | | SSH hostname or IP address |
| `user` | ✅ | | SSH username |
| `private_key` | ✅ | | Contents of the private SSH key |
| `port` | | `22` | SSH port |
| `known_hosts` | | | `known_hosts` entry. If omitted, the action uses `ssh-keyscan`. |
| `key_filename` | | `id_key` | Filename for the private key inside `~/.ssh` |
| `alias` | | `production` | Host alias written to `~/.ssh/config` |
| `strict_host_key_checking` | | `yes` | Value for `StrictHostKeyChecking` |
| `test_connection` | | `false` | Test the SSH connection after setup |

---

## Example: Multiple hosts

```yaml
- uses: dotryw/setup-ssh@v1
  with:
    alias: production
    key_filename: production_key
    host: ${{ secrets.PRODUCTION_HOST }}
    user: deploy
    private_key: ${{ secrets.PRODUCTION_KEY }}

- uses: dotryw/setup-ssh@v1
  with:
    alias: backup
    key_filename: backup_key
    host: ${{ secrets.BACKUP_HOST }}
    user: backup
    private_key: ${{ secrets.BACKUP_KEY }}

- run: ssh production hostname
- run: ssh backup hostname
```

---

## Security

This action:

- validates the supplied private key before use
- uses secure file permissions
- enables `StrictHostKeyChecking` by default
- never prints the private key

If `known_hosts` is omitted, the action retrieves the host key using `ssh-keyscan`. For the highest level of security, provide a trusted `known_hosts` entry instead.

---

## License

MIT