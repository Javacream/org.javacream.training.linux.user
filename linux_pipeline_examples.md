# 10 Useful Linux Pipelines for Data Processing

These examples demonstrate how Linux commands can be chained together using pipes (`|`) for powerful text and data processing.

---

# 1. Count Running Processes

```bash
ps aux | wc -l
```

## What it does
- `ps aux` → lists all processes
- `wc -l` → counts lines

## Result
Total number of running processes.

---

# 2. Find Top Memory-Consuming Processes

```bash
ps aux | sort -rk 4 | head
```

## What it does
- `ps aux` → process list
- `sort -rk 4` → sort by memory column descending
- `head` → top 10 entries

## Result
Highest memory-consuming processes.

---

# 3. Count Unique IP Addresses in a Log File

```bash
cat access.log | awk '{print $1}' | sort | uniq | wc -l
```

## What it does
- extracts IPs
- sorts them
- removes duplicates
- counts unique entries

## Result
Number of unique visitors.

---

# 4. Find Most Frequent IP Addresses

```bash
cat access.log | awk '{print $1}' | sort | uniq -c | sort -nr | head
```

## What it does
- extracts IPs
- counts occurrences
- sorts numerically descending

## Result
Top IPs by request count.

---

# 5. Count Failed SSH Login Attempts

```bash
journalctl | grep "Failed password" | wc -l
```

## What it does
- filters system logs
- counts failed SSH logins

## Result
Total failed SSH attempts.

---

# 6. Find Largest Files

```bash
du -ah /var | sort -rh | head
```

## What it does
- `du -ah` → file sizes
- `sort -rh` → human-readable reverse sort
- `head` → top largest files

## Result
Largest files/directories under `/var`.

---

# 7. Count Listening TCP Ports

```bash
ss -tuln | grep LISTEN | wc -l
```

## What it does
- lists sockets
- filters listening services
- counts them

## Result
Number of listening ports.

---

# 8. Extract Usernames from `/etc/passwd`

```bash
cat /etc/passwd | cut -d: -f1 | sort
```

## What it does
- `cut` extracts usernames
- `sort` orders alphabetically

## Result
Sorted list of system users.

---

# 9. Find Most Common Error Messages

```bash
grep ERROR app.log | sort | uniq -c | sort -nr | head
```

## What it does
- filters errors
- groups duplicates
- counts occurrences
- sorts by frequency

## Result
Most common application errors.

---

# 10. Monitor Active Network Connections

```bash
ss -tunap | grep ESTAB | awk '{print $5}' | cut -d: -f1 | sort | uniq -c | sort -nr
```

## What it does
- shows active TCP connections
- extracts remote IPs
- counts connections per IP

## Result
Top connected remote hosts.

---

# Pipeline Pattern Breakdown

Typical Linux pipeline structure:

```bash
data_source | filter | transform | aggregate | output
```

Example:

```bash
cat access.log | grep 500 | awk '{print $1}' | sort | uniq -c
```

Flow:

```text
read log
→ filter HTTP 500
→ extract IP
→ sort
→ count duplicates
```

---

# Most Common Pipeline Commands

| Command | Purpose |
|---|---|
| `grep` | filter lines |
| `awk` | extract/process fields |
| `cut` | split columns |
| `sort` | sort data |
| `uniq` | remove/count duplicates |
| `wc` | count |
| `head` | first lines |
| `tail` | last lines |
| `tr` | translate characters |
| `sed` | edit streams |

---

# Key Learning Pattern

Most Linux data processing follows this structure:

```bash
input → filter → transform → summarize
```

Example:

```bash
journalctl | grep nginx | awk '{print $5}' | sort | uniq -c
```

This is foundational Linux and DevOps skill.
