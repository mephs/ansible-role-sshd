# sshd

This Ansible role helps you manage SSHD configurations on your servers easily and efficiently.

All [sshd_config](<https://man.freebsd.org/cgi/man.cgi?sshd_config(5)>) options are supported.

> [!WARNING]
> Any changes to SSHD configuration can impact server access and security. Always test new configurations in a staging environment before applying them to production servers.

## Requirements

- Ansible 2.9 or higher
- SSHD installed on target systems

## Role Variables

### sshd_files

Primary variable, allows configure multiple sshd config files. Each entry in this variable represents a configuration file.

### sshd_cleanup_enabled

Set to `true` to cleanup old configuration files. The default is `false`.

### sshd_reload_service

Set to `false` to disable reload of the sshd service. The default is `true`.

## Dependencies

This role does not have any dependencies.

## Example Playbook

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

    - hosts: servers
      roles:
         - { role: username.rolename, x: 42 }

## License

MIT

## Author Information

Created and maintained by Mikhail Vorontsov (@mephs) <mvorontsov@tuta.io>
