# ec2-snap-db

Wrapper script for [`ec2-consistent-snapshot`](https://github.com/alestic/ec2-consistent-snapshot)
that allows volume ids to be determined on the fly based on mount points in a
configuration file (defaulting to `/etc/default/ec2-snap-db`).

Supports LVM and RAID devices in addition to normal block devices.

Requires installation of `ec2-consistent-snapshot` and the
[Amazon EC2 API Tools](https://aws.amazon.com/developertools/351) in the
`PATH`.

## Installation
Copy the files `ec2-instance-env`, `ec2-snap-db`, and `ec2-volumes-for-mount`
to your directory of choice. Ensure that they have executable permissions for
the user who will be running the process (0755 with root ownership should be
fine). All three files must be installed in the same directory.

You may find it convenient to install these files in the same directory as
either `ec2-consistent-snapshot` or the Amazon EC2 API Tools (e.g.
`/opt/aws/bin`) to avoid having to update the `PATH`.

## Usage

To run `ec2-snap-db` for testing purposes, execute it as follows:

```
ec2-snap-db --verbose --config /path/to/config
```

If the command is not run as root, it will attempt to use `sudo` to execute
`ec2-consistent-snapshot`.

When the `--config` parameter is not passed, the default configuration file at
`/etc/default/ec2-snap-db` will be used (if it exists).

To run from cron, copy the file [`examples/ec2-snap-db.cron`](examples/ec2-snap-db.cron) to
`/etc/cron.d/ec2-snap-db` and edit it to meet your needs.

## Configuration

The configuration file for `ec2-snap-db` is a standard shell script. By
default, it will be loaded from `/etc/default/ec2-snap-db`. The config file is
expected to set the following environment variables:

* The `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY` variables are used to call
  `ec2-consistent-snapshot` with the appropriate access credentials. They are
  also translated into `AWS_ACCESS_KEY` and `AWS_SECRET_KEY` for the calls to
  `ec2-describe-volumes` and `ec2-describe-tags`. These credentials must allow
  the `ec2:CreateSnapshot`, `ec2:DescribeTags`, and `ec2:DescribeVolumes` actions
  to be executed for the instance on which `ec2-snap-db` is run.

* The `SNAP_DB_MOUNTS` variable is an array containing the list of mount points to
  snapshot.

* The `SNAP_DB_OPTIONS` variable is an array containing additional options to be
  passed to `ec2-consistent-snapshot`, e.g. MySQL or Mongo-related parameters.

* The `SNAP_DB_PREFIX` variable controls the prefix used for the snapshot
  description. The default value is the value of the instance's _Name_ tag or
  instance id. If the value contains a single '%s', the instance name will be
  subsituted into that position in the prefix.

A sample configuration file can be found at [`examples/ec2-snap-db.default`](examples/ec2-snap-db.default).

__NOTE__: Your configuration file will contain AWS secrets, so its permissions
should not allow other users to access the file. Owner-only permissions (e.g.
0600) are recommended.
