## Keep a file or directory in sync with another ansible-managed host

This role is intended to copy SSL-certificates generated on another host to the current host
using `scp` and keep it in sync by adding a cronjob. It is not limited to SSL certificates and
may well be used to copy any other file or directory.

### These variables must be set in order to use this role

* `sync_source_host_ansible`: The other host from which the file or directory should be copied. This name must be known by ansible.
* `sync_source_host`: Source IP address or host name under which the other host in reachable from the currently processed host.
* `sync_source`: Full path to the file or directory to be synced.
* `sync_target_dir`: Target directory under which the basename of `sync_source` will be created.

### What this role does

1. Create a dedicated SSH key for this job.
2. Add this key to the root user's `authorized_keys` file on `sync_source_host_ansible`, making sure that you are only allowed to read `sync_source` when using this key.
3. Run the copy job initially.
4. Add a cronjob that runs the copy job once every hour.

### Example usage

```yaml
- hosts: mail
  vars:
    sync_source_host_ansible: my_other_host
    sync_source_host: 192.168.1.42
    sync_source: /etc/certs/example.com
    sync_target_dir: /srv/certs
  roles:
    - { role: rompe.sync_dir_from_other_host, tags: "sync_dir" }
```

After running this, `/srv/certs/example.com` will be in place and kept current every hour.

### Restrictions

Using `scp` is simple and works without any special prerequites However, if you need to transfer
huge directories, you might be better off using an rsync based solution to save network traffic.

### License

Licensed under the MIT License. See the LICENSE file for details.

### Author information

Created by Ulf Rompe in 2018.
