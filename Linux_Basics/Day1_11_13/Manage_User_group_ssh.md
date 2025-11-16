## 1. User & Group Basics

Important files (just keep them in mind while practicing):

* `/etc/passwd` – basic user info (name, UID, GID, shell, home)
* `/etc/shadow` – password hashes + password aging
* `/etc/group` – group definitions
* `/etc/sudoers` + `/etc/sudoers.d/` – sudo rules

### 1.1 `useradd`

Create a user:

```bash
# Create user with home directory and default shell
sudo useradd -m -s /bin/bash devuser

# Create user with specific primary group and comment
sudo groupadd devops
sudo useradd -m -s /bin/bash -g devops -c "DevOps Engineer" viswa
```

Useful flags:

* `-m` – create home dir
* `-s` – login shell
* `-g` – primary group (must exist)
* `-G` – extra (supplementary) groups
* `-u` – custom UID

### 1.2 `usermod`

Modify existing users:

```bash
# Add user to extra group without removing existing ones
sudo usermod -aG wheel viswa

# Change login shell
sudo usermod -s /bin/zsh viswa

# Lock / unlock account (no login)
sudo usermod -L viswa   # lock
sudo usermod -U viswa   # unlock
```

> **Important**: Always use `-aG` (append + groups). If you use `-G` without `-a`, it overwrites the user’s supplementary groups.

### 1.3 `groupadd`

Create groups for access separation:

```bash
sudo groupadd dev
sudo groupadd qa
```

You can later assign users:

```bash
sudo usermod -aG dev alice
sudo usermod -aG qa bob
```

### 1.4 `passwd`

Set or change passwords and lock accounts:

```bash
# Set password interactively
sudo passwd viswa

# Lock account (disables password)
sudo passwd -l viswa

# Unlock account
sudo passwd -u viswa
```

### 1.5 `chage` (Password Aging)

View and set password expiry policy per user:

```bash
# View password aging for a user
sudo chage -l viswa

# Force password to expire today (user must change on next login)
sudo chage -d 0 viswa

# Set max days password is valid, warning period, and inactivity
sudo chage -M 90 -W 7 -I 30 viswa
# -M 90 → must change every 90 days
# -W 7  → warn 7 days before expiry
# -I 30 → account disabled 30 days after password expires
```

---

## 2. File Ownership & Permissions

### 2.1 `chown`

Change owner and/or group:

```bash
# Change owner only
sudo chown viswa file.txt

# Change owner and group
sudo chown viswa:dev file.txt

# Recursive change on directories
sudo chown -R viswa:dev /opt/app
```

### 2.2 `chmod`

Change permission bits:

```bash
# Numeric mode
chmod 640 file.txt   # rw-r----- (owner read/write, group read, others none)
chmod 750 script.sh  # rwxr-x--- (owner all, group rx, others none)

# Symbolic mode
chmod u+x script.sh          # add execute to user
chmod g-w,o-r file.txt       # remove write from group, read from others

# Typical secure directory for app config
chmod 750 /opt/app/config
```

### 2.3 ACLs: `setfacl`, `getfacl`

Use ACLs when classic user/group/others isn’t enough (e.g. multiple users need different permissions).

```bash
# Give user 'alice' read/write access to a file
sudo setfacl -m u:alice:rw /opt/app/config.yaml

# Give group 'qa' read access to a directory
sudo setfacl -m g:qa:rX /opt/reports

# Set default ACL for new files in a directory
sudo setfacl -d -m g:dev:rwX /srv/shared

# View ACLs
getfacl /opt/app/config.yaml
```

Common flags:

* `-m` – modify / add entry
* `-x` – remove specific ACL entry
* `-b` – remove all ACLs
* `-d` – default ACL on directories

---

## 3. Sudo: `sudoers`, `visudo`, `sudoers.d`

**Golden rule**: Always edit sudoers via `visudo`.

```bash
sudo visudo
```

This opens `/etc/sudoers` with syntax checking when you save.

### 3.1 Basic sudoers entries

Examples:

```text
# Allow members of wheel group to run any command
%wheel  ALL=(ALL)       ALL

# Allow viswa to run any command without password (not recommended in prod)
viswa  ALL=(ALL)       NOPASSWD: ALL

# Allow devops group to restart a specific service without password
%devops ALL=(root) NOPASSWD: /usr/bin/systemctl restart httpd
```

* `ALL` (first) → from any host (on single host, just leave ALL)
* `(ALL)` → can run commands as any user
* `NOPASSWD:` → no password prompt for listed commands
* `%groupname` → group entry

### 3.2 `/etc/sudoers.d/`

Better than cluttering main file: drop per-team or per-app sudo definitions.

```bash
sudo visudo -f /etc/sudoers.d/devops
```

Example file:

```text
%devops ALL=(root) NOPASSWD: /usr/bin/systemctl restart httpd, \
                             /usr/bin/systemctl restart nginx
```

**Permissions** must be strict:

