# Linux `ps` Command (Process Status)

The **`ps` (Process Status)** command provides a **static snapshot of currently running processes** on a Linux system.

Unlike tools such as `top`, which show **real-time updates**, `ps` captures **exactly what processes are running at the moment you execute the command**.

This makes it extremely useful for **process inspection, debugging, and system monitoring**.

---

# Table of Contents

1. Basic Usage (The "Just Me" View)  
2. Seeing Everything (The "Global" View)  
3. Process States (STAT Column)  
4. Essential Filtering and Sorting  
5. Pro Tip: Using `grep` with `ps`

---

# 1. Basic Usage (The "Just Me" View)

Running `ps` **without any options** shows only the processes started by **you in the current terminal session**.

Command:

```bash
ps
```

Example output columns:

| Column | Meaning |
|------|------|
| PID | Unique Process ID |
| TTY | Terminal where the process is running |
| TIME | Total CPU time consumed by the process |
| CMD | Command that started the process |

Example output:

```bash
  PID TTY          TIME CMD
 1320 pts/0    00:00:00 bash
 1458 pts/0    00:00:00 ps
```

This view is useful when you want to **see processes related only to your current shell**.

---

# 2. Seeing Everything (The "Global" View)

Most administrators need to view **all processes on the system**, not just those in the current terminal.

There are **two common ways** to display all running processes.

---

## 2.1 Using `ps aux` (BSD Style)

Command:

```bash
ps aux
```

This is the **most popular command** for viewing detailed process information.

Options explained:

| Option | Meaning |
|------|------|
| a | Shows processes for all users |
| u | Displays user-oriented format (includes CPU and memory usage) |
| x | Shows processes not attached to a terminal (background services) |

Example:

```bash
ps aux
```

This command shows:

- User
- Process ID
- CPU usage
- Memory usage
- Command running

---

## 2.2 Using `ps -ef` (Standard / UNIX Style)

Command:

```bash
ps -ef
```

This provides a **clean full-format listing**, often used to analyze **parent-child process relationships**.

Options explained:

| Option | Meaning |
|------|------|
| -e | Selects all processes |
| -f | Displays full-format listing |

Important additional column:

| Column | Meaning |
|------|------|
| PPID | Parent Process ID |

Example:

```bash
ps -ef
```

This format is commonly used when **tracking which process started another process**.

---

# 3. Process States (STAT Column)

In `ps` output, the **STAT or S column** shows the **current state of a process** using a single-letter code.

These states describe **what the process is doing at that exact moment**.

| Code | State | Description |
|------|------|------|
| R | Running / Runnable | Process is currently running or waiting for CPU time |
| S | Interruptible Sleep | Waiting for an event (like user input) |
| D | Uninterruptible Sleep | Waiting for critical I/O such as disk access |
| T | Stopped | Process is paused (for example with `Ctrl + Z`) |
| Z | Zombie | Process finished but still exists because the parent hasn't collected its exit status |

Example process entry:

```bash
root     1201  0.0  0.1  50000  4000 ?        S    10:30   0:00 sshd
```

Here:

```
S = Interruptible Sleep
```

---

# 4. Essential Filtering and Sorting

When running `ps aux` or `ps -ef`, the output can contain **hundreds of processes**.

These options help **filter and sort the output** to find what you need.

---

## 4.1 Find Process by Name

Command:

```bash
ps -C <process_name>
```

Example:

```bash
ps -C python
```

Shows only **Python processes**.

---

## 4.2 Find Processes by User

Command:

```bash
ps -u <username>
```

Example:

```bash
ps -u root
```

Shows all processes owned by the **root user**.

---

## 4.3 Find Process by PID

Command:

```bash
ps -p <PID>
```

Example:

```bash
ps -p 1234
```

Displays information for **one specific process**.

---

## 4.4 Display Process Tree

Command:

```bash
ps --forest
```

This shows a **visual hierarchy** of processes.

Example:

```bash
ps -ef --forest
```

