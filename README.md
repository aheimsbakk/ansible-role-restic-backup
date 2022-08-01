# Restic backup

[restic]: https://restic.net/
[rclone]: https://rclone.org/

This role helps orchestrate a [restic][] backup system. Configures both clients and destination server. [restic][] and [rclone][] will be downloaded from [Github](https://github.com). Append only mode is default, and avoids that a client can modify its own historical backups.

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

## Versions

* `1.1.0` --- make append only backup configurable.
* `1.0.0` --- initial version.
* `main` --- upstream development version.

## Requirements

A secure bastion server, from where to do Ansible orchestration. The bastion server should have two-factor or hardware token authentication.

### OS compatibility

No direct OS limitation. The Vagrant test environment in `tests` directory uses these OSes.

* Ubuntu Jammy
* Ubuntu Focal
* Alma Linux 8
* Alma Linux 9

### Known caveats

* CentOS 7 had problems with the sysemd service file. It was excluded from test environment since it's rather old.

## Role Variables

Configurable variables for this role. `''` is a reference to an empty string.

* `restic_backup_destination_server` --- **required** inventory hostname of the destination server, no default.
* `restic_backup_destination_address` --- address of destination server, default `{{ ansible_fqdn }}`.
* `restic_backup_destination_user` --- created on first run, default `restic`.
* `restic_backup_destination_path` --- default `/var/backups/restic`.
* `restic_backup_destination_multiple` --- multiple repos with path  
  `{{ restic_backup_destination_path + "/" + inventory_hostname }}`, default `true`.
* `restic_backup_destination_rclone_conf` --- content of configuration file on destination, default:
    ```ini
    [restic]
    type = local
    ```
* `restic_backup_destination_rclone_remote` --- remote to use, default `restic`.
* `restic_backup_destination_rclone_transfers` --- rclone parallel streams, default `2`.
* `restic_backup_source_paths` --- list of paths to backup, default `['/etc']`.
* `restic_backup_source_password` --- password for backup, default `''`.
* `restic_backup_source_append_only` --- force append only backup, default `true`.
* `restic_backup_source_options` --- list of strings with options to add, default `[]`.
* `restic_backup_source_exclude_if_present` --- exclude directory if file is present, default `.restic-ignore`.
* `restic_backup_source_timer` --- when to start backup - see `man systemd.time`, default `*-*-* 00:00:00`.
* `restic_backup_source_timer_delay` --- random start delay, default `6h`.
* `restic_backup_source_timer_accuracy` --- let systemd be flexible, default `12h`.
* `restic_backup_restic_version` --- version of restic to use, default `0.13.1`.
* `restic_backup_rclone_version` --- version of rclone to use, default `v1.59.0`.


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
