# sshd

This Ansible role helps you manage SSHD configurations on your servers easily and efficiently.

All [sshd_config](<https://man.freebsd.org/cgi/man.cgi?sshd_config(5)>) options are supported.

> [!CAUTION]
> Any changes to SSHD configuration can impact server access and security. Always test new configurations in a staging environment before applying them to production servers.

> [!IMPORTANT]
> This role does not configure the firewall or selinux, so if you don't want to lose access to your server, use other roles to achieve it.

## Requirements

- Ansible 2.9 or higher
- SSHD installed on target systems

## Role Variables

### sshd_files

Primary variable, allows configure multiple sshd config files. Each entry in this variable represents a configuration file.

Keyword values `yes` and `no` are used as Ansible boolean values.

You can specify a list of keyword values to create multiple lines for the same keyword.

```yaml
sshd_files:
  00-example:
    ListenAddress:
      - 0.0.0.0
      - ::
```

This will be templated in:

```conf
ListenAddress 0.0.0.0
ListenAddress ::
```

If keyword values must be separated by a comma, you can specify them as a list or string.

```yaml
sshd_files:
  00-example:
    Ciphers:
      - aes256-ctr
      - aes256-gcm@openssh.com
      - chacha20-poly1305@openssh.com
    HostKeyAlgorithms: ssh-ed25519,rsa-sha2-512,rsa-sha2-256
```

This will be templated in:

```conf
Ciphers aes256-ctr,aes256-gcm@openssh.com,chacha20-poly1305@openssh.com
HostKeyAlgorithms ssh-ed25519,rsa-sha2-512,rsa-sha2-256
```

To use the `Match` block, format it as shown below. You can provide a list for the `value` field if you want the result to be a comma-separated string.

```yaml
sshd_files:
  00-example:
    Match:
      - criteria: Address
        value:
          - 192.0.2.0/24
          - 192.0.3.0/24
        keywords:
          PermitRootLogin: without-password
      - criteria: Group
        value: admins
        keywords:
          PermitRootLogin: without-password
          PasswordAuthentication: true
```

This will be templated in:

```conf
Match Address 192.0.2.0/24,192.0.3.0/24
  PermitRootLogin without-password

Match Group admins
  PermitRootLogin without-password
  PasswordAuthentication yes
```

### sshd_cleanup_enabled

Set to `true` to cleanup old configuration files. The default is `false`.

### sshd_reload_service

Set to `false` to disable reload of the sshd service. The default is `true`.

## Dependencies

This role does not have any dependencies.

## Example Playbook

```yaml
- hosts: all
  become: true
  gather_facts: false
  vars:
    sshd_files:
      10-basic-settings:
        PermitRootLogin: false
        PasswordAuthentication: true
        PubkeyAuthentication: true
      20-network-settings:
        Port: 2222
        ListenAddress:
          - 0.0.0.0
          - ::
      30-security-settings:
        AllowUsers:
          - alice
          - bob
        AllowGroups: admin,developers
      40-advanced-settings:
        MaxSessions: 10
        Ciphers: aes256-ctr,aes192-ctr,aes128-ctr
        MACs:
          - hmac-sha2-256
          - hmac-sha2-512
      50-match-settings:
        Match:
          - criteria: Address
            value:
              - 192.0.2.0/24
              - 192.0.3.0/24
            keywords:
              PermitRootLogin: without-password
          - criteria: Group
            value: admins
            keywords:
              PermitRootLogin: without-password
              PasswordAuthentication: true
  roles:
    - mephs.sshd
```

This will create 5 files on the system with the following contents:

```conf
PermitRootLogin no
PasswordAuthentication yes
PubkeyAuthentication yes
```

```conf
Port 2222
ListenAddress 0.0.0.0
ListenAddress ::
```

```conf
AllowUsers alice,bob
AllowGroups admin,developers
```

```conf
MaxSessions 10
Ciphers aes256-ctr,aes192-ctr,aes128-ctr
MACs hmac-sha2-256,hmac-sha2-512
```

```conf
Match Address 192.0.2.0/24,192.0.3.0/24
  PermitRootLogin without-password

Match Group admins
  PermitRootLogin without-password
  PasswordAuthentication true
```

## License

MIT

## Author Information

Created and maintained by Mikhail Vorontsov (@mephs) <mvorontsov@tuta.io>