Example output:

```
systemd
 ├─sshd
 │   └─bash
 │       └─ps
```

This helps identify **parent-child relationships between processes**.

---

## 4.5 Sort Processes by Resource Usage

### Sort by Highest Memory Usage

```bash
ps aux --sort=-%mem | head
```

This shows **top memory-consuming processes**.

---

### Sort by Highest CPU Usage

```bash
ps aux --sort=-%cpu | head
```

This shows **top CPU-consuming processes**.

---

# 5. Pro Tip: Using `grep` with `ps`

The **most common way to search for a specific process** is by piping `ps` output to `grep`.

Example:

```bash
ps -ef | grep firefox
```

This command:

1. Lists all processes
2. Filters them for the word **firefox**

Example output:

```bash
user   2351  1800  0 10:45 ? 00:00:02 firefox
```

This technique is widely used for **quick process identification and troubleshooting**.

---

# Summary

The `ps` command is an essential **Linux process monitoring tool**.

It allows you to:

- View running processes
- Identify resource usage
- Track parent-child process relationships
- Filter processes by name, user, or PID

Commonly used commands:

```bash
ps
ps aux
ps -ef
ps aux --sort=-%mem
ps -ef | grep <process>
```

Understanding `ps` is important for **system administration, DevOps, and troubleshooting running applications**.


-----------------------------------------------------------------------------------------------------------------------------------------------------------------------
# Linux `kill` Command

In Linux, **`kill`** is a command used to **send signals to running processes**.  
Most commonly, it is used to **terminate a process**.

A signal tells a process **what action it should perform**, such as stopping, pausing, or reloading.

---

## 1. Basic Syntax

```bash
kill [signal] PID
```

- **signal** → type of signal to send  
- **PID** → Process ID of the target process

Example:

```bash
kill 1234
```

This sends the **default signal `SIGTERM (15)`** to process `1234`.

---

## 2. Common Signals

| Signal | Number | Purpose |
|------|------|------|
| SIGTERM | 15 | Gracefully stop a process (default) |
| SIGKILL | 9 | Force kill immediately |
| SIGHUP | 1 | Reload configuration |
| SIGSTOP | 19 | Pause a process |
| SIGCONT | 18 | Resume a paused process |

---

## 3. Graceful Termination (SIGTERM)

Command:

```bash
kill 1234
```

This sends **SIGTERM (signal 15)**.

The program receives the signal and **gets a chance to shut down safely**.

Typical cleanup actions include:

- Save unsaved data
- Close open files
- Close network connections
- Delete temporary files
- Release memory or locks

After completing these tasks, the process **exits safely**.

---

## 4. Force Kill (SIGKILL)

Command:

```bash
kill -9 1234
```

This sends **SIGKILL (signal 9)**.

In this case, the **Linux kernel immediately stops the process**.

The program **cannot catch or handle this signal**, meaning it **cannot run cleanup code**.

Possible problems:

- Unsaved work lost
- Corrupted files
- Temporary files left behind
- Database inconsistency
- Locked resources remaining

Because of these risks, **SIGKILL should be used only when SIGTERM fails**.

---

## 5. Kill by Process Name

Instead of using a PID, you can kill processes **by their name**.

### Using `pkill`

```bash
pkill firefox
```

### Using `killall`

```bash
killall firefox
```

Both commands **terminate processes matching the given name**.

---

## 6. Quick DevOps Workflow

Find a process:

```bash
ps -ef | grep nginx
```

Kill the process gracefully:

```bash
kill <PID>
```

Force kill if needed:

```bash
kill -9 <PID>
```

---

## Summary

The **`kill` command** is essential for managing processes in Linux.

Key points:

- `kill` sends **signals** to processes
- `SIGTERM (15)` allows **safe shutdown**
- `SIGKILL (9)` **forces immediate termination**
- `pkill` and `killall` allow **killing by process name**

Understanding signals helps **system administrators and DevOps engineers manage applications safely and efficiently**.

