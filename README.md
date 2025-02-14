# SSHD

Minimal Alpine Linux Docker image with `sshd` exposed and `rsync` installed.

Mount your .ssh credentials (RSA public keys) at `/root/.ssh/` in order to
access the container via root and set `SSH_ENABLE_ROOT=true` or mount each user's key in
`/etc/authorized_keys/<username>` and set `SSH_USERS` config to create user accounts (see below).

Optionally mount a custom sshd config at `/etc/ssh/`.

## Environment Options

- `SSH_USERS` list of user accounts and uids/gids to create. eg `SSH_USERS=www:48:48,admin:1000:1000`
- `SSH_ENABLE_ROOT` if "true" unlock the root account
- `MOTD` change the login message
- `SFTP_MODE` if "true" sshd will only accept sftp connections
- `SFTP_CHROOT` if in sftp only mode sftp will be chrooted to this directory. Default "/data"
- `GATEWAY_PORTS` if "true" sshd will allow gateway ports (port forwardings not bound to the loopback address)

## SSH Host Keys

SSH uses host keys to identity the server you are connecting to. To avoid receiving security warning the containers host keys should be mounted on an external volume.

By default this image will create new host keys in `/etc/ssh/keys` which should be mounted on an external volume. If you are using existing keys and they are mounted in `/etc/ssh` this image will use the default host key location making this image compatible with existing setups.

If you wish to configure SSH entirely with environment variables it is suggested that you externally mount `/etc/ssh/keys` instead of `/etc/ssh`.

## SFTP mode

When in sftp only mode (activated by setting `SFTP_MODE=true` the container will only accept sftp connections. All sftp actions will be chrooted to the `SFTP_CHROOT` directory which defaults to "/data".

Please note that all components of the pathname in the ChrootDirectory directive must be root-owned directories that are not writable by any other user or group (see man 5 sshd_config).

## Usage Example

```
docker run -d -p 2222:22 -v /secrets/id_rsa.pub:/root/.ssh/authorized_keys -v /mnt/data/:/data/ -e SSH_ENABLE_ROOT=true docker.io/panubo/sshd:1.0.3
```

or

```
docker run -d -p 2222:22 -v $(pwd)/.ssh/id_rsa.pub:/etc/authorized_keys/www -e SSH_USERS="www:48:48" docker.io/panubo/sshd:1.0.3
```

or use the provided docker-compose example file.

## Status

Production ready and stable.
