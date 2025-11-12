# 🧩 **Complete Linux User Management Command Set (With Real-World Context)**

---

## 🧠 1️⃣ Account Management — Creation, Modification, Deletion

| Command    | Purpose                                                  | Example                                          |
| ---------- | -------------------------------------------------------- | ------------------------------------------------ |
| `useradd`  | Create new user                                          | `sudo useradd -m -s /bin/bash viswa`             |
| `usermod`  | Modify existing user (e.g., add to groups, change shell) | `sudo usermod -aG devops viswa`                  |
| `userdel`  | Delete user                                              | `sudo userdel -r viswa` *(removes home dir too)* |
| `groupadd` | Create new group                                         | `sudo groupadd devops`                           |
| `groupdel` | Delete group                                             | `sudo groupdel devops`                           |
| `groupmod` | Rename or modify group                                   | `sudo groupmod -n sre devops`                    |

> 💡 **Pro Tip:** Every user always has one **primary group** (same name as user by default) and can belong to multiple **secondary groups**.

---

## 🔐 2️⃣ Password & Authentication Management

| Command               | Purpose                                   | Example                        |
| --------------------- | ----------------------------------------- | ------------------------------ |
| `passwd`              | Set or change user password               | `sudo passwd viswa`            |
| `chage`               | Manage password expiry policies           | `sudo chage -M 90 -W 14 viswa` |
| `faillog`             | Check failed login attempts               | `sudo faillog -a`              |
| `lastlog`             | Show last login time for all users        | `sudo lastlog`                 |
| `who`, `w`            | Show currently logged-in users            | `w`                            |
| `last`                | Show login history (from `/var/log/wtmp`) | `last viswa`                   |
| `sudo passwd -l user` | Lock user account                         | `sudo passwd -l bob`           |
| `sudo passwd -u user` | Unlock user account                       | `sudo passwd -u bob`           |

> 💡 Combine `chage` and `lastlog` in auditing scripts to detect **stale or inactive accounts**.

---

## 🧾 3️⃣ User Information & Database Query

| Command                | Purpose                                       | Example                                         |
| ---------------------- | --------------------------------------------- | ----------------------------------------------- |
| `id`                   | Show user’s UID, GID, groups                  | `id viswa`                                      |
| `groups`               | Show all groups the user belongs to           | `groups viswa`                                  |
| `getent passwd`        | Query user info (local + LDAP)                | `getent passwd viswa`                           |
| `getent group`         | Query group info                              | `getent group devops`                           |
| `finger`               | Show user details like login, home dir, shell | `finger viswa` *(may need to install `finger`)* |
| `sudo cat /etc/passwd` | Manual view of account DB                     | For learning — avoid editing manually!          |
| `sudo cat /etc/group`  | Manual view of group DB                       | Shows all group memberships                     |

---

## 🛠️ 4️⃣ Ownership & Permissions Management

| Command               | Purpose                                | Example                       |
| --------------------- | -------------------------------------- | ----------------------------- |
| `chmod`               | Change file/directory permissions      | `chmod 640 file.txt`          |
| `chown`               | Change ownership                       | `chown viswa:devops file.txt` |
| `chgrp`               | Change group ownership                 | `chgrp devops file.txt`       |
| `umask`               | Default permission mask                | `umask 022`                   |
| `setfacl` / `getfacl` | Manage ACLs (fine-grained permissions) | `setfacl -m u:bob:r file.txt` |

> 💡 **Pro tip:** ACLs are essential when multiple users need different permissions on the same file or directory — very useful in shared project folders.

---

## 🧰 5️⃣ Session, Process, and Security Utilities

| Command         | Purpose                                 | Example                      |
| --------------- | --------------------------------------- | ---------------------------- |
| `whoami`        | Show current user                       | `whoami`                     |
| `su - user`     | Switch user                             | `su - bob`                   |
| `sudo`          | Run commands as superuser               | `sudo systemctl status sshd` |
| `loginctl`      | Manage sessions (systemd-based systems) | `loginctl list-sessions`     |
| `pkill -u user` | Kill all processes owned by user        | `sudo pkill -u bob`          |

> 💡 Great for cleanup when removing a user who has lingering active sessions.

---

## 🧩 6️⃣ Files Tied to User Management

| File                        | Description                                       | Usage                                          |
| --------------------------- | ------------------------------------------------- | ---------------------------------------------- |
| `/etc/passwd`               | User accounts database                            | Primary user info (username, UID, GID, shell)  |
| `/etc/shadow`               | Encrypted passwords & expiry                      | Root-only file, controlled by `passwd`/`chage` |
| `/etc/group`                | Groups & members                                  | Defines all groups                             |
| `/etc/sudoers`              | Sudo privileges                                   | Use `visudo` to edit                           |
| `/etc/login.defs`           | Default user creation settings                    | Defines default UID/GID ranges, password aging |
| `/etc/security/limits.conf` | Set resource limits                               | e.g., max processes per user                   |
| `/etc/nsswitch.conf`        | Controls database sources (`files`, `ldap`, etc.) | Tells `getent` where to query                  |

---

## 💼 7️⃣ Enterprise-Level Extras (Once You’re Confident)

| Command                   | Purpose                                | Real-World Usage                      |
| ------------------------- | -------------------------------------- | ------------------------------------- |
| `pam_tally2` / `faillock` | Lockout policy for failed logins       | PAM-based security control            |
| `visudo`                  | Safely edit sudoers                    | Prevent syntax errors that break sudo |
| `newgrp`                  | Switch primary group in active session | e.g., `newgrp devops`                 |
| `vipw` / `vigr`           | Safely edit passwd/group files         | Used by sysadmins for manual fixes    |
| `sudoers.d/`              | Directory for modular sudo policies    | Best practice for managing roles      |
| `auditctl` / `ausearch`   | Audit account-related system calls     | Security/compliance logging           |

---

## ⚡ **Pro-Tier Exercise: Audit All User Accounts**

Here’s a quick one-liner that combines what you’ve learned:

```bash
echo -e "USER\tUID\tGID\tHOME\tSHELL"
getent passwd | awk -F: '{printf "%s\t%s\t%s\t%s\t%s\n", $1, $3, $4, $6, $7}'
```

Output:

```
USER    UID   GID   HOME          SHELL
root    0     0     /root         /bin/bash
alice   1001  1001  /home/alice   /bin/bash
bob     1002  1002  /home/bob     /bin/bash
```

---

## ✅ Summary — Your “User Management Arsenal”

| Category                   | Commands to Master                                   |
| -------------------------- | ---------------------------------------------------- |
| User creation/modification | `useradd`, `usermod`, `userdel`                      |
| Group management           | `groupadd`, `groupmod`, `groupdel`, `newgrp`         |
| Permissions                | `chown`, `chmod`, `chgrp`, `setfacl`                 |
| Information lookup         | `id`, `groups`, `getent`, `finger`, `who`, `lastlog` |
| Security & policies        | `passwd`, `chage`, `faillog`, `sudo`, `visudo`       |

---
