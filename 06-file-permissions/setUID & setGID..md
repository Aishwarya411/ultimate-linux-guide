# SUID (Set User ID) in Linux

**SUID** stands for **Set User ID**.

It is a **special Linux file permission** that allows a user to **run a PROGRAM with the privileges of the file owner**, instead of the privileges of the user who executes the program.
So SUID is applied to executable files

If the program is owned by **root**, it will temporarily run with **root privileges**.

---

# Table of Contents

1. What Exactly SUID Does  
2. How SUID Appears in File Permissions  
3. Example: `passwd` Command  
4. How to Set SUID  
5. Where SUID Is Needed  
6. What Problem SUID Solves  
7. Security Risks of SUID  
8. Related Special Permissions  
9. Simple Analogy  
10. Short Definition  

---

# 1. What Exactly SUID Does

Normally in Linux:

- A program runs with the **permissions of the user who executes it**.

With **SUID enabled**:

- The program runs with the **permissions of the file owner**.

If the owner of the file is **root**, the program temporarily runs with **root privileges**.

This allows programs to perform **restricted system tasks safely**.

---

# 2. How SUID Appears in File Permissions

Example command:

```bash
ls -l /usr/bin/passwd
```

Example output:

```
-rwsr-xr-x 1 root root 68248 Jan 10 /usr/bin/passwd
```

Notice the **`s`** in the owner execute position:

```
rws
```

Explanation:

- `r` → read
- `w` → write
- `s` → execute with **SUID enabled**

The `s` indicates that **SUID bit is set**.

---

# 3. Example: `passwd` Command

Command:

```bash
passwd
```

Purpose:

Change the user password.

Passwords are stored in:

```
/etc/shadow
```

Permissions of `/etc/shadow`:

```
-rw------- root root
```

This means:

- Only **root** can read or modify the file.

---

## 3.1 The Problem

Normal users **cannot modify `/etc/shadow`**.

If they could, it would be a **major security risk**.

---

## 3.2 The Solution

The `passwd` program has **SUID enabled** and is owned by **root**.

When a user runs:

```bash
passwd
```

The program temporarily runs **with root privileges**.

This allows the program passwd to **update `/etc/shadow` safely**.

Users still **do not get full root access**, only the specific operation needed.
When you run passwd

You type: passwd

The passwd program executes. --> Because of SUID, it runs with root privileges, not your normal user privileges.--> Now it can edit /etc/shadow, even though you cannot touch that file yourself.
---

# 4. How to Set SUID

There are **two ways** to set the SUID bit.

---

## 4.1 Method 1 — Symbolic Method

```bash
chmod u+s program
```

Example:

```bash
chmod u+s myprogram
```

---

## 4.2 Method 2 — Numeric Method

```bash
chmod 4755 program
```

Explanation:

```
4 = SUID
755 = normal permissions
```

Example permission after setting SUID:

```
-rwsr-xr-x
```

---

# 5. Where SUID Is Needed

Some programs require **privileged operations**, but must be executed by **normal users**.

Common SUID programs:

| Program | Purpose |
|------|------|
| `passwd` | update `/etc/shadow` |
| `sudo` | run commands as root |
| `ping` (older systems) | raw network access |
| `su` | switch users |
| `mount` / `umount` | mount storage devices |

These programs perform **system-level operations**, but users must be able to run them.

---

# 6. What Problem SUID Solves

Without SUID:

- Users would need **root access** for many everyday tasks.
- This would be **dangerous and impractical**.

SUID provides:

- Controlled **privilege escalation**
- Secure access to **root-required operations**
- Better **system security design**

It allows **specific privileged operations without granting full root access**.

---

# 7. Security Risks of SUID

SUID programs are **very sensitive from a security perspective**.

If a SUID program contains a **bug or vulnerability**, attackers may gain **root privileges**.

Common attack types:

- Buffer overflow
- Path injection
- Environment variable exploitation

Because of this risk, system administrators **regularly audit SUID programs**.

---

## 7.1 Finding All SUID Files

Command:

```bash
find / -perm -4000 2>/dev/null
```

Explanation:

- `-perm -4000` → finds files with **SUID bit set**
- `2>/dev/null` → hides permission denied errors

---

# 8. Related Special Permissions

Linux has **three special permission bits**.

| Bit Value | Name | Purpose |
|------|------|------|
| 4 | SUID | run program as file owner |
| 2 | SGID | run program as group owner |
| 1 | Sticky Bit | restrict file deletion |

Example numeric permissions:

```
4755 → SUID
2755 → SGID
1755 → Sticky Bit
```

---

# 9. Simple Analogy

Think of SUID as **borrowing someone else's authority temporarily**.

Example scenario:

- Normally you **cannot open a bank vault**.
- But a **bank employee program** opens it **for you when required**.

You **do not get full control of the vault**, only the **specific action needed**.

This is similar to how **SUID allows limited privileged operations**.

---

# 10. Short Definition

**SUID allows a program to run with the permissions of its owner (often root) instead of the user executing it.**


`SUID`
File permission bit
Runs program as file owner
Often root

`sudo`
Command
Allows users to run commands as root
Controlled by sudoers policy


-------------------------------------------------------------------------------------------------------------------------------------------




