# Linux systemd Unit Files Tutorial

In Linux, **unit files** are configuration files used by `systemd` to define and manage system resources like:

- services
- timers
- sockets
- mounts
- devices
- targets

They tell `systemd`:
- what to start
- how to start it
- when to start it
- dependencies
- restart behavior
- permissions
- logging behavior

---

# Simple Definition

A unit file is basically:

```text
a configuration file for systemd
```

---

# Common Unit Types

| Unit Type | Extension | Purpose |
|---|---|
| Service | `.service` | background processes |
| Timer | `.timer` | scheduled jobs |
| Socket | `.socket` | socket activation |
| Mount | `.mount` | filesystem mounts |
| Target | `.target` | boot/runlevel grouping |
| Device | `.device` | hardware devices |
| Path | `.path` | file path monitoring |

---

# Most Common: Service Units

Example:

```text
/etc/systemd/system/nginx.service
```

Controls:
- starting nginx
- stopping nginx
- restarting nginx

---

# Example Unit File

```ini
[Unit]
Description=My Web App
After=network.target

[Service]
Type=simple
User=ubuntu
ExecStart=/usr/bin/python3 /opt/app/app.py
Restart=always

[Install]
WantedBy=multi-user.target
```

---

# Structure of a Unit File

Most unit files contain 3 sections:

| Section | Purpose |
|---|---|
| `[Unit]` | metadata + dependencies |
| `[Service]` | process settings |
| `[Install]` | boot integration |

---

# 1. `[Unit]` Section

Defines:
- description
- dependencies
- startup order

Example:

```ini
[Unit]
Description=Nginx Web Server
After=network.target
```

---

# 2. `[Service]` Section

Defines:
- executable
- user
- restart policy
- environment

Example:

```ini
[Service]
ExecStart=/usr/sbin/nginx
Restart=always
```

---

# 3. `[Install]` Section

Defines:
- when service should start

Example:

```ini
[Install]
WantedBy=multi-user.target
```

Equivalent to:
- start during normal multi-user boot

---

# Unit File Locations

| Path | Purpose |
|---|---|
| `/etc/systemd/system` | custom/admin units |
| `/usr/lib/systemd/system` | package-managed units |
| `/lib/systemd/system` | distro-provided units |
| `~/.config/systemd/user` | user-level units |

---

# Common Commands

## Show service status

```bash
systemctl status nginx
```

---

## Start service

```bash
sudo systemctl start nginx
```

---

## Stop service

```bash
sudo systemctl stop nginx
```

---

## Enable at boot

```bash
sudo systemctl enable nginx
```

---

## Reload changed unit files

```bash
sudo systemctl daemon-reload
```

Very important after editing unit files.

---

# Create Your Own Service

## Create file

```bash
sudo nano /etc/systemd/system/myapp.service
```

---

## Example

```ini
[Unit]
Description=My Python App
After=network.target

[Service]
Type=simple
User=ubuntu
WorkingDirectory=/opt/myapp
ExecStart=/usr/bin/python3 app.py
Restart=always

[Install]
WantedBy=multi-user.target
```

---

## Reload systemd

```bash
sudo systemctl daemon-reload
```

---

## Start service

```bash
sudo systemctl start myapp
```

---

## Enable at boot

```bash
sudo systemctl enable myapp
```

---

# View Unit Files

## Show active unit

```bash
systemctl cat nginx
```

---

## List all services

```bash
systemctl list-units --type=service
```

---

# Timer Units

Timers replace cron jobs.

Example:

```text
backup.timer
backup.service
```

Timer triggers service.

---

# Socket Units

Used for:
- socket activation
- on-demand services

Example:

```text
ssh.socket
```

systemd starts service only when connection arrives.

---

# Target Units

Targets group services together.

Examples:

| Target | Meaning |
|---|---|
| `multi-user.target` | normal server mode |
| `graphical.target` | GUI mode |
| `network.target` | networking ready |

---

# Dependency Examples

## Start after network

```ini
After=network.target
```

---

## Require another service

```ini
Requires=postgresql.service
```

---

## Soft dependency

```ini
Wants=redis.service
```

---

# Restart Policies

| Option | Meaning |
|---|---|
| `Restart=no` | never restart |
| `Restart=always` | always restart |
| `Restart=on-failure` | restart on errors |

Example:

```ini
Restart=on-failure
RestartSec=5
```

---

# Logs

systemd integrates with journald.

View logs:

```bash
journalctl -u nginx
```

Follow live:

```bash
journalctl -fu nginx
```

---

# Validate Unit Files

```bash
systemd-analyze verify myapp.service
```

---

# Why Unit Files Matter

They provide:
- automatic startup
- dependency management
- restart handling
- logging
- security controls
- resource limits

---

# Common Real-World Services

| Service | Unit File |
|---|---|
| nginx | `nginx.service` |
| docker | `docker.service` |
| ssh | `ssh.service` |
| prometheus | `prometheus.service` |
| grafana | `grafana-server.service` |

---

# Best Practices

- store custom units in `/etc/systemd/system`
- always run `daemon-reload` after edits
- use non-root users
- use `Restart=on-failure`
- validate units before deployment
- monitor logs with `journalctl`

---

# Cheat Sheet

| Action | Command |
|---|---|
| Start | `systemctl start service` |
| Stop | `systemctl stop service` |
| Restart | `systemctl restart service` |
| Enable | `systemctl enable service` |
| Status | `systemctl status service` |
| Logs | `journalctl -u service` |
| Reload units | `systemctl daemon-reload` |
