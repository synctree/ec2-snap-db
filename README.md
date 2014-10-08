# ec2-snap-db

Wrapper script for [ec2-consistent-snapshot](https://github.com/alestic/ec2-consistent-snapshot)
that allows volume ids to be determined on the fly based on mount points in a
configuration file (defaulting to `/etc/default/ec2-snap-db`).

Supports LVM and RAID devices in addition to normal block devices.

## Usage

To test `ec2-snap-db` with a temporary config file, run it as follows:

```
ec2-snap-db --config /path/to/config
```

If the command is not run as root, it will attempt to use `sudo` to execute
`ec2-consistent-snapshot`.

When the `--config` parameter is not passed, the default configuration file at
`/etc/default/ec2-snap-db` will be used.

To run from cron, copy the file `examples/ec2-snap-db.cron` to
`/etc/cron.d/ec2-snap-db` and edit it to meet your needs.
