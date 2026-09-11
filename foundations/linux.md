# Linux Filesystem

## Core Idea

Linux has **one filesystem tree** starting from:

```text
/
```

Everything exists somewhere under `/`.

---

## Essential Commands

```bash
pwd
```

→ Shows your **current directory**.

```bash
ls
```

→ Lists files and directories in the current location.

```bash
cd
```

→ Moves you to another directory.

```bash
find
```

→ Searches the filesystem for files/directories matching conditions.

```bash
locate
```

→ Quickly searches an **indexed database** for file paths.

```bash
cat
```

→ Displays the contents of a file.

```bash
less
```

→ Opens a file for comfortable **page-by-page reading**.

```bash
head
```

→ Shows the **beginning** of a file.

```bash
tail
```

→ Shows the **end** of a file.

```bash
grep
```

→ Searches **inside files** for matching text.

```bash
grep -R
```

→ Searches for text **recursively through a directory and its subdirectories**.

### Quick Mental Model

```text
pwd     → Where am I?
ls      → What's here?
cd      → Move
find    → Which files?
locate  → Where is this file?
cat     → Read it
less    → Read it comfortably
head    → Beginning
tail    → End
grep    → Which lines contain this text?
grep -R → Which files under this directory contain this text?
```

---

## Important Directories

| Path    | What belongs there?            |
| ------- | ------------------------------ |
| `/etc`  | System configuration           |
| `/var`  | Changing data, logs, cache     |
| `/tmp`  | Temporary files                |
| `/home` | Users' personal files          |
| `/usr`  | Programs and libraries         |
| `/opt`  | Optional/add-on software       |
| `/proc` | Kernel and process information |

### Security Perspective

```text
/etc    → configurations
/var    → logs + application data
/tmp    → temporary/writable data
/home   → user files
/proc   → processes + system information
```

`/proc` is a **virtual filesystem** — its contents represent information provided by the kernel.

---

## Useful Examples

```bash
ls -la
```

→ Lists files including hidden ones with detailed information.

```bash
find /etc -name "*.conf" 2>/dev/null
```

→ Finds `.conf` files under `/etc`.

```bash
grep "root" /etc/passwd
```

→ Finds lines containing `root` in `/etc/passwd`.

```bash
grep -R "password" /etc 2>/dev/null
```

→ Searches recursively for `password` inside files under `/etc`.

```bash
tail -f /var/log/syslog
```

→ Continuously watches a log as new lines are added.

---

## Pentester Mental Model

When you get access to an unfamiliar Linux machine:

```text
Where am I?
    ↓
What files exist?
    ↓
What configuration exists?
    ↓
What users/processes/services exist?
    ↓
What information can I access?
```

> **Don't memorize commands. Know what question each command answers.**

# Linux Permissions

## Core Idea

Linux permissions answer:

> **Who can do what with this file/directory?**

Three permission groups:

```text
user    → owner
group   → file's group
others  → everyone else
```

Three basic permissions:

```text
r → read
w → write
x → execute
```

---

## `ls -la`

```bash
ls -la
```

→ Shows detailed file information, including hidden files and permissions.

Example:

```text
-rwxr-x--- 1 kali hackers 1234 script.sh
```

Breakdown:

```text
-   rwx   r-x   ---
│    │     │     │
│   user  group others
│
└── file type
```

```text
- → regular file
d → directory
l → symbolic link
```

So:

```text
-rwxr-x---
```

means:

```text
user   → rwx
group  → r-x
others → ---
```

---

## Permissions

### Files

```text
r → read contents
w → modify contents
x → execute
```

### Directories

```text
r → list contents
w → create/delete/rename entries
x → enter/traverse directory
```

---

## `chmod`

```bash
chmod +x script.sh
```

→ Adds execute permission.

```bash
chmod -x script.sh
```

→ Removes execute permission.

```bash
chmod u+x script.sh
```

→ Adds execute permission for the owner.

```text
u → user
g → group
o → others
a → all
```

---

## Numeric Permissions

```text
r = 4
w = 2
x = 1
```

Therefore:

```text
rwx = 7
rw- = 6
r-x = 5
r-- = 4
```

Example:

```bash
chmod 755 script.sh
```

means:

```text
user   → rwx = 7
group  → r-x = 5
others → r-x = 5
```

Result:

```text
-rwxr-xr-x
```

Another common permission:

```bash
chmod 644 file.txt
```

```text
user   → rw-
group  → r--
others → r--
```

---

## `chown`

```bash
chown alice file.txt
```

