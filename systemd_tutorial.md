# Linux systemd Tutorial

A practical beginner-to-advanced guide to using `systemd` on Linux.

---

# What is systemd?

`systemd` is the modern init system and service manager used by most Linux distributions.

It:
- starts services during boot
- manages daemons
- tracks processes
- handles logging
- manages timers
- controls targets/runlevels

Common distros using systemd:
- Ubuntu
- Debian
- RHEL
- CentOS
- Rocky Linux
- Fedora
- Arch Linux

---

# Key Components

| Component | Purpose |
|---|---|
| `systemctl` | Manage services |
| `journalctl` | View logs |
| `systemd` | Init/service manager |
| unit files | Service definitions |
| targets | Boot states |
| timers | Cron replacement |

---

# Check systemd Version

```bash
systemctl --version
```

---

# Service Basics

## Check service status

```bash
systemctl status nginx
```

Example:

```text
● nginx.service - A high performance web server
   Loaded: loaded
   Active: active (running)
```

---

# Start a Service

```bash
sudo systemctl start nginx
```

---

# Stop a Service

```bash
sudo systemctl stop nginx
```

---

# Restart a Service

```bash
sudo systemctl restart nginx
```

---

# Reload a Service

Reload configuration without full restart.

```bash
sudo systemctl reload nginx
```

---

# Enable Service at Boot

```bash
sudo systemctl enable nginx
```

Creates symlinks so the service starts automatically.

---

# Disable Service at Boot

```bash
sudo systemctl disable nginx
```

---

# Check if Service is Enabled

```bash
systemctl is-enabled nginx
```

---

# Check if Service is Active

```bash
systemctl is-active nginx
```

---

# List Running Services

```bash
systemctl list-units --type=service
```

---

# List Failed Services

```bash
systemctl --failed
```

---

# View Service Definition

```bash
systemctl cat nginx
```

---

# Show Service Properties

```bash
systemctl show nginx
```

Useful properties:

```bash
systemctl show nginx | grep Exec
```

---

# Understanding Unit Files

Systemd uses unit files.

Common locations:

| Path | Purpose |
|---|---|
| `/etc/systemd/system` | Custom/admin units |
| `/usr/lib/systemd/system` | Package units |
| `/lib/systemd/system` | Distribution units |

---

# Unit File Example

```ini
[Unit]
Description=My App
After=network.target

[Service]
Type=simple
User=myuser
WorkingDirectory=/opt/myapp
ExecStart=/usr/bin/python3 app.py
Restart=always
RestartSec=5

[Install]
WantedBy=multi-user.target
```

---

# Create Custom Service

## Create service file

```bash
sudo nano /etc/systemd/system/myapp.service
```

Paste:

```ini
[Unit]
Description=My Python App
After=network.target

[Service]
Type=simple
User=ubuntu
WorkingDirectory=/opt/myapp
ExecStart=/usr/bin/python3 /opt/myapp/app.py
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

## Enable service

```bash
sudo systemctl enable myapp
```

---

## Start service

```bash
sudo systemctl start myapp
```

---

## Check logs

```bash
journalctl -u myapp
```

---

# Logging with journalctl

## Show all logs

```bash
journalctl
```

---

## Follow logs live

```bash
journalctl -f
```

---

## Show logs for service

```bash
journalctl -u nginx
```

---

## Show recent logs

```bash
journalctl -u nginx -n 50
```

---

## Show logs since boot

```bash
journalctl -b
```

---

## Show logs by priority

```bash
journalctl -p err
```

Priorities:
- emerg
- alert
- crit
- err
- warning
- notice
- info
- debug

---

# Targets (Runlevels)

Systemd replaced SysV runlevels with targets.

| Old Runlevel | systemd Target |
|---|---|
| 0 | poweroff.target |
| 1 | rescue.target |
| 3 | multi-user.target |
| 5 | graphical.target |
| 6 | reboot.target |

---

# Check Default Target

```bash
systemctl get-default
```

---

# Change Default Target

CLI mode:

```bash
sudo systemctl set-default multi-user.target
```

GUI mode:

```bash
sudo systemctl set-default graphical.target
```

---

# Reboot and Shutdown

## Reboot

```bash
sudo systemctl reboot
```

---

## Power off

```bash
sudo systemctl poweroff
```

---

## Halt

```bash
sudo systemctl halt
```

---

# Timers (Cron Replacement)

## List timers

```bash
systemctl list-timers
```

---

# Timer Example

## Service file

```ini
[Unit]
Description=Backup Job

[Service]
Type=oneshot
ExecStart=/usr/local/bin/backup.sh
```

Save as:

```text
/etc/systemd/system/backup.service
```

---

## Timer file

```ini
[Unit]
Description=Run backup daily

[Timer]
OnCalendar=daily
Persistent=true

[Install]
WantedBy=timers.target
```

Save as:

```text
/etc/systemd/system/backup.timer
```

---

## Enable timer

```bash
sudo systemctl enable --now backup.timer
```

---

## Check timers

```bash
systemctl list-timers
```

---

# Useful OnCalendar Examples

| Schedule | Example |
|---|---|
| Every day | `daily` |
| Every hour | `hourly` |
| Weekly | `weekly` |
| Every 5 min | `*:0/5` |
| Midnight | `*-*-* 00:00:00` |
| Mondays 8AM | `Mon *-*-* 08:00:00` |

---

# Dependency Management

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

## Start together

```ini
Wants=redis.service
```

---

# Restart Policies

| Policy | Meaning |
|---|---|
| `no` | Never restart |
| `always` | Always restart |
| `on-failure` | Restart on error |
| `unless-stopped` | Restart unless manually stopped |

Example:

```ini
Restart=on-failure
RestartSec=5
```

---

# Security Hardening

Useful options:

```ini
NoNewPrivileges=true
PrivateTmp=true
ProtectSystem=full
ProtectHome=true
```

---

# Resource Limits

Limit memory:

```ini
MemoryMax=512M
```

Limit CPU:

```ini
CPUQuota=50%
```

---

# Debugging systemd

## Validate unit file

```bash
systemd-analyze verify myapp.service
```

---

## Analyze boot time

```bash
systemd-analyze
```

---

## Blame slow services

```bash
systemd-analyze blame
```

---

## Critical chain

```bash
systemd-analyze critical-chain
```

---

# Common Troubleshooting

## Service won't start

Check:

```bash
systemctl status myapp
journalctl -u myapp
```

---

## Reload changed unit

```bash
sudo systemctl daemon-reload
```

---

## Reset failed state

```bash
sudo systemctl reset-failed
```

---

# Best Practices

- Use `Restart=on-failure`
- Run services as non-root users
- Use `journalctl` for logs
- Use timers instead of cron where possible
- Store custom units in `/etc/systemd/system`
- Validate units before deploying

---

# Useful Commands Cheat Sheet

| Action | Command |
|---|---|
| Status | `systemctl status service` |
| Start | `systemctl start service` |
| Stop | `systemctl stop service` |
| Restart | `systemctl restart service` |
| Enable | `systemctl enable service` |
| Disable | `systemctl disable service` |
| Logs | `journalctl -u service` |
| Follow logs | `journalctl -fu service` |
| Failed services | `systemctl --failed` |
| Reload units | `systemctl daemon-reload` |

---

# Advanced Topics

You can extend this knowledge into:
- socket activation
- transient services
- cgroups
- sandboxing
- portable services
- container integration
- Kubernetes node services
- systemd-networkd
- systemd-resolved
- systemd-oomd
