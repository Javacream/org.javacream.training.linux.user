# Linux Process Management Commands Cheat Sheet

## View Processes

### Show all running processes
```bash
ps aux
```

### Show process tree
```bash
pstree
```

### Interactive process viewer
```bash
top
```

### Better interactive viewer
```bash
htop
```

Install:
```bash
sudo apt install htop
```

---

# Find Processes

### Find process by name
```bash
pgrep nginx
```

### Show process details
```bash
ps -fp $(pgrep nginx)
```

### Search processes
```bash
ps aux | grep nginx
```

### Find process using a port
```bash
sudo lsof -i :8080
```

---

# Kill Processes

### Kill by PID
```bash
kill 1234
```

### Force kill
```bash
kill -9 1234
```

### Kill by process name
```bash
pkill nginx
```

### Kill all matching processes
```bash
killall nginx
```

---

# Process Priorities

### Start process with priority
```bash
nice -n 10 command
```

Priority range:
- `-20` = highest priority
- `19` = lowest priority

---

### Change running process priority
```bash
renice 5 -p 1234
```

---

# Background & Foreground Jobs

### Run in background
```bash
command &
```

---

### List background jobs
```bash
jobs
```

---

### Bring job to foreground
```bash
fg %1
```

---

### Send job to background
```bash
bg %1
```

---

# Monitor Resource Usage

### CPU and memory usage
```bash
top
```

---

### Sort by memory
```bash
ps aux --sort=-%mem | head
```

---

### Sort by CPU
```bash
ps aux --sort=-%cpu | head
```

---

### Monitor per-core CPU
```bash
mpstat -P ALL 1
```

Install:
```bash
sudo apt install sysstat
```

---

# Process Details

### Show full command line
```bash
ps -p 1234 -o cmd
```

---

### Show process owner
```bash
ps -o user= -p 1234
```

---

### Show parent process
```bash
ps -o ppid= -p 1234
```

---

### Show process threads
```bash
ps -T -p 1234
```

---

# Signals

## Common signals

| Signal | Meaning |
|---|---|
| `1` | HUP reload |
| `2` | Interrupt |
| `9` | Kill |
| `15` | Terminate |
| `19` | Stop |
| `18` | Continue |

---

### Reload service gracefully
```bash
kill -HUP 1234
```

---

### Pause process
```bash
kill -STOP 1234
```

---

### Resume process
```bash
kill -CONT 1234
```

---

# Process Limits

### Show current limits
```bash
ulimit -a
```

---

### Increase open files limit
```bash
ulimit -n 65535
```

---

# systemd Service Processes

### Check service status
```bash
systemctl status nginx
```

---

### Restart service
```bash
sudo systemctl restart nginx
```

---

### Show service logs
```bash
journalctl -u nginx
```

---

# Real-Time Monitoring

### Watch process list
```bash
watch -n 1 'ps aux --sort=-%cpu | head'
```

---

### Monitor process IO
```bash
iotop
```

Install:
```bash
sudo apt install iotop
```

---

### Monitor process network usage
```bash
nethogs
```

---

# Debugging Processes

### Trace system calls
```bash
strace -p 1234
```

---

### Trace file access
```bash
lsof -p 1234
```

---

### Monitor library calls
```bash
ltrace -p 1234
```

---

# CPU Affinity

### Show CPU affinity
```bash
taskset -p 1234
```

---

### Set CPU affinity
```bash
taskset -cp 0,1 1234
```

---

# Process Scheduling

### Real-time scheduling info
```bash
chrt -p 1234
```

---

### Run with realtime priority
```bash
sudo chrt -r 99 command
```

---

# Useful One-Liners

### Top memory consumers
```bash
ps aux --sort=-rss | head
```

---

### Zombie processes
```bash
ps aux | awk '$8=="Z"'
```

---

### Longest running processes
```bash
ps -eo pid,etime,cmd --sort=etime
```

---

### Processes started recently
```bash
ps -eo pid,lstart,cmd --sort=start_time
```

---

# Recommended Tools

| Purpose | Tool |
|---|---|
| Process listing | `ps` |
| Interactive monitor | `top` |
| Better monitor | `htop` |
| File usage | `lsof` |
| System calls | `strace` |
| IO monitoring | `iotop` |
| Network usage | `nethogs` |
| CPU affinity | `taskset` |

---

# Typical Troubleshooting Workflow

## High CPU

```bash
top
```

Then:
```bash
ps -fp PID
```

---

## Memory leak

```bash
ps aux --sort=-%mem | head
```

---

## Process stuck

```bash
strace -p PID
```

---

## Port conflict

```bash
sudo lsof -i :80
```

---

## Kill runaway process

```bash
kill -9 PID
```