→ Changes the file owner to `alice`.

```bash
chown alice:developers file.txt
```

→ Changes owner to `alice` and group to `developers`.

---

## Security Mindset

When examining a file:

```text
Who owns it?
    ↓
What group owns it?
    ↓
Who can read it?
    ↓
Who can modify it?
    ↓
Who can execute it?
```

A particularly interesting situation:

```text
Privileged process
       ↓
runs a file/script
       ↓
low-privileged user can modify it
```

This can potentially lead to **privilege escalation**.

---

## Quick Mental Model

```text
ls -la  → What permissions does it have?
chmod   → Change permissions
chown   → Change ownership

r → read
w → write
x → execute

user → owner
group → group members
others → everyone else
```

### Goal

Be able to look at:

```text
-rwxr-x---
```

and immediately say:

> **Owner:** read/write/execute
> **Group:** read/execute
> **Others:** no access

# Processes + Services

## Core Idea

Think:

```text
Service
   ↓
Process
   ↓
Port
```

Example:

```text
SSH service → sshd process → TCP 22
```

---

## Commands

```bash
ps
```

→ Shows processes associated with the current shell.

```bash
ps aux
```

→ Shows a detailed list of running processes and the users running them.

```bash
ps -ef
```

→ Shows processes with PID and PPID, helping you understand process parent/child relationships.

```bash
top
```

→ Displays running processes and system resource usage in real time.

```bash
htop
```

→ Interactive and easier-to-read process viewer.

```bash
systemctl status ssh
```

→ Shows the current status, main PID, and other information about the SSH service.

```bash
systemctl start ssh
```

→ Starts the SSH service.

```bash
systemctl stop ssh
```

→ Stops the SSH service.

```bash
systemctl restart ssh
```

→ Restarts the SSH service.

```bash
systemctl is-enabled ssh
```

→ Checks whether the service starts automatically at boot.

```bash
ss -tuln
```

→ Shows listening TCP/UDP ports.

```bash
sudo ss -tulpn
```

→ Shows listening ports **and the processes using them**.

```bash
ps -fp <PID>
```

→ Shows detailed information about a specific process.

---

## Important Terms

```text
PID  → Process ID
PPID → Parent Process ID
USER → User running the process
```

Example:

```text
root   PID 1234   PPID 1   sshd
```

→ `sshd` is running as `root`, its PID is `1234`, and its parent process is PID `1`.

---

## Investigation Flow

When you find an open port:

```text
Open port
    ↓
Which process?
    ↓
Which PID?
    ↓
Which user?
    ↓
Which service?
    ↓
What does it do?
```

Useful command:

```bash
sudo ss -tulpn
```

Then investigate the PID:

```bash
ps -fp <PID>
```

---

## Mental Model

```text
ps        → What processes exist?
top/htop  → What's happening right now?
systemctl → What services are running?
ss        → What ports are listening?
```

### Goal

Don't just say:

> "Port 8080 is open."

Be able to say:

> **"Port 8080 is being used by process X, PID Y, running as user Z, and it belongs to/service is started by X."**

# SSH

## Core Idea

SSH provides **secure remote access** to another machine.

```text
Your Machine
     ↓
    SSH
     ↓
Remote Machine
     ↓
   Shell
```

Default SSH port:

```text
TCP 22
```

---

## Commands

```bash
ssh user@IP
```

→ Connects to a remote machine through SSH.

```bash
scp file.txt user@IP:/path/
```

→ Securely copies a file to a remote machine using SSH.

```bash
scp user@IP:/path/file.txt .
```

→ Copies a remote file to the current directory.

```bash
ssh-keygen
```

→ Generates an SSH public/private key pair.

```bash
ssh-copy-id user@IP
```

→ Adds your public key to the remote user's `authorized_keys`.

---

## SSH Authentication

### Password

```text
Username + Password
        ↓
   SSH Server
        ↓
   Authentication
```

### Key Authentication

```text
Private Key → stays on client
Public Key  → stored on server
```

The server stores allowed public keys in:

```text
~/.ssh/authorized_keys
```

---

## Important `.ssh` Files

```text
~/.ssh/
├── id_ed25519          → private key — KEEP SECRET
├── id_ed25519.pub      → public key
└── authorized_keys     → public keys allowed to log in
```

---

## Permissions

Common secure permissions:

```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/id_ed25519
chmod 644 ~/.ssh/id_ed25519.pub
chmod 600 ~/.ssh/authorized_keys
```

