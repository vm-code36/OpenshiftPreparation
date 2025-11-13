# 🕒 Cron Jobs Handbook — Simple Go‑To Reference

This document provides a clear, beginner‑friendly reference for understanding and using **cron jobs** in Linux. Use it as a quick handbook whenever you need to schedule automated tasks.

---

## ✅ What is Cron?

Cron is a **time-based job scheduler** in Linux used to automatically run scripts or commands at specific times.

The cron service runs in the background and reads scheduled tasks from:

* **User crontabs** (per-user schedules)
* **System crontabs** (system-wide schedules)

---

## 📌 Types of Cron

### **1. User crontab**

Each user has their own cron schedule.

```
crontab -e     # edit your cron jobs
crontab -l     # list your cron jobs
crontab -r     # remove all cron jobs (dangerous!)
```

### **2. Root crontab**

Used for system-level tasks.

```
sudo crontab -e
sudo crontab -l
```

### **3. System Cron Files**

* `/etc/crontab`
* `/etc/cron.daily/`
* `/etc/cron.hourly/`
* `/etc/cron.weekly/`
* `/etc/cron.monthly/`
* `/etc/cron.d/`

---

## ⏱️ Cron Time Format (Must Know)

Cron uses a **5‑field schedule pattern**:

```
* * * * * command
│ │ │ │ └─── Day of week (0–7, Sun=0 or 7)
│ │ │ └────── Month (1–12)
│ │ └──────── Day of month (1–31)
│ └────────── Hour (0–23)
└──────────── Minute (0–59)
```

Meaning of symbols:

* `*` → every
* `,` → list (e.g. 1,15)
* `-` → range (e.g. 1-5)
* `/` → step (e.g. */5 = every 5 minutes)

---

## 🧩 Common Cron Examples

| Schedule                       | Cron Pattern   |
| ------------------------------ | -------------- |
| Every minute                   | `* * * * *`    |
| Every 5 minutes                | `*/5 * * * *`  |
| Every day at 7 AM              | `0 7 * * *`    |
| Every Sunday at 3:30 AM        | `30 3 * * 0`   |
| Every weekday at 10 PM         | `0 22 * * 1-5` |
| First day of month at midnight | `0 0 1 * *`    |

---

## 🛠️ Redirecting Output in Cron

Cron's environment is small and does **not** show errors on screen.

### **Standard outputs:**

* `1` = STDOUT (normal output)
* `2` = STDERR (error output)

### **Sending output to a log file:**

```
command > /path/file.log
```

### **Sending both STDOUT + STDERR:**

```
command > file.log 2>&1
```

This is **important** because cron hides errors unless redirected.

---

## 📁 Using Crontab with a File

You can install cron jobs from a file instead of editing interactively.

### Install cron from file (your user):

```
crontab mycronfile
```

### Install cron for another user (root only):

```
sudo crontab -u username mycronfile
```

> Note: The `-n` option is **not supported** on most Linux distributions (only on some UNIX systems).

---

## 🧪 Testing Cron Jobs

Use a simple test:

```
*/2 * * * * echo "Cron test at $(date)" >> /tmp/cron_test.log 2>&1
```

Wait 4–5 minutes, then check:

```
cat /tmp/cron_test.log
```

If entries appear → cron is working.

---

## ⚙️ Cron Environment Notes

Cron runs with a limited environment.

### Important rules:

* Always use **absolute paths** (`/usr/bin/ps`, `/usr/bin/df`)
* No aliases or functions
* No user profile (`~/.bashrc`) loaded

### Add PATH manually inside scripts:

```
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
```

---

## 🔍 Checking Cron Logs

Identify if cron actually ran your job.

### Ubuntu / Debian:

```
grep CRON /var/log/syslog
```

### CentOS / RHEL:

```
sudo cat /var/log/cron
```

---

## 🧰 Crontab Quick Reference

```
crontab -e       # edit your cron jobs
crontab -l       # list cron jobs
crontab -r       # remove cron jobs
crontab file     # install cron from a file
sudo crontab -u <user> file   # install cron for another user
```

---

## 🧱 Sample Cron Job Templates

### **Run script daily at 7 AM:**

```
0 7 * * * /opt/sys_activity.sh >> /opt/sys.log 2>&1
```

### **Run every 5 minutes:**

```
*/5 * * * * /usr/bin/uptime >> /tmp/uptime.log 2>&1
```

### **Run backup weekly:**

```
0 3 * * 0 tar -czf /backup/logs.tar.gz /var/log 2>&1
```

---

## 🎉 Summary: Key Cron Concepts

* Cron schedules commands automatically
* Crontab uses a 5‑field time format
* Use `sudo crontab -e` for root jobs
* Redirect STDERR using `2>&1` to capture errors
* Always use **absolute paths** in cron
* Check logs in `/var/log/syslog` or `/var/log/cron`

---

This is your **simple go-to handbook** — perfect for quick reference while practicing cron on your Linux nodes.
