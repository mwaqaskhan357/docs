---
description: Describes how to use the local logging driver.
keywords: local, docker, logging, driver
title: Local File logging driver
aliases:
- /engine/reference/logging/local/
- /engine/admin/logging/local/
---

The `local` logging driver captures output from container's stdout/stderr and
writes them to an internal storage that is optimized for performance and disk
use.

By default, the `local` driver preserves 100MB of log messages per container and
uses automatic compression to reduce the size on disk. The 100MB default value is based on a 20M default size 
for each file and a default count of 5 for the number of such files (to account for log rotation).

> *Note*
>
> The `local` logging driver uses file-based storage. The file-format and
> storage mechanism are designed to be exclusively accessed by the Docker
> daemon, and should not be used by external tools as the implementation may
> change in future releases.

## Usage

To use the `local` driver as the default logging driver, set the `log-driver`
and `log-opt` keys to appropriate values in the `daemon.json` file, which is
located in `/etc/docker/` on Linux hosts or
`C:\ProgramData\docker\config\daemon.json` on Windows Server. For more about
configuring Docker using `daemon.json`, see
[daemon.json](../../../engine/reference/commandline/dockerd.md#daemon-configuration-file).

The following example sets the log driver to `local` and sets the `max-size`
option.

```json
{
  "log-driver": "local",
  "log-opts": {
    "max-size": "10m"
  }
}
```

Restart Docker for the changes to take effect for newly created containers. Existing containers do not use the new logging configuration.

You can set the logging driver for a specific container by using the
`--log-driver` flag to `docker container create` or `docker run`:

```console
$ docker run \
      --log-driver local --log-opt max-size=10m \
      alpine echo hello world
```

Note that `local` is a bash reserved keyword, so you may need to quote it in scripts.

### Options

The `local` logging driver supports the following logging options:

| Option      | Description                                                                                                                                                   | Example  value             |
|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------|:---------------------------|
| `max-size`  | The maximum size of the log before it is rolled. A positive integer plus a modifier representing the unit of measure (`k`, `m`, or `g`). Defaults to 20m.     | `--log-opt max-size=10m`   |
| `max-file`  | The maximum number of log files that can be present. If rolling the logs creates excess files, the oldest file is removed. A positive integer. Defaults to 5. | `--log-opt max-file=3`     |
| `compress`  | Toggle compression of rotated log files. Enabled by default.                                                                                                  | `--log-opt compress=false` |

### Examples

This example starts an `alpine` container which can have a maximum of 3 log
files no larger than 10 megabytes each.

```console
$ docker run -it --log-driver local --log-opt max-size=10m --log-opt max-file=3 alpine ash
```