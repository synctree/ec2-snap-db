# ec2-snap-db

Wrapper script for [`ec2-consistent-snapshot`](https://github.com/alestic/ec2-consistent-snapshot)
that allows volume ids to be determined on the fly based on mount points in a
configuration file (defaulting to `/etc/default/ec2-snap-db`).

Supports LVM and RAID devices in addition to normal block devices.

Requires installation of `ec2-consistent-snapshot` and the
[Amazon EC2 API Tools](https://aws.amazon.com/developertools/351) in the
`PATH`.

## Usage

To run `ec2-snap-db` for testing purposes, execute it as follows:

```
ec2-snap-db --config /path/to/config
```

If the command is not run as root, it will attempt to use `sudo` to execute
`ec2-consistent-snapshot`.

When the `--config` parameter is not passed, the default configuration file at
`/etc/default/ec2-snap-db` will be used (if it exists).

To run from cron, copy the file [`examples/ec2-snap-db.cron`] to
`/etc/cron.d/ec2-snap-db` and edit it to meet your needs.

## Configuration

The configuration file for `ec2-snap-db` is a standard shell script. By
default, it will be loaded from `/etc/default/ec2-snap-db`. The config file is
expected to set the following environment variables `AWS_ACCESS_KEY_ID`,
`AWS_SECRET_ACCESS_KEY`, `SNAP_DB_MOUNTS`, and `SNAP_DB_OPTIONS`.

A sample configuration file can be found at [`examples/ec2-snap-db.default`].
