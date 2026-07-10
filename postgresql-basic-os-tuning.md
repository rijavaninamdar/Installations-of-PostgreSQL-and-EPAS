# PostgreSQL Basic OS Tuning

## Introduction

The performance of your PostgreSQL server depends not only on PostgreSQL's own configuration options — several operating system settings also have a direct impact on PostgreSQL performance.

## Root Cause

Many operating systems ship with default values that were not chosen specifically for database workloads.

## Adjust Swappiness

Many Linux distributions use a default value of `60` for `vm.swappiness`, based on the idea that this offers balanced performance for desktop workloads. For database workloads, the lowest possible non-zero value is preferred instead, to avoid database processes being swapped to disk.

To set `vm.swappiness` to `1`:

```bash
$ sudo sysctl vm.swappiness=1
```

Edit `/etc/sysctl.conf` so the change persists across restarts — set `vm.swappiness` to `1`, or add the line if it isn't already present:

```
vm.swappiness=1
```

> **Note:** Completely disabling swap is not recommended, as it can lead to errors and OOM-killer activity under memory pressure.

## Disable Transparent Huge Pages (THP)

Transparent Huge Pages should be disabled. The way THP is implemented in the Linux kernel (and other OS kernels) can lead to PostgreSQL crashes, as the OOM-killer may terminate PostgreSQL processes interacting with shared memory segments.

By default, THP is enabled in nearly all Linux distributions. Refer to your distribution's documentation for the exact steps to disable it — the procedure differs slightly between RHEL/CentOS, Ubuntu, and other distributions, and via GRUB boot parameters vs. `systemd`.

> The issue is specifically with **Transparent** Huge Pages. We recommend disabling THP and using regular (classic) Huge Pages instead.

## Enable Regular Huge Pages

Using regular (classic) Huge Pages improves PostgreSQL performance and system stability in multiple ways. It's worth understanding *why* Linux HugePages matter so much for database servers — Percona's article, [Why Linux HugePages Are Super Important for Database Servers: A Case with PostgreSQL](https://www.percona.com/blog/why-linux-hugepages-are-super-important-for-database-servers-a-case-with-postgresql/), covers this with real benchmark data and gives detailed instructions on enabling regular 2MB huge pages.

## Recommended Linux Kernel Memory Overcommit Settings for PostgreSQL

PostgreSQL relies heavily on the Linux virtual memory subsystem. The kernel's memory overcommit settings directly influence how PostgreSQL handles memory allocation and how the system behaves under memory exhaustion.

Incorrect overcommit settings are a common cause of:

- OOM-killer terminating PostgreSQL processes
- Unexpected PostgreSQL restarts
- `fork()` failures when creating new backend connections
- Memory allocation errors inside queries

Overcommit matters because PostgreSQL uses `fork()` to start new backend processes. When memory is tight:

**If overcommit is enabled** (`vm.overcommit_memory=0` or `1`):
- Linux may allow memory allocations, but later kill a backend process once memory is actually accessed.
- This forces a full PostgreSQL restart.

**If overcommit is disabled** (`vm.overcommit_memory=2`):
- PostgreSQL receives a clean `ENOMEM` allocation failure instead of being killed outright.
- This prevents ungraceful backend termination and protects database uptime.

For dedicated PostgreSQL servers, disabling overcommit is the much safer behavior.

### Recommended Settings

```
vm.overcommit_memory = 2
vm.overcommit_ratio = 80 or 90
```

Apply at runtime:

```bash
sudo sysctl -w vm.overcommit_memory=2
sudo sysctl -w vm.overcommit_ratio=80
```

Or add to `/etc/sysctl.conf` (or a file under `/etc/sysctl.d/`) to persist across reboots:

```
vm.overcommit_memory=2
vm.overcommit_ratio=80
```

### How Linux Sets Memory Limits

```
CommitLimit = (RAM - HugePages) * (overcommit_ratio / 100) + Swap
```

If system-wide `Committed_AS` approaches `CommitLimit`:

- Allocations may return `ENOMEM`
- PostgreSQL may log errors such as `ERROR: out of memory` / `Failed on request of size X`

This controlled failure mechanism is far safer than letting the kernel kill processes outright.

### Overcommit Mode Comparison

| Mode | Description | PostgreSQL Impact |
|------|-------------|--------------------|
| `0` – heuristic (default) | Weak checks | Higher chance of OOM-killer killing backend processes |
| `1` – always overcommit | No checks at all | Highest OOM risk |
| `2` – never overcommit | Enforces `CommitLimit` | Most stable for database servers |

**Mode `2` is preferred for PostgreSQL** to avoid ungraceful restarts.

### Best Practices

- Monitor `Committed_AS` and `CommitLimit` during peak workloads.
- Ensure `max_connections`, `work_mem`, and parallel worker settings do not collectively exceed a realistic memory budget.

> **Note:** These settings are safe for most PostgreSQL workloads, but they must be validated for your environment. Enabling strict overcommit can expose existing memory pressure issues stemming from high connection counts or overly generous memory parameters. Evaluate memory usage patterns before deploying these settings to production.

## CPU Power Management

Many Linux distributions default to the `ondemand` CPU governor, which throttles CPU frequency down while idle to save power. On a database server, this behavior is undesirable and reduces overall performance. Instead, configure the `performance` governor setting for the CPU.

The exact procedure for changing this setting depends on your operating system distribution and version. Consult your OS documentation for the recommended way to change the governor and make it persist across reboots.

## Filesystem Mount Options

Well-chosen mount options can improve performance:

- Ensure your drives are mounted with `noatime`.
- If the drives sit behind a RAID controller with a proper battery-backed cache, also mount with `nobarrier`.

> **Note:** `nobarrier` should **not** be set when using the XFS filesystem, as XFS always enables a write barrier internally. See [Red Hat's guidance on this](https://access.redhat.com/solutions/5315771) for more detail.

You can remount your drives without a reboot. For example, to remount `/mnt/db/` with these options:

```bash
mount -oremount,rw,noatime,nobarrier /mnt/db
```

Add or edit the corresponding line in `/etc/fstab` for the option to persist across reboots.

## Other I/O Considerations

- **Layout:** Separating OS and data partitions — not just logically, but physically — can improve database performance.
- **RAID level:** Avoid RAID-5 and similar levels, since the checksum calculation required for integrity implies roughly a 4x write penalty. The best performance without compromising redundancy is typically achieved using an advanced controller with a battery-backed cache unit, paired with RAID-10 volumes spanned across multiple disks.