```bash
sudo chmod 440 /etc/sudoers.d/devops
sudo chown root:root /etc/sudoers.d/devops
```

---

## 4. Security Hardening (Accounts & SSH)

### 4.1 Password policy (quick wins)

Global defaults often live in `/etc/login.defs` and PAM configs.

Some quick examples (varies slightly by distro):

In `/etc/login.defs`:

```text
PASS_MAX_DAYS   90
PASS_MIN_DAYS   1
PASS_WARN_AGE   7
```

For stronger password quality, many distros use `pam_pwquality.so` in e.g. `/etc/pam.d/system-auth` or `/etc/pam.d/common-password`. Example line (don’t copy blindly into prod, but conceptually):

```text
password requisite pam_pwquality.so retry=3 minlen=12 ucredit=-1 lcredit=-1 dcredit=-1 ocredit=-1
```

Meaning: at least one uppercase, lowercase, digit, special char; min length 12.

### 4.2 Account lockout

Two layers:

1. **Admin-driven lock** (immediate):

```bash
sudo passwd -l user1    # lock
sudo usermod -L user1   # alternative
sudo passwd -u user1    # unlock
```

2. **Automatic lockout after failures** (PAM faillock on many distros):

Config lives in e.g. `/etc/security/faillock.conf` (RHEL/Rocky/Alma):

```text
deny = 5          # lock after 5 failed attempts
unlock_time = 600 # auto unlock after 10 mins
```

Then PAM files use `pam_faillock.so`. That’s more detail than you probably need now, but good to know.

### 4.3 SSH Hardening (sshd_config)

File: `/etc/ssh/sshd_config`

Key directives (common hardening set):

```text
# Disable root SSH login
PermitRootLogin no

# Disable password auth (keys only)
PasswordAuthentication no

# (Optional) Restrict to a group
AllowGroups sshusers

# Stronger protocol defaults (on old systems)
Protocol 2
```

Then:

```bash
sudo systemctl restart sshd    # or ssh
```

You’d typically:

1. Create a group for SSH users:

   ```bash
   sudo groupadd sshusers
   sudo usermod -aG sshusers viswa
   ```
2. Ensure your SSH key works **before** turning off password auth, or you’ll lock yourself out.

---

## 5. Hands-On Mini Lab (Do These on a Test VM)

I’ll name the tasks so you can track them like a checklist.

### UG-01: Create Teams & Users

```bash
sudo groupadd devops
sudo groupadd qa

sudo useradd -m -s /bin/bash -g devops alice
sudo useradd -m -s /bin/bash -g qa bob

sudo passwd alice
sudo passwd bob
```

Verify:

```bash
id alice
id bob
```

---

### UG-02: Add Users to Multiple Groups

Make `alice` part of both `devops` and `qa`:

```bash
sudo usermod -aG qa alice
id alice
```

---

### UG-03: Setup Password Aging for a User

Set `alice` to rotate password every 60 days, warn 5 days before, mark inactive after 15 days:

```bash
sudo chage -M 60 -W 5 -I 15 alice
sudo chage -l alice
```

---

### UG-04: Lock & Unlock a User

Simulate a disabled account for `bob`:

```bash
sudo passwd -l bob
su - bob      # should fail

sudo passwd -u bob
su - bob      # should work again
```

---

### UG-05: Shared Project Directory with Group Ownership

Create a shared dir for devops team:

```bash
sudo mkdir -p /srv/devops-project
sudo chown root:devops /srv/devops-project
sudo chmod 2770 /srv/devops-project
# 2 = setgid bit → new files inherit group devops
```

Test with `alice`:

```bash
sudo -u alice touch /srv/devops-project/app.log
ls -l /srv/devops-project
# Group should be devops
```

---

### UG-06: Extra Access with ACLs

Give `bob` read access to that project directory without adding him to devops:

```bash
sudo setfacl -m u:bob:rx /srv/devops-project
getfacl /srv/devops-project
```

Test:

```bash
sudo -u bob ls /srv/devops-project
sudo -u bob cat /srv/devops-project/app.log  # should work if file is readable
```

---

### UG-07: Allow DevOps Group to Restart a Service via sudo

Create a sudoers drop-in:

```bash
sudo visudo -f /etc/sudoers.d/devops
```

Add:

```text
%devops ALL=(root) NOPASSWD: /usr/bin/systemctl restart sshd
```

Then test with `alice`:

```bash
sudo -l                      # check allowed commands
sudo systemctl restart sshd  # should not ask for password
```

---

### UG-08: Configure SSH Basic Hardening

1. Create SSH group and add your user:

```bash
sudo groupadd sshusers
sudo usermod -aG sshusers viswa
```

2. Edit sshd config:

```bash
sudo vi /etc/ssh/sshd_config
```

Add/modify:

```text
PermitRootLogin no
AllowGroups sshusers
```

3. Restart SSH:

```bash
sudo systemctl restart sshd
```

4. From another terminal, try:

   * Login as `root` → **should fail**
   * Login as your user in `sshusers` → **should work**

---
