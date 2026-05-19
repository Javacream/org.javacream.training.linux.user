# Add User and Group in Ubuntu

## Create a New User

### Basic user creation

```bash
sudo adduser john
```

This:
- creates user
- creates home directory
- sets password
- creates matching group

---

# Create User Without Password Prompt

```bash
sudo useradd -m john
```

Then set password:

```bash
sudo passwd john
```

---

# Create a Group

```bash
sudo groupadd developers
```

Verify:

```bash
getent group developers
```

---

# Add User to Group

```bash
sudo usermod -aG developers john
```

Important:
- `-a` = append
- `-G` = supplementary groups

Without `-a`, existing groups may be removed.

---

# Add User to Multiple Groups

```bash
sudo usermod -aG sudo,docker,developers john
```

---

# Verify User Groups

```bash
groups john
```

or:

```bash
id john
```

Example:

```text
uid=1001(john) gid=1001(john) groups=1001(john),27(sudo),999(docker)
```

---

# Create User with Specific Group

```bash
sudo useradd -m -g developers john
```

Meaning:
- primary group = developers

---

# Create User with Home Directory

```bash
sudo useradd -m john
```

Home:

```text
/home/john
```

---

# Create System User

For services/daemons:

```bash
sudo useradd -r -s /usr/sbin/nologin appuser
```

Meaning:
- `-r` = system account
- no interactive login

---

# Give User sudo Access

## Add to sudo group

```bash
sudo usermod -aG sudo john
```

---

# Verify sudo Access

Switch user:

```bash
su - john
```

Test:

```bash
sudo whoami
```

Expected:

```text
root
```

---

# Change User Password

```bash
sudo passwd john
```

---

# Lock User Account

```bash
sudo passwd -l john
```

---

# Unlock User Account

```bash
sudo passwd -u john
```

---

# Delete User

Keep home directory:

```bash
sudo userdel john
```

Remove home directory too:

```bash
sudo userdel -r john
```

---

# Delete Group

```bash
sudo groupdel developers
```

---

# Change User Primary Group

```bash
sudo usermod -g developers john
```

---

# List All Users

```bash
cut -d: -f1 /etc/passwd
```

---

# List All Groups

```bash
cut -d: -f1 /etc/group
```

---

# Check Logged-In Users

```bash
who
```

or:

```bash
w
```

---

# User Management Files

| File | Purpose |
|---|---|
| `/etc/passwd` | user accounts |
| `/etc/shadow` | encrypted passwords |
| `/etc/group` | group definitions |
| `/etc/gshadow` | secure group passwords |

---

# Common Examples

## Create admin user

```bash
sudo adduser devops
sudo usermod -aG sudo devops
```

---

## Create docker user

```bash
sudo adduser appuser
sudo usermod -aG docker appuser
```

---

## Create shared development group

```bash
sudo groupadd developers

sudo usermod -aG developers alice
sudo usermod -aG developers bob
```

---

# Useful Permissions Commands

## Change ownership

```bash
sudo chown john:developers file.txt
```

---

## Recursive ownership

```bash
sudo chown -R john:developers /opt/app
```

---

# Best Practices

- use `adduser` for interactive creation
- use `useradd` in scripts
- always use `-aG` with `usermod`
- avoid logging in directly as root
- use groups for shared access
- use system users for services

---

# Quick Cheat Sheet

| Task | Command |
|---|---|
| Add user | `sudo adduser john` |
| Add group | `sudo groupadd developers` |
| Add user to group | `sudo usermod -aG developers john` |
| Grant sudo | `sudo usermod -aG sudo john` |
| Delete user | `sudo userdel -r john` |
| Change password | `sudo passwd john` |
| Show groups | `groups john` |
| Show user info | `id john` |
