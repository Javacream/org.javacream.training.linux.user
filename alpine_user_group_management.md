# Add User and Group in Alpine Linux

Alpine Linux uses BusyBox utilities and lightweight user management tools.

Unlike Ubuntu:
- `adduser` and `addgroup` are the primary commands
- there is usually no `usermod`
- sudo may not be installed by default

---

# Create a New User

## Interactive user creation

```sh
adduser john
```

This:
- creates user
- creates home directory
- prompts for password

---

# Create User Without Password

```sh
adduser -D john
```

Meaning:
- `-D` = default/no password prompt

---

# Set User Password

```sh
passwd john
```

---

# Create a Group

```sh
addgroup developers
```

---

# Add User to Group

```sh
addgroup john developers
```

Meaning:
- add existing user `john`
- to group `developers`

---

# Verify User Groups

```sh
groups john
```

or:

```sh
id john
```

Example:

```text
uid=1000(john) gid=1000(john) groups=developers,wheel
```

---

# Create User with Specific Group

```sh
adduser -G developers john
```

Meaning:
- primary group = developers

---

# Create System User

For services/daemons:

```sh
adduser -S appuser
```

Meaning:
- `-S` = system account

---

# Create System Group

```sh
addgroup -S appgroup
```

---

# Create System User with System Group

```sh
addgroup -S appgroup
adduser -S -G appgroup appuser
```

---

# Create User with Home Directory

```sh
adduser -h /home/john john
```

---

# Disable Shell Login

```sh
adduser -s /sbin/nologin appuser
```

or:

```sh
adduser -s /bin/false appuser
```

---

# Install sudo

Alpine often does not include sudo by default.

Install:

```sh
apk add sudo
```

---

# Give User sudo Access

Add user to wheel group:

```sh
addgroup john wheel
```

Edit sudoers:

```sh
visudo
```

Enable:

```text
%wheel ALL=(ALL) ALL
```

---

# Verify sudo Access

Switch user:

```sh
su - john
```

Test:

```sh
sudo whoami
```

Expected:

```text
root
```

---

# Change User Password

```sh
passwd john
```

---

# Lock User Account

```sh
passwd -l john
```

---

# Unlock User Account

```sh
passwd -u john
```

---

# Delete User

```sh
deluser john
```

Remove home directory:

```sh
deluser --remove-home john
```

---

# Delete Group

```sh
delgroup developers
```

---

# Remove User from Group

```sh
delgroup john developers
```

---

# List All Users

```sh
cut -d: -f1 /etc/passwd
```

---

# List All Groups

```sh
cut -d: -f1 /etc/group
```

---

# Check Logged-In Users

```sh
who
```

or:

```sh
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

```sh
adduser devops
addgroup devops wheel
```

---

## Create docker user

```sh
adduser appuser
addgroup appuser docker
```

---

## Create shared development group

```sh
addgroup developers

adduser alice developers
adduser bob developers
```

---

# Useful Permissions Commands

## Change ownership

```sh
chown john:developers file.txt
```

---

## Recursive ownership

```sh
chown -R john:developers /opt/app
```

---

# Alpine Package Management

Install packages:

```sh
apk add package-name
```

Update repositories:

```sh
apk update
```

Upgrade packages:

```sh
apk upgrade
```

---

# BusyBox Notes

Many Alpine commands come from BusyBox.

Examples:

```sh
busybox
```

or:

```sh
busybox --list
```

---

# OpenRC Service Management

Alpine uses OpenRC instead of systemd.

## Start service

```sh
rc-service nginx start
```

---

## Stop service

```sh
rc-service nginx stop
```

---

## Restart service

```sh
rc-service nginx restart
```

---

## Enable at boot

```sh
rc-update add nginx default
```

---

## List services

```sh
rc-status
```

---

# Best Practices

- use system users for services
- use wheel group for sudo
- avoid running services as root
- use OpenRC correctly
- install sudo explicitly when needed
- keep Alpine minimal

---

# Quick Cheat Sheet

| Task | Command |
|---|---|
| Add user | `adduser john` |
| Add group | `addgroup developers` |
| Add user to group | `addgroup john developers` |
| Install sudo | `apk add sudo` |
| Grant sudo | `addgroup john wheel` |
| Delete user | `deluser john` |
| Delete group | `delgroup developers` |
| Change password | `passwd john` |
| Show groups | `groups john` |
| Show user info | `id john` |

---

# Ubuntu vs Alpine

| Task | Ubuntu | Alpine |
|---|---|---|
| Add user | `adduser` | `adduser` |
| Add group | `groupadd` | `addgroup` |
| Modify groups | `usermod -aG` | `addgroup user group` |
| Remove user | `userdel` | `deluser` |
| Init system | systemd | OpenRC |
| Package manager | apt | apk |
