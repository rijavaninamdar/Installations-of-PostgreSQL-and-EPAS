# Navigating PostgreSQL Control: `pg_ctl` vs `systemctl`

## Introduction

A novice user might wonder what the best way to manage the PostgreSQL service on Linux is. The two most frequently used tools for managing the PostgreSQL database service are `pg_ctl` and `systemctl`, but they serve different purposes and are used in different contexts.

This article dissects the differences between these tools, providing insights into when and how to use each for effective PostgreSQL management.

## Root Cause

`pg_ctl` and `systemctl` operate at different scopes.

### `pg_ctl`

- A command-line utility provided by PostgreSQL itself, used to start, stop, restart, and control the PostgreSQL database cluster directly.
- Typically used when fine-grained control over the database service is needed, or when actions need to be performed directly on the PostgreSQL server process.
- Commonly used by database administrators and experienced users who need to manage the PostgreSQL cluster at a low level.
- Common use cases include:
  - Starting the cluster/instance for troubleshooting
  - Stopping the cluster/instance using the `fast` option (the most common case)
  - Performing an `immediate` shutdown in emergencies

### `systemctl`

- A general-purpose command used to manage services on Linux systems that use the `systemd` init system (the case for most modern Linux distributions). `systemctl` is essentially a tool that talks to `systemd`.
- Used to start, stop, restart, enable, and disable services — including PostgreSQL — as part of the operating system's overall service management.
- Provides a standardized, consistent way to manage services across different Linux distributions, simplifying system-level administration.
- Used by system administrators to control various services on the system, not just PostgreSQL.
- Allows PostgreSQL to be enabled/disabled and configured to start automatically on system boot.
- Its features can be leveraged to view logs and monitor service status.
- `systemctl` often executes `pg_ctl` in the background.

> **Important:** `systemctl` will not be aware that PostgreSQL is running if it was started using `pg_ctl` outside of the systemd service. Once a cluster is started using `systemctl`, it can be stopped using `pg_ctl` — but the reverse is not true.

## Usage

### Using `pg_ctl` directly

```bash
pg_ctl -D /path/to/data_directory start
pg_ctl -D /path/to/data_directory stop
pg_ctl -D /path/to/data_directory restart
pg_ctl -D /path/to/data_directory reload
```

### Using `systemctl`

```bash
systemctl start postgresql
systemctl stop postgresql
systemctl restart postgresql
systemctl status postgresql
systemctl enable postgresql
systemctl disable postgresql
```

> **Note:** Depending on the Linux distribution, the exact commands used to manage the PostgreSQL service may vary.

## Examples

**RedHat family (including CentOS)** — typical `systemd` service file content:

```ini
ExecStart=/usr/lib/postgresql/15/bin/postmaster -D ${PGDATA}
ExecStop=/usr/bin/kill $KILLLEVEL $(pidoff $NAME)
ExecReload=/bin/kill -HUP $MAINPID
```

**Debian family (including Ubuntu)** — typical `systemd` service file content, using `pg_ctl`:

```ini
ExecStart=/usr/local/pgsql/bin/pg_ctl start -D ${PGDATA} -s
ExecStop=/usr/local/pgsql/bin/pg_ctl stop -D ${PGDATA} -s -m fast
ExecReload=/usr/local/pgsql/bin/pg_ctl reload -D ${PGDATA} -s
```

> Ideally, service files should contain more configuration detail, and the exact commands shown may not match your installation precisely. The key distinction is that the RedHat family typically uses `postmaster` (a symlink to `postgres`) along with other OS-level commands, while Debian/Ubuntu relies on the PostgreSQL `pg_ctl` utility.

## Key Differences

| Aspect | `pg_ctl` | `systemctl` |
|--------|----------|-------------|
| **System Integration** | PostgreSQL-specific; deals mainly with PostgreSQL processes | General-purpose Linux service manager; standardized across services |
| **Logging & Output** | Shows PostgreSQL's console output directly | Redirects output to system logs, suited for centralized log management |

In general: **`pg_ctl`** is used for direct, low-level control over the PostgreSQL server process, while **`systemctl`** is used to manage the PostgreSQL service as part of overall system management.

> **Note:** Specific commands and options may vary depending on your PostgreSQL installation and Linux distribution.

## Applicable Versions

| Component | Versions |
|-----------|----------|
| PostgreSQL | 10, 11, 12, 13, 14, 15, 16, 17 |