```text
700 → .ssh directory
600 → private key
644 → public key
600 → authorized_keys
```

Why?

> SSH credentials should not be writable/readable by unauthorized users.

---

## Useful Checks

```bash
whoami
```

→ Shows which user you are.

```bash
hostname
```

→ Shows which machine you are currently on.

```bash
ls -la ~/.ssh
```

→ Shows SSH files and their permissions.

```bash
cat ~/.ssh/authorized_keys
```

→ Shows public keys allowed to authenticate as the current user.

```bash
sudo systemctl status ssh
```

→ Shows whether the SSH server is running.

---

## Security Mental Model

```text
SSH
 │
 ├── Remote shell
 │
 ├── File transfer (scp)
 │
 └── Authentication
       │
       ├── Password
       │
       └── SSH keys
             │
             ├── Private key → SECRET
             └── Public key  → Server
```

### Goal

Be able to explain:

> **Who am I connecting as? Which machine am I connecting to? How is authentication happening? Where is the authorized public key stored? Who can access the private key?**

# SSH — Secure Shell

**SSH (Secure Shell)** is a protocol used to securely connect to another computer remotely and access its terminal.

```text
Kali (SSH Client) ───────> Debian (SSH Server)
          ssh                    sshd
```

## Key Concepts

* **SSH Client:** starts the connection → `ssh`
* **SSH Server:** receives connections → `sshd`
* **Default Port:** `TCP 22`
* **Authentication:** proves who you are

  * Username + Password
  * SSH Keys

## Connecting

```bash
ssh username@IP
```

Example:

```bash
ssh alice@192.168.1.10
```

This means: connect to `192.168.1.10` as user `alice`.

## SSH Keys

Instead of a password, SSH can use a key pair:

```text
Private Key 🔒  → Client
Public Key      → Server
```

The server stores allowed public keys in:

```text
~/.ssh/authorized_keys
```

**The private key stays on the client and should never be shared.**

## Important Files

```text
~/.ssh/
├── id_ed25519          # Private key
├── id_ed25519.pub      # Public key
└── authorized_keys     # Allowed public keys
```

## SSH in Pentesting

If Nmap shows:

```text
22/tcp open ssh
```

It only means **an SSH service is running**.

It does **not** mean you can log in.

In a CTF/VulnHub machine:

```text
SSH found
   ↓
Authentication required
   ↓
Find valid credentials
   ↓
Try the credentials
   ↓
Get a remote shell
```

### Mental Model

> **SSH = Secure Remote Connection + Authentication + Remote Shell**

# Bash

**Goal:** I don't need to become a Bash developer. I need to understand and modify small Bash commands/scripts used for automation and pentesting.

## Basic Loop

```bash
for ip in 192.168.1.{1..254}; do
    ping -c 1 -W 1 $ip
done
```

* `for` → starts a loop
* `ip` → variable
* `in` → values to iterate over
* `{1..254}` → Bash expands this into 1, 2, 3 ... 254
* `do` → starts the commands inside the loop
* `$ip` → current value of the variable
* `done` → ends the loop

Mental model:

```text
for each IP
    ↓
run ping
    ↓
next IP
```

## Important Operators

### `|` — Pipe

Send the output of one command to another command.

```bash
ps aux | grep ssh
```

```text
command A → output → command B
```

### `>` — Redirect

Write output to a file and **overwrite** its previous content.

```bash
ls > files.txt
```

### `>>` — Append

Add output to the end of a file.

```bash
echo "hello" >> notes.txt
```

```text
>   overwrite
>>  append
```

### `&&` — AND

Run the second command **only if the first succeeds**.

```bash
mkdir test && cd test
```

### `||` — OR

Run the second command **if the first fails**.

```bash
ping -c 1 192.168.1.10 || echo "Host is down"
```

```text
&& → if first succeeds
|| → if first fails
```

### `$` — Variable Value

```bash
name="alice"
echo $name
```

`$name` means: **use the value stored in `name`.**

### `$(...)` — Command Substitution

Run a command and use its output as a value.

```bash
ip=$(hostname -I)
echo $ip
```

Mental model:

```text
$(command)
    ↓
run command
    ↓
take its output
    ↓
use the output here
```

## Pentesting Mental Model

When I see a Bash command, ask:

1. What commands are being executed?
2. What is the input?
3. What is the output?
4. Are variables being used?
5. Is output being piped or redirected?
6. Is a command being repeated?
7. What happens if the command succeeds or fails?

**Goal:** Understand Bash commands well enough to read, modify, and create small automation scripts.
