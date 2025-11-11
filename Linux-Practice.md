# 🧩 **Tier 1: Linux Fundamentals & System Navigation (The Single Node)**

**Objective:** Become fluent with Linux CLI, filesystem hierarchy, and process/user control on a single node (`node-01`).

---

## **I. Getting Started**

| **Topic**                | **Key Concepts**                                                                                                                                                          | **Mandatory Hands-on (node-01)**                                                                                                                                                                                                                                                                          |
| ------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Linux Setup & Access** | Installation, SSH configuration, basic commands, shell types (`bash`, `zsh`), `man` pages, filesystem hierarchy `/bin`, `/etc`, `/home`, `/var`, `/proc`, `/tmp`, `/usr`. | 1️⃣ SSH into `node-01` as root.<br>2️⃣ Create a non-root user `student`. <br>3️⃣ Generate an SSH keypair on your laptop and copy the public key to `~student/.ssh/authorized_keys`. <br>4️⃣ Create a custom alias: `alias ll='ls -lh --color=auto'`. <br>5️⃣ Use `man ls` and summarize 3 useful options. |

---

## **II. CLI Basics**

| **Topic**                       | **Key Concepts**                                   | **Mandatory Hands-on (node-01)**                                                                                                                |              |                                                                                                     |
| ------------------------------- | -------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------- | ------------ | --------------------------------------------------------------------------------------------------- |
| **File Navigation**             | `pwd`, `cd`, relative vs absolute paths.           | Navigate between `/`, `/etc`, `/home`, `/tmp`; create `~/practice` and test relative/absolute paths.                                            |              |                                                                                                     |
| **File & Directory Management** | `ls`, `mkdir`, `touch`, `cp`, `mv`, `rm`, `rmdir`. | Build a small directory tree `~/projects/demo/{logs,config,scripts}`.<br>Copy/move files across directories and verify using `tree` or `ls -R`. |              |                                                                                                     |
| **Viewing & Editing Files**     | `cat`, `less`, `head`, `tail`, `nano` or `vim`.    | Create `demo.txt` with at least 20 lines; use `head`, `tail`, `less +10` to navigate.<br>Edit with `vim` to add a header.                       |              |                                                                                                     |
| **Wildcards & Globs**           | `*`, `?`, `[a-z]`.                                 | Generate files `file1` to `file5` and use globs to delete only odd-numbered files.                                                              |              |                                                                                                     |
| **I/O Redirection**             | `>`, `>>`, `<`, `                                  | `, `tee`.                                                                                                                                       | Pipe `ps aux | grep ssh`to a file`ssh_processes.log`.<br>Append system uptime to `~/practice/system.log`using`>>`. |
| **Filters & Piping**            | `grep`, `cut`, `sort`, `uniq`, `wc`, `tr`.         | Extract usernames from `/etc/passwd` and sort them alphabetically.<br>Count total unique users.                                                 |              |                                                                                                     |

---

## **III. User & Permission Management**

| **Topic**                            | **Key Concepts**                                        | **Mandatory Hands-on (node-01)**                                                                               |
| ------------------------------------ | ------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------- |
| **User/Group Basics**                | `/etc/passwd`, `/etc/group`, UID/GID, `id`, `who`, `w`. | Create a user `devuser` and group `devteam`. Add `devuser` to `devteam`.                                       |
| **Privilege Escalation**             | `sudo`, `su`, `/etc/sudoers`, `visudo`.                 | Give `devuser` password-less sudo for `systemctl status`. Test it.                                             |
| **Ownership & Permissions**          | `chmod`, `chown`, `chgrp`, symbolic vs octal.           | Create `/opt/secure` owned by `devuser:devteam`, permissions `640`. Verify with `ls -l`.                       |
| **Access Control Scenario**          | Read/Write/Execute on files & directories.              | Create a shared folder `/opt/shared` readable by all users but writable only by members of `devteam`.          |
| **Sticky Bit & Special Permissions** | `chmod +t`, `setuid`, `setgid`.                         | Experiment with `/tmp`’s sticky bit.<br>Create an executable with `setuid` bit and observe permission changes. |

---

## **IV. Process & Job Management**

| **Topic**                      | **Key Concepts**                                                            | **Mandatory Hands-on (node-01)**                                                                  |                        |
| ------------------------------ | --------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------- | ---------------------- |
| **Process Inspection**         | `ps`, `top`, `htop`, `pgrep`, `pstree`.                                     | Identify parent/child process of `sshd` using `pstree`.                                           |                        |
| **Foreground/Background Jobs** | `&`, `jobs`, `fg`, `bg`, `Ctrl+Z`.                                          | Start a ping job in background; list it; bring it back to foreground.                             |                        |
| **Signals**                    | `kill`, `pkill`, `killall`, signal types (`SIGTERM`, `SIGKILL`, `SIGSTOP`). | Find and kill a running `sleep` process using `ps aux                                             | grep sleep`+`kill -9`. |
| **Scheduling**                 | `at`, `cron`, `/etc/cron.*`, `crontab -e`.                                  | Schedule a job to write `uptime` to `/tmp/uptime.log` every 2 minutes. Verify output.             |                        |
| **System Monitoring**          | `uptime`, `free`, `df`, `du`, `vmstat`, `iostat`.                           | Check CPU, memory, and disk stats. Create a log `syshealth.log` capturing these commands’ output. |                        |

---

## 🧠 **Mini Project #1 — “System Baseline Logger”**

**Goal:** Combine all Tier 1 knowledge into one practical automation.

**Scenario:** You’ve just been given SSH access to a fresh Linux node (`node-01`). Your task is to create a script that gathers a system snapshot for auditing.

**Requirements:**

1. Create a directory `/opt/sysaudit` owned by `devuser:devteam`.
2. Script name: `/opt/sysaudit/syscheck.sh`.
3. Script must collect:

   * Hostname, kernel version (`uname -a`), uptime, disk space, memory stats, top 5 CPU processes.
   * List of all users and their last login (`lastlog`).
   * Current cron jobs of root.
4. Output saved to `/opt/sysaudit/report-<date>.log`.
5. Schedule it to run daily at 1 AM using `cron`.
6. Restrict execute permissions to only `devteam` users.

**Outcome:**
By the end, you’ll understand *every core building block*—commands, permissions, users, scheduling, and process handling.

---

## ✅ **Completion Checklist for Tier 1**

* [ ] SSH access and aliases configured.
* [ ] Comfortable with navigating Linux FHS.
* [ ] Mastered file manipulation, redirection, piping.
* [ ] Understood users, groups, permissions (symbolic & octal).
* [ ] Can manage processes and jobs.
* [ ] Deployed and scheduled your first system script.

---

Would you like me to proceed with **Tier 2: System Administration & Networking (The Multi-Node Environment)** next — where you’ll start connecting `node-01`, `node-02`, and `node-03` to simulate a real Linux fleet (services, SSH trust, networking, NFS, etc.)?
