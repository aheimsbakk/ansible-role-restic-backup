# Restic backup

[restic]: https://restic.net/
[rclone]: https://rclone.org/

This role helps orcestrate a [restic][] backup system. Configures both clients and destination server.

```plain
               +---------+
               | clients |+
   ansible-   /+---------+|\ one way backup over ssh
   playbook  /  +---------+ \ triggered by systemd.timer
            /                \
+---------+/                  \+-------------+
| bastion |--------------------| destination |
+---------+     ansible-       +-------------+
                playbook
```

## Requirements

A secure bastion server, from where to do Ansible orcestration. The bastion server should have two-factor or hardware token autentication.

## Role Variables

Configurable variables for this role. `''` is a reference to an empty string.

* `restic_backup_destination_server` --- **required** [FQDN](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) of the destination server, no default.
* `restic_backup_destination_path` --- default `/var/backups/restic`.
* `restic_backup_destination_multiple` --- multiple repos named `{{ restic_backup_destination_path + "/" + inventory_hostname }}`, default `true`.
* `restic_backup_destination_user` --- created on first run, default `restic`.
* `restic_backup_source_options` --- restic options to add, default `''`.
* `restic_backup_source_password` --- password for backup, default `''`.
* `restic_backup_source_paths` --- array of paths to backup up, default `['/etc']`.


## Dependencies

* `restic` --- the backup software [restic][].
* `rclone` --- [rclone][] is a feature rich storage interface.

## Example Playbook

```yaml
- hosts: servers
  roles:
    - role: restic_backup
```

## Testing

```bash
cd tests
vagrant up
```

### Rerun role

Run role on all OSes again.

```bash
vagrant provision
```

## License

GPL-2.0-or-later

## Author Information

* Name: [Arnulf Heimsbakk](mailto:arnulf.heimsbakk+ansible@gmail.com)
* Blog: [> /dev/null 2>&1](https://blog.heimsbakk.net/)

<!---
# set vim: spell spelllang=en:
-->
