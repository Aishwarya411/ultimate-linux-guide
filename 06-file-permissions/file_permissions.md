# Linux File Permissions

Linux file permissions are one of the most important Linux concepts. Every **DevOps engineer**, **backend developer**, or **system engineer** must understand them clearly.

They define **who can access files and what actions they can perform**.

---

# Table of Contents

1. Why Linux File Permissions Exist  
2. View File Permissions  
3. Structure of Linux Permissions  
4. File Type  
5. Permission Types  
6. User Categories  
7. Example Breakdown  
8. Directory Permissions  
9. Changing Permissions  
10. Numeric (Octal) Permissions  
11. Common Permission Numbers  
12. Changing File Owner  
13. Example Real Scenario (DevOps)  
14. Common Beginner Mistakes  
15. Default Permission and Umask  
16. What is Umask  
17. Sticky Bit  

---

# 1. Why Linux File Permissions Exist

Linux is a **multi-user operating system**, meaning multiple users can use the same system.

Permissions control:

- Who can **read a file**
- Who can **modify a file**
- Who can **execute a file**

> Without permissions, any user could delete or modify critical system files.

---

# 2. View File Permissions

Command:

```bash
ls -l
```

Example output:

```bash
-rwxr-xr-- 1 aishwarya devops 1200 Mar 15 10:20 script.sh
```

The **first 10 characters** represent the **file permissions**.

---

# 3. Structure of Linux Permissions

Example permission string:

```
-rwxr-xr--
│││││││││
││││││││└ others read
│││││││└ others write
││││││└ others execute
│││││└ group read
││││└ group write
│││└ group execute
││└ user read
│└ user write
└ user execute
```

Structure format:

```
[TYPE] [USER] [GROUP] [OTHERS]
```

Example:

```
- rwx r-x r--
```

---

# 4. File Type (First Character)

| Symbol | Meaning |
|------|------|
| `-` | Regular file |
| `d` | Directory |
| `l` | Symbolic link |
| `c` | Character device |
| `b` | Block device |

Example:

```
drwxr-xr-x
```

`d` indicates a **directory**.

---

# 5. Permission Types

| Symbol | Meaning | Action |
|------|------|------|
| r | Read | Open file |
| w | Write | Modify file |
| x | Execute | Run file |

---

# 6. User Categories

Linux divides permissions into **three user groups**.

## 6.1 User (Owner)

The person who created the file.

Example:

```
aishwarya
```

## 6.2 Group

Users belonging to the same group.

Example:

```
devops
```

## 6.3 Others

All other users on the system.

---

# 7. Example Breakdown

Permission:

```
-rwxr-xr--
```

Split:

```
rwx   r-x   r--
```

| Category | Permission | Meaning |
|--------|--------|--------|
| User | rwx | read write execute |
| Group | r-x | read execute |
| Others | r-- | read only |

---

# 8. Directory Permissions

Directory permissions behave differently.

| Permission | Meaning |
|------|------|
| r | list files |
| w | create or delete files |
| x | enter directory |

Example:

```
drwxr-xr-x
```

User can:

- Enter directory
- Create files
- List files

Others can:

- Enter directory
- Read files

But **cannot create files**.

---

# 9. Changing Permissions

Command:

```bash
chmod
```

Example:

```bash
chmod u+x script.sh
```

## Operators

| Operator | Meaning |
|------|------|
| + | add permission |
| - | remove permission |
| = | set exact permission |

## Symbols

| Symbol | Meaning |
|------|------|
| u | user |
| g | group |
| o | others |
| a | all |

Examples:

```bash
chmod u+x file.sh
chmod g-w file.txt
chmod a+r file.txt
chmod +r file.txt
```

---

# 10. Numeric (Octal) Permissions

| Permission | Value |
|------|------|
| r | 4 |
| w | 2 |
| x | 1 |

Examples:

```
rwx = 4+2+1 = 7
r-x = 4+0+1 = 5
r-- = 4
```

Example permission:

```
rwxr-xr-- → 754
```

Command:

```bash
chmod 754 script.sh
```

---

# 11. Common Permission Numbers

| Number | Meaning |
|------|------|
| 777 | full access to everyone |
| 755 | user full access, others read and execute |
| 644 | user read write, others read |
| 700 | user only access |

Examples:

```bash
chmod 755 script.sh
chmod 644 file.txt
```

---

# 12. Changing File Owner

Command:

```bash
chown
```

Change owner:

```bash
sudo chown aishwarya file.txt
```

Change owner and group:

```bash
sudo chown aishwarya:devops file.txt
```

Recursive change:

```bash
sudo chown -R aishwarya:devops folder
```

---

# 13. Example Real Scenario (DevOps)

Script:

```
deploy.sh
```

Current permission:

```
-rw-r--r--
```

The script **will not run** because it lacks execute permission.

Fix:

```bash
chmod +x deploy.sh
```

New permission:

```
-rwxr-xr-x
```

Now the script can run.

---

# 14. Common Beginner Mistakes

## 14.1 Giving 777

```bash
chmod 777 file
```

Anyone can modify the file.  
Avoid this unless testing.

## 14.2 Forgetting Execute Permission

```bash
chmod +x script.sh
```

Without execute permission, scripts cannot run.

---

# 15. Default Permission and Umask

Formula:

```
Final Permission = Base Permission − Umask
```

Example for files:

```
Base permission = 666
Umask = 022
Final permission = 644
```

Example for directories:

```
Base permission = 777
Umask = 022
Final permission = 755
```

---

# 16. What is Umask

Umask (User Mask) controls the **default permissions of new files and directories**.

Purpose:

Automatically restricts permissions so files are not created with open access.

Without umask:

```
files → 666
directories → 777
```

Commands:

Check current umask:

```bash
umask
```

Change umask:

```bash
umask 022
```

---

# 17. Sticky Bit

Sticky bit is applied to directories so **only the file owner can delete their files**, even if others have write permission.

Common example:

```
/tmp
```

Apply sticky bit:

```bash
chmod +t folder
```

Check sticky bit:

```bash
ls -l
```

Examples:

```
drwxrwxrwt
```

`t` means sticky bit with execute permission.

```
drwxr-xr-T
```

`T` means sticky bit without execute permission.
