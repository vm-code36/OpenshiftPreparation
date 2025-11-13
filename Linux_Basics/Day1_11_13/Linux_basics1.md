Perfect, Viswa 💪 — this is the **true starting line** of every serious Linux engineer’s journey.
We’ll go through **Command-line Navigation** in a **hands-on, structured way**, just like a bootcamp.
Each concept will include:
1️⃣ Command purpose
2️⃣ Real-world usage
3️⃣ A practical exercise to run on `node-01`

---

# 🧭 **Module: Command-Line Navigation & File Handling**

---

## 🧩 1️⃣ The Basics — Moving Around Linux

### 🧠 Commands:

| Command | Description                     | Example               |
| ------- | ------------------------------- | --------------------- |
| `pwd`   | Print current working directory | `pwd` → `/home/viswa` |
| `ls`    | List files and directories      | `ls -lh /etc`         |
| `cd`    | Change directory                | `cd /var/log`         |
| `clear` | Clear terminal screen           | `clear`               |

### 🔧 Practice:

```bash
cd /etc
pwd
ls -l
ls -lh /var/log
cd ~
```

✅ **Mini-Task:**
Create a directory `/home/student/practice1`, navigate into it, and list its absolute path.

---

## 🧩 2️⃣ File Viewing & Inspection

### 🧠 Commands:

| Command   | Description                           | Example                      |
| --------- | ------------------------------------- | ---------------------------- |
| `cat`     | Concatenate and display file contents | `cat /etc/hostname`          |
| `less`    | Scroll through file (page by page)    | `less /etc/passwd`           |
| `head`    | Show first 10 lines                   | `head -n 5 /etc/passwd`      |
| `tail`    | Show last 10 lines                    | `tail -n 10 /var/log/syslog` |
| `tail -f` | Follow log output in real time        | `tail -f /var/log/messages`  |

### 🔧 Practice:

```bash
cat /etc/passwd
less /etc/passwd
head -n 3 /etc/passwd
tail -n 5 /etc/passwd
```

✅ **Mini-Task:**
Monitor `/var/log/syslog` in real time and open another terminal to ping Google (`ping google.com`). Observe how logs appear dynamically using `tail -f`.

---

## 🧩 3️⃣ File and Directory Operations

### 🧠 Commands:

| Command | Description              | Example                     |
| ------- | ------------------------ | --------------------------- |
| `mkdir` | Create directory         | `mkdir projects`            |
| `touch` | Create empty file        | `touch index.html`          |
| `cp`    | Copy files/directories   | `cp file1.txt backup/`      |
| `mv`    | Move or rename files     | `mv file.txt /tmp/`         |
| `rm`    | Remove files/directories | `rm file.txt`, `rm -r dir/` |

### 🔧 Practice:

```bash
cd ~/practice1
mkdir dir1 dir2
touch dir1/file1.txt dir1/file2.txt
cp dir1/file1.txt dir2/
mv dir2/file1.txt dir2/config.txt
rm dir1/file2.txt
```

✅ **Mini-Task:**
Create `/opt/testdir` with 3 subdirectories (`app`, `config`, `logs`).
Copy `/etc/hosts` into `config/` and rename it to `hosts_backup`.

---

## 🧩 4️⃣ Redirection & Piping — Combining Commands Like a Pro

### 🧠 Concepts:

* `>` → Redirect **output** (overwrite)
* `>>` → Redirect **output** (append)
* `<` → Redirect **input**
* `|` → **Pipe** output from one command to another

### 🔧 Examples:

```bash
echo "Hello Linux!" > hello.txt         # Create new file
echo "This is appended text" >> hello.txt
cat < hello.txt                         # Use input redirection
cat /etc/passwd | grep root             # Pipe output to grep
ls /etc | wc -l                         # Count number of files in /etc
```

✅ **Mini-Task:**

1. Save the output of `ps aux` into `process.txt`
2. Append the output of `df -h` to the same file
3. Count the total number of lines using `wc -l process.txt`

---

## 🧩 5️⃣ Text Filtering & Processing (Core for DevOps Scripts)

### 🧠 Commands:

| Command | Description                         | Example                   |                 |
| ------- | ----------------------------------- | ------------------------- | --------------- |
| `grep`  | Search text patterns                | `grep root /etc/passwd`   |                 |
| `sort`  | Sort text alphabetically            | `sort names.txt`          |                 |
| `uniq`  | Remove duplicates (use with `sort`) | `sort names.txt           | uniq`           |
| `cut`   | Extract specific columns            | `cut -d: -f1 /etc/passwd` |                 |
| `tr`    | Translate or delete characters      | `cat text.txt             | tr 'a-z' 'A-Z'` |
| `wc`    | Count lines, words, characters      | `wc -l /etc/passwd`       |                 |

### 🔧 Practice:

```bash
cat /etc/passwd | grep bash
cut -d: -f1 /etc/passwd | sort | uniq
ls /etc | wc -l
```

✅ **Mini-Task:**
List all usernames using `/etc/passwd`, sort them alphabetically, and count how many total users exist:

```bash
cut -d: -f1 /etc/passwd | sort | wc -l
```

---

## 🧩 6️⃣ Wildcards (Globbing)

### 🧠 Symbols:

| Symbol | Meaning                              | Example            |
| ------ | ------------------------------------ | ------------------ |
| `*`    | Matches zero or more characters      | `ls *.txt`         |
| `?`    | Matches a single character           | `ls file?.txt`     |
| `[ ]`  | Matches a range or set of characters | `ls file[1-3].txt` |

### 🔧 Practice:

```bash
cd ~/practice1
touch report1.txt report2.txt reportA.txt reportB.log
ls report*.txt      # all .txt files starting with 'report'
ls report?.txt      # single-character after 'report'
ls report[AB].log   # only reportA.log and reportB.log
```

✅ **Mini-Task:**
Create 5 files: `test1.txt` to `test5.txt`
Delete only even-numbered files using wildcards.

---

## 🧩 7️⃣ Combine Everything — Real-world Example

**Scenario:**
You’re troubleshooting a service and want to extract all lines containing `error` from `/var/log/syslog`, sort them, and count unique occurrences.

```bash
grep "error" /var/log/syslog | sort | uniq -c | sort -nr | head
```

**Explanation:**

* `grep "error"` → find error lines
* `sort | uniq -c` → combine duplicates and count
* `sort -nr | head` → show top 10 most common error messages

---

## 🧱 **Mini Project: “System Activity Analyzer”**

🧩 **Objective:**
Create a script `/opt/sys_activity.sh` that:

1. Prints current directory and logged-in user
2. Shows top 10 memory-consuming processes
3. Lists top 10 largest directories under `/var`
4. Counts total running processes
5. Saves report to `/opt/sys_activity_$(date +%F).log`

🧠 **Hint:**
Use commands you just learned:

```bash
pwd, whoami, ps aux, du -sh, sort, head, wc, tee
```

✅ Schedule it with cron to run every day at 7AM.

---

Would you like me to **generate a structured worksheet (Markdown or PDF)** for this module — including exercises, “fill-the-command” tasks, and the mini-project template you can print or practice step-by-step on `node-01`?
