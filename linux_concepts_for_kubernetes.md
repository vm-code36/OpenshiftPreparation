
# 🐧 Linux Concepts to Learn Before Working on Kubernetes

A strong understanding of Linux is essential before diving into Kubernetes, especially when managing clusters, debugging workloads, or automating deployments.

---

## 🧠 1. Basic Linux Command Line Skills
| Concept       | Description                                  |
|---------------|----------------------------------------------|
| Navigation    | `cd`, `pwd`, `ls`, `tree`                    |
| File Ops      | `cp`, `mv`, `rm`, `mkdir`, `touch`           |
| File Viewing  | `cat`, `less`, `head`, `tail`, `wc`          |
| Searching     | `grep`, `find`, `locate`, `which`, `whereis` |
| Archiving     | `tar`, `gzip`, `zip`, `unzip`                |

---

## 🧾 2. File Permissions and Ownership
| Concept       | Description                                    |
|---------------|------------------------------------------------|
| Permissions   | `chmod`, numeric/symbolic modes                |
| Ownership     | `chown`, `chgrp`                               |
| Special Bits  | Sticky bit, SUID, SGID                         |
| Umask         | `umask` command for default permissions        |

---

## 🧍‍♂️ 3. User and Group Management
| Concept       | Description                                    |
|---------------|------------------------------------------------|
| User Mgmt     | `useradd`, `userdel`, `passwd`, `id`           |
| Group Mgmt    | `groupadd`, `usermod -aG`                      |
| Config Files  | `/etc/passwd`, `/etc/shadow`, `/etc/group`    |
| Sudo          | `visudo`, manage least privilege               |

---

## 🛠️ 4. Package Management
| Concept       | Description                                    |
|---------------|------------------------------------------------|
| RPM Systems   | `yum`, `dnf`, `rpm`                            |
| DEB Systems   | `apt`, `dpkg`                                  |
| Package Ops   | Install, remove, upgrade packages              |

---

## 📦 5. Process and Service Management
| Concept       | Description                                    |
|---------------|------------------------------------------------|
| Process Tools | `ps`, `top`, `htop`, `pgrep`                  |
| Killing       | `kill`, `killall`, `pkill`                    |
| Services      | `systemctl start/stop/status/enable`          |
| Logs          | `journalctl`, `dmesg`                         |

---

## 📂 6. Filesystem and Storage
| Concept       | Description                                    |
|---------------|------------------------------------------------|
| Mounting      | `mount`, `umount`, `/etc/fstab`               |
| Disk Usage    | `df`, `du`, `lsblk`, `blkid`                  |
| Filesystems   | ext4, xfs, tmpfs                              |
| Optional LVM  | `pvcreate`, `lvcreate`, `vgcreate`            |

---

## 🧠 7. Networking Basics
| Concept       | Description                                    |
|---------------|------------------------------------------------|
| Interfaces    | `ip a`, `ip r`, `ifconfig`                    |
| DNS Tools     | `nslookup`, `dig`, `host`                     |
| Connectivity  | `ping`, `telnet`, `curl`, `wget`, `nc`        |
| Ports         | `ss`, `netstat` (deprecated)                  |

---

## 🔐 8. SSH & Security Essentials
| Concept       | Description                                    |
|---------------|------------------------------------------------|
| SSH           | `ssh`, `scp`, `sftp`                          |
| SSH Keys      | `ssh-keygen`, `ssh-copy-id`                   |
| Firewalls     | `firewalld`, `iptables`, `ufw`                |
| SELinux       | Modes, `getenforce`, `setenforce`             |
| Audit         | `auditd`, logs in `/var/log/audit/audit.log`  |

---

## 🧵 9. Scripting and Automation
| Concept       | Description                                    |
|---------------|------------------------------------------------|
| Bash Scripting| Variables, loops, functions, conditionals     |
| Env Variables | `export`, `.bashrc`, `.bash_profile`          |
| Cron          | `crontab -e`, automation                      |
| Debugging     | `bash -x script.sh`                           |

---

## 🧩 10. Tools Used in Kubernetes Context
| Tool          | Use Case                                       |
|---------------|------------------------------------------------|
| `curl`, `wget`| API testing, downloading manifests             |
| `jq`, `yq`    | JSON/YAML manipulation in CI/CD                |
| `awk`, `sed`  | Log and file manipulation                      |
| `envsubst`    | Dynamic variable injection into YAML           |
| `rsync`, `scp`| File and config copy across nodes              |
| Containers    | Basics of `podman`, `docker`                   |

---

## ✅ Bonus: Real-World Debugging Tools
| Tool          | Use Case                                       |
|---------------|------------------------------------------------|
| `strace`      | Trace syscalls and diagnose command behavior   |
| `lsof`        | List open files and ports                      |
| `watch`       | Continuously monitor outputs                   |
| Performance   | `uptime`, `free`, `vmstat`, `iostat`, `mpstat` |
| Logs          | `logrotate` for rotation and size mgmt         |

---

## 🧭 Suggested Learning Path
1. Practice daily CLI tasks in a Linux VM (Ubuntu or CentOS).
2. Learn to debug systemd services and logs.
3. Automate repetitive tasks using shell scripts.
4. Use tools like `jq`, `awk`, and `curl` in practice.
5. Set up Minikube or K3s to start basic Kubernetes labs.

---

*Mastering these Linux concepts will make your Kubernetes journey significantly easier and more productive.*
