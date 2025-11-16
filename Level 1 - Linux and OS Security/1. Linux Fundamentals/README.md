# Day 1: Linux Fundamentals - Complete Deep Dive

## Table of Contents
1. [Introduction to Linux](#module-1-introduction-to-linux)
2. [Working with Files and Directories](#module-2-working-with-files-and-directories)
3. [User and Group Management](#module-3-user-and-group-management)
4. [File Permissions and Security](#module-4-file-permissions-and-security)
5. [Text Processing and Manipulation](#module-5-text-processing-and-manipulation)
6. [Shell Basics and Environment](#module-6-shell-basics-and-environment)
7. [Practical Exercises](#practical-exercises)

---

## Module 1: Introduction to Linux

### 1.1 What is Linux?

#### History and Evolution
- **1969**: UNIX created at AT&T Bell Labs by Ken Thompson and Dennis Ritchie
- **1983**: Richard Stallman announces GNU Project (GNU's Not Unix)
- **1991**: Linus Torvalds creates Linux kernel (version 0.01)
- **1992**: Linux kernel released under GPL license
- **Today**: Powers 90% of cloud infrastructure, Android devices, and supercomputers

#### Linux Kernel vs. Linux Distribution
- **Kernel**: Core of the OS, manages hardware and system resources
- **Distribution**: Kernel + GNU tools + package manager + desktop environment
- **Components**: Bootloader, init system, shell, utilities, applications

#### Popular Linux Distributions

**Debian-based:**
- **Ubuntu**: User-friendly, great for beginners, LTS releases
- **Debian**: Stable, reliable, basis for many distributions
- **Kali Linux**: Penetration testing and security auditing
- **Parrot OS**: Security-focused with privacy tools

**Red Hat-based:**
- **RHEL**: Enterprise-grade, commercial support
- **CentOS/Rocky Linux**: Free RHEL alternatives
- **Fedora**: Cutting-edge features, community-driven

**Independent:**
- **Arch Linux**: Rolling release, highly customizable
- **Gentoo**: Source-based, maximum optimization
- **Alpine Linux**: Lightweight, security-oriented

#### Linux vs. Windows: Key Differences

| Feature | Linux | Windows |
|---------|-------|---------|
| **Cost** | Free and open source | Commercial license required |
| **File System** | ext4, xfs, btrfs | NTFS, FAT32 |
| **Path Separator** | Forward slash (/) | Backslash (\) |
| **Case Sensitivity** | Yes | No |
| **Root User** | root (UID 0) | Administrator |
| **Package Management** | apt, yum, pacman | Windows Store, installers |
| **Configuration** | Text files in /etc | Registry + GUI |
| **Security Model** | Permissions-based | ACLs + permissions |

### 1.2 Getting Started with Linux

#### Setting Up a Linux VM

**VirtualBox Setup (Recommended for Beginners):**
```bash
# Download VirtualBox from virtualbox.org
# Download Ubuntu Server ISO from ubuntu.com

# VM Configuration:
- Name: Ubuntu-Security-Lab
- Type: Linux
- Version: Ubuntu (64-bit)
- Memory: 4096 MB (4 GB minimum)
- Hard Disk: 40 GB VDI (dynamically allocated)
- Network: NAT + Host-Only Adapter
- Processors: 2 CPUs
```

**VMware Workstation Setup:**
```bash
# Download VMware Workstation Player (free for personal use)
# Create New Virtual Machine
- Typical configuration
- Installer disc image: Ubuntu ISO
- Memory: 4 GB
- Disk: 40 GB
- Network: NAT
```

**First Boot Checklist:**
1. Update system: `sudo apt update && sudo apt upgrade -y`
2. Install essential tools: `sudo apt install -y vim git curl wget net-tools`
3. Install VM tools: `sudo apt install -y open-vm-tools` (VMware) or VirtualBox Guest Additions
4. Configure SSH: `sudo systemctl enable ssh && sudo systemctl start ssh`
5. Take a snapshot: "Fresh Install"

#### Understanding the Terminal

**Terminal vs. Shell vs. Console:**
- **Terminal**: The application window (GNOME Terminal, Konsole, xterm)
- **Shell**: Command interpreter (bash, zsh, fish)
- **Console**: Physical text-based interface (TTY)

**Common Shells:**
- **bash** (Bourne Again Shell): Default on most systems
- **zsh** (Z Shell): Enhanced features, popular with developers
- **fish** (Friendly Interactive Shell): User-friendly, auto-suggestions
- **sh** (Bourne Shell): Original UNIX shell, minimal

**Terminal Shortcuts:**
```bash
Ctrl + C        # Kill current process
Ctrl + Z        # Suspend current process
Ctrl + D        # Exit shell (EOF)
Ctrl + L        # Clear screen
Ctrl + A        # Move to beginning of line
Ctrl + E        # Move to end of line
Ctrl + U        # Delete from cursor to beginning
Ctrl + K        # Delete from cursor to end
Ctrl + W        # Delete word before cursor
Ctrl + R        # Reverse search command history
Ctrl + Shift + C # Copy (in terminal)
Ctrl + Shift + V # Paste (in terminal)
Tab             # Auto-complete
!!              # Repeat last command
!$              # Last argument of previous command
```

#### Command Structure and Syntax

**Basic Command Anatomy:**
```bash
command [options] [arguments]

# Examples:
ls -la /home/user
│  │   └─ argument (what to act on)
│  └───── options/flags (how to act)
└──────── command (what to do)

# Multiple options:
ls -l -a -h /home    # Separate flags
ls -lah /home        # Combined flags (same result)

# Long-form options:
ls --all --human-readable -l /home
```

**Getting Help:**
```bash
man command          # Manual pages (detailed)
command --help       # Quick help
info command         # Info pages (GNU documentation)
whatis command       # One-line description
apropos keyword      # Search man pages
type command         # Show command type
which command        # Show command location
whereis command      # Show binary, source, and man page locations
```

### 1.3 File System Navigation

#### Linux Directory Structure (FHS - Filesystem Hierarchy Standard)

```
/                    # Root directory (everything starts here)
├── bin/            # Essential user binaries (ls, cat, cp)
├── boot/           # Boot loader files (kernel, initrd)
├── dev/            # Device files (hard drives, USB, terminals)
├── etc/            # System configuration files
│   ├── passwd      # User account information
│   ├── shadow      # Encrypted passwords
│   ├── group       # Group information
│   ├── fstab       # Filesystem mount table
│   ├── hosts       # Static hostname lookup
│   └── ssh/        # SSH configuration
├── home/           # User home directories
│   └── username/   # Individual user directory
├── lib/            # Shared libraries for /bin and /sbin
├── media/          # Mount points for removable media
├── mnt/            # Temporary mount points
├── opt/            # Optional/third-party software
├── proc/           # Virtual filesystem (process/kernel info)
│   ├── cpuinfo     # CPU information
│   ├── meminfo     # Memory information
│   └── [PID]/      # Process-specific information
├── root/           # Root user's home directory
├── run/            # Runtime data (PIDs, sockets)
├── sbin/           # System binaries (admin commands)
├── srv/            # Service data (web, FTP)
├── sys/            # Virtual filesystem (device/driver info)
├── tmp/            # Temporary files (cleared on reboot)
├── usr/            # User programs and data
│   ├── bin/        # User commands
│   ├── lib/        # Libraries for /usr/bin
│   ├── local/      # Locally installed software
│   └── share/      # Shared data (docs, icons)
└── var/            # Variable data
    ├── log/        # Log files
    ├── mail/       # Mail spools
    ├── spool/      # Print queues, cron jobs
    └── www/        # Web server files
```

#### Absolute vs. Relative Paths

**Absolute Paths:**
```bash
# Start with / (root directory)
/home/user/documents/file.txt
/etc/ssh/sshd_config
/var/log/syslog

# Always work regardless of current directory
cd /home/user
cd /etc
cd /var/log
```

**Relative Paths:**
```bash
# Relative to current directory
documents/file.txt          # File in documents subdirectory
../other_user/file.txt      # File in sibling directory
../../etc/hosts             # File two levels up, then down

# Current directory: /home/user
cd documents                # Goes to /home/user/documents
cd ../other_user           # Goes to /home/other_user
cd ../../etc               # Goes to /etc
```

#### Essential Navigation Commands

**pwd - Print Working Directory:**
```bash
pwd                         # Show current directory
pwd -P                      # Show physical path (resolve symlinks)

# Example output:
/home/user/documents
```

**ls - List Directory Contents:**
```bash
# Basic usage:
ls                          # List files in current directory
ls /etc                     # List files in /etc
ls -l                       # Long format (detailed)
ls -a                       # Show hidden files (starting with .)
ls -h                       # Human-readable sizes (KB, MB, GB)
ls -R                       # Recursive (show subdirectories)
ls -t                       # Sort by modification time
ls -S                       # Sort by size
ls -r                       # Reverse order

# Combined options:
ls -lah                     # Long format, all files, human-readable
ls -ltr                     # Long format, time-sorted, reverse
ls -lhS                     # Long format, human-readable, size-sorted

# Advanced usage:
ls -i                       # Show inode numbers
ls -d */                    # List only directories
ls *.txt                    # List only .txt files
ls -l --time=atime          # Sort by access time
ls --color=auto             # Colorize output

# Example output of ls -lah:
# drwxr-xr-x  2 user group 4.0K Nov 16 10:30 .
# drwxr-xr-x 15 user group 4.0K Nov 15 14:20 ..
# -rw-r--r--  1 user group  220 Nov 16 10:30 .bashrc
# -rw-r--r--  1 user group 3.5K Nov 16 10:31 document.txt
```

**cd - Change Directory:**
```bash
cd /path/to/directory       # Change to specific directory
cd                          # Change to home directory
cd ~                        # Change to home directory
cd -                        # Change to previous directory
cd ..                       # Move up one directory
cd ../..                    # Move up two directories
cd /                        # Change to root directory

# Examples:
cd /var/log                 # Go to log directory
cd                          # Go home
cd -                        # Go back to /var/log
cd ~/documents              # Go to documents in home
```

**file - Determine File Type:**
```bash
file filename               # Show file type
file *                      # Show type of all files
file -b filename            # Brief mode (no filename)
file -i filename            # Show MIME type

# Examples:
file document.txt           # ASCII text
file image.jpg              # JPEG image data
file script.sh              # Bash script
file /bin/ls                # ELF 64-bit executable
```

**tree - Display Directory Structure:**
```bash
# Install tree:
sudo apt install tree       # Debian/Ubuntu
sudo yum install tree       # RHEL/CentOS

# Usage:
tree                        # Show tree of current directory
tree /etc                   # Show tree of /etc
tree -L 2                   # Limit depth to 2 levels
tree -d                     # Show only directories
tree -a                     # Show hidden files
tree -h                     # Human-readable sizes
tree -p                     # Show permissions
tree -u                     # Show owner
tree -D                     # Show last modification time

# Example output:
# .
# ├── documents
# │   ├── file1.txt
# │   └── file2.txt
# ├── downloads
# └── pictures
#     └── vacation
#         └── photo.jpg
```

#### Special Directories and Symbols

```bash
.                           # Current directory
..                          # Parent directory
~                           # Home directory ($HOME)
-                           # Previous directory
/                           # Root directory

# Examples:
ls .                        # List current directory
ls ..                       # List parent directory
cd ~                        # Go to home directory
cd -                        # Go to previous directory
cp file.txt .               # Copy file to current directory
cp file.txt ..              # Copy file to parent directory
cp file.txt ~/              # Copy file to home directory
```

#### Advanced Navigation Tips

**Directory Stack (pushd/popd):**
```bash
pushd /var/log              # Push current dir to stack, go to /var/log
pushd /etc                  # Push /var/log to stack, go to /etc
dirs                        # Show directory stack
popd                        # Return to previous directory
popd                        # Return to original directory
```

**Wildcards and Globbing:**
```bash
*                           # Match any characters
?                           # Match single character
[abc]                       # Match a, b, or c
[a-z]                       # Match any lowercase letter
[0-9]                       # Match any digit
[!abc]                      # Match anything except a, b, or c

# Examples:
ls *.txt                    # All .txt files
ls file?.txt                # file1.txt, fileA.txt, etc.
ls [abc]*.txt               # Files starting with a, b, or c
ls file[0-9].txt            # file0.txt through file9.txt
ls *[!0-9].txt              # Files not ending with digit before .txt
```

---

## Module 2: Working with Files and Directories

### 2.1 Creating Files and Directories

#### Creating Files

**touch - Create Empty Files:**
```bash
touch filename              # Create empty file
touch file1 file2 file3     # Create multiple files
touch -t 202311161030 file  # Set specific timestamp
touch -d "2023-11-16" file  # Set date
touch -a file               # Update access time only
touch -m file               # Update modification time only

# Examples:
touch notes.txt             # Create empty notes.txt
touch {1..10}.txt           # Create 1.txt through 10.txt
touch file{a,b,c}.txt       # Create filea.txt, fileb.txt, filec.txt
```

**Redirection - Create Files with Content:**
```bash
> filename                  # Create empty file (overwrite if exists)
>> filename                 # Append to file (create if doesn't exist)
echo "text" > file          # Write text to file
echo "more" >> file         # Append text to file
cat > file                  # Write from stdin (Ctrl+D to finish)

# Examples:
echo "Hello World" > greeting.txt
echo "Line 2" >> greeting.txt
cat > multiline.txt << EOF
Line 1
Line 2
Line 3
EOF
```

**Text Editors:**
```bash
# nano (beginner-friendly):
nano filename               # Open file in nano
# Ctrl+O: Save, Ctrl+X: Exit, Ctrl+K: Cut, Ctrl+U: Paste

# vim (powerful but steep learning curve):
vim filename                # Open file in vim
# i: Insert mode, Esc: Normal mode, :w: Save, :q: Quit, :wq: Save and quit

# vi (always available):
vi filename                 # Open file in vi
```

#### Creating Directories

**mkdir - Make Directories:**
```bash
mkdir dirname               # Create directory
mkdir -p path/to/dir        # Create parent directories as needed
mkdir -m 755 dirname        # Create with specific permissions
mkdir dir1 dir2 dir3        # Create multiple directories
mkdir -v dirname            # Verbose output

# Examples:
mkdir projects              # Create projects directory
mkdir -p ~/work/project1/src/main    # Create nested structure
mkdir {dir1,dir2,dir3}      # Create multiple directories
mkdir -p backup/{daily,weekly,monthly}  # Create backup structure
```

### 2.2 Viewing and Reading Files

**cat - Concatenate and Display:**
```bash
cat filename                # Display file contents
cat file1 file2             # Display multiple files
cat -n filename             # Show line numbers
cat -b filename             # Number non-empty lines
cat -s filename             # Squeeze blank lines
cat -A filename             # Show all characters (tabs, line endings)

# Examples:
cat /etc/passwd             # View user accounts
cat file1.txt file2.txt > combined.txt  # Combine files
cat -n script.sh            # View script with line numbers
```

**less - Page Through Files:**
```bash
less filename               # View file with pagination
less -N filename            # Show line numbers
less +F filename            # Follow mode (like tail -f)
less -S filename            # Don't wrap long lines

# Navigation in less:
Space       # Next page
b           # Previous page
/pattern    # Search forward
?pattern    # Search backward
n           # Next search result
N           # Previous search result
g           # Go to beginning
G           # Go to end
q           # Quit

# Examples:
less /var/log/syslog        # View system log
less +G /var/log/syslog     # Start at end of file
```

**more - Simple Pager:**
```bash
more filename               # View file page by page
more +10 filename           # Start at line 10
more -10 filename           # Show 10 lines at a time

# Navigation in more:
Space       # Next page
Enter       # Next line
q           # Quit
```

**head - View Beginning of File:**
```bash
head filename               # Show first 10 lines
head -n 20 filename         # Show first 20 lines
head -n -5 filename         # Show all except last 5 lines
head -c 100 filename        # Show first 100 bytes

# Examples:
head /etc/passwd            # View first users
head -n 5 /var/log/syslog   # View first 5 log entries
head -n 1 *.txt             # View first line of all .txt files
```

**tail - View End of File:**
```bash
tail filename               # Show last 10 lines
tail -n 20 filename         # Show last 20 lines
tail -n +5 filename         # Show from line 5 to end
tail -f filename            # Follow file (watch for new lines)
tail -F filename            # Follow file (retry if file is recreated)
tail -f file1 file2         # Follow multiple files

# Examples:
tail /var/log/syslog        # View recent log entries
tail -f /var/log/auth.log   # Monitor authentication attempts
tail -n 50 /var/log/apache2/access.log  # View last 50 web requests
```

### 2.3 Copying, Moving, and Deleting

**cp - Copy Files and Directories:**
```bash
cp source destination       # Copy file
cp file1 file2 dir/         # Copy multiple files to directory
cp -r dir1 dir2             # Copy directory recursively
cp -i source dest           # Interactive (prompt before overwrite)
cp -u source dest           # Update (copy only if newer)
cp -p source dest           # Preserve attributes (permissions, timestamps)
cp -v source dest           # Verbose output
cp -a source dest           # Archive mode (preserve everything)

# Examples:
cp file.txt backup.txt      # Create backup
cp -r /etc/ssh ~/ssh-backup # Backup SSH configuration
cp -p important.txt /backup/  # Copy preserving attributes
cp *.txt documents/         # Copy all .txt files
cp -a /var/www/html /backup/website  # Full website backup
```

**mv - Move and Rename:**
```bash
mv source destination       # Move or rename file
mv file1 file2 dir/         # Move multiple files
mv -i source dest           # Interactive mode
mv -u source dest           # Update only
mv -v source dest           # Verbose output
mv -n source dest           # No overwrite

# Examples:
mv oldname.txt newname.txt  # Rename file
mv file.txt documents/      # Move file to directory
mv *.log /var/log/archive/  # Move all log files
mv -i important.txt backup/ # Move with confirmation
```

**rm - Remove Files and Directories:**
```bash
rm filename                 # Delete file
rm file1 file2 file3        # Delete multiple files
rm -i filename              # Interactive (confirm each deletion)
rm -f filename              # Force (no confirmation)
rm -r directory             # Remove directory recursively
rm -rf directory            # Force remove directory (DANGEROUS!)
rm -v filename              # Verbose output
rm -- -filename             # Remove file starting with dash

# Examples:
rm temp.txt                 # Delete temp file
rm *.tmp                    # Delete all .tmp files
rm -i important.txt         # Delete with confirmation
rm -rf old_project/         # Delete directory and contents
rm -rf /                    # NEVER DO THIS! (Destroys system)

# Safe deletion practices:
rm -i *                     # Always confirm
ls -l file && rm file       # Check before deleting
mkdir ~/trash && mv file ~/trash/  # Move to trash instead
```

**rmdir - Remove Empty Directories:**
```bash
rmdir dirname               # Remove empty directory
rmdir -p path/to/dir        # Remove directory and empty parents
rmdir -v dirname            # Verbose output

# Examples:
rmdir empty_folder          # Remove empty folder
rmdir -p project/old/temp   # Remove if all are empty
```

### 2.4 Finding Files and Directories

**find - Search for Files:**
```bash
# Basic syntax:
find [path] [options] [expression]

# Find by name:
find /path -name "filename"         # Exact name
find /path -iname "filename"        # Case-insensitive
find /path -name "*.txt"            # Pattern matching
find /path -name "file?"            # Single character wildcard

# Find by type:
find /path -type f                  # Files only
find /path -type d                  # Directories only
find /path -type l                  # Symbolic links only

# Find by size:
find /path -size +100M              # Larger than 100MB
find /path -size -1k                # Smaller than 1KB
find /path -size 50M                # Exactly 50MB

# Find by time:
find /path -mtime -7                # Modified in last 7 days
find /path -mtime +30               # Modified more than 30 days ago
find /path -atime -1                # Accessed in last 24 hours
find /path -ctime -7                # Changed in last 7 days

# Find by permissions:
find /path -perm 644                # Exactly 644
find /path -perm -644               # At least 644
find /path -perm /u+w               # User writable

# Find by owner:
find /path -user username           # Owned by user
find /path -group groupname         # Owned by group
find /path -nouser                  # No valid owner

# Execute commands on results:
find /path -name "*.txt" -exec cat {} \;        # Cat each file
find /path -name "*.log" -exec rm {} \;         # Delete each file
find /path -type f -exec chmod 644 {} \;        # Change permissions
find /path -name "*.bak" -delete                # Delete directly

# Complex examples:
find /home -name "*.txt" -type f -mtime -7      # .txt files modified in last week
find /var/log -name "*.log" -size +10M -exec ls -lh {} \;  # Large log files
find / -perm -4000 -type f 2>/dev/null          # Find SUID files
find /tmp -type f -atime +7 -delete             # Delete old temp files
find . -name "*.py" -exec grep -l "import os" {} \;  # Python files importing os
```

**locate - Fast File Search:**
```bash
# Update database first:
sudo updatedb               # Update locate database

# Search:
locate filename             # Find file by name
locate -i filename          # Case-insensitive
locate -c filename          # Count matches
locate -r "pattern"         # Use regex
locate -e filename          # Only existing files

# Examples:
locate passwd               # Find all passwd files
locate -i readme            # Find README files (any case)
locate "*.conf" | grep ssh  # Find SSH config files
```

**which - Locate Command:**
```bash
which command               # Show path of command
which -a command            # Show all matches

# Examples:
which python                # /usr/bin/python
which ls                    # /bin/ls
which -a python             # Show all python installations
```

**whereis - Locate Binary, Source, and Manual:**
```bash
whereis command             # Show binary, source, and man page
whereis -b command          # Binary only
whereis -m command          # Manual only
whereis -s command          # Source only

# Examples:
whereis ls                  # ls: /bin/ls /usr/share/man/man1/ls.1.gz
whereis python              # Show all python-related files
```

### 2.5 File Links

**ln - Create Links:**
```bash
# Hard links:
ln source link              # Create hard link
ln file1 file2 file3 dir/   # Create multiple hard links

# Symbolic (soft) links:
ln -s source link           # Create symbolic link
ln -s /path/to/file link    # Absolute path recommended
ln -sf source link          # Force (overwrite existing)

# Differences:
# Hard link: Same inode, same data, can't cross filesystems
# Soft link: Different inode, pointer to file, can cross filesystems

# Examples:
ln data.txt data-link.txt   # Hard link
ln -s /var/log/syslog ~/syslog-link  # Symbolic link
ln -s /usr/bin/python3 /usr/local/bin/python  # Command alias
```

---

## Module 3: User and Group Management

### 3.1 Understanding User Accounts

#### User Types

**Root User (UID 0):**
- Superuser with unlimited privileges
- Can access all files and execute all commands
- Represented by # in prompt
- Home directory: /root

**System Users (UID 1-999):**
- Created for services and daemons
- No login shell (usually /sbin/nologin)
- Examples: www-data, mysql, sshd
- Used for security isolation

**Regular Users (UID 1000+):**
- Normal user accounts
- Limited privileges
- Home directory: /home/username
- Represented by $ in prompt

#### User Configuration Files

**/etc/passwd - User Account Information:**
```bash
# Format: username:x:UID:GID:comment:home:shell
root:x:0:0:root:/root:/bin/bash
user:x:1000:1000:John Doe:/home/user:/bin/bash
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin

# Fields explained:
# 1. Username
# 2. Password placeholder (x = in /etc/shadow)
# 3. User ID (UID)
# 4. Primary Group ID (GID)
# 5. Comment/Full name (GECOS field)
# 6. Home directory
# 7. Login shell

# View file:
cat /etc/passwd
grep username /etc/passwd
getent passwd username
```

**/etc/shadow - Encrypted Passwords:**
```bash
# Format: username:password:lastchange:min:max:warn:inactive:expire
root:$6$xyz...:18900:0:99999:7:::
user:$6$abc...:18900:0:99999:7:::

# Fields explained:
# 1. Username
# 2. Encrypted password ($6$ = SHA-512)
# 3. Days since 1970-01-01 password was last changed
# 4. Minimum days between password changes
# 5. Maximum days password is valid
# 6. Days before expiration to warn user
# 7. Days after expiration account is disabled
# 8. Days since 1970-01-01 account is disabled
# 9. Reserved field

# View (requires root):
sudo cat /etc/shadow
sudo grep username /etc/shadow
```

#### Creating and Managing Users

**useradd - Create New User:**
```bash
# Basic usage:
sudo useradd username                   # Create user with defaults
sudo useradd -m username                # Create with home directory
sudo useradd -m -s /bin/bash username   # Specify shell
sudo useradd -m -c "Full Name" username # Add comment
sudo useradd -m -G group1,group2 username  # Add to groups
sudo useradd -m -e 2024-12-31 username  # Set expiration date

# Advanced options:
sudo useradd -m -u 1500 username        # Specify UID
sudo useradd -m -d /custom/home username # Custom home directory
sudo useradd -m -k /etc/skel username   # Specify skeleton directory
sudo useradd -m -p encrypted_pass username # Set encrypted password

# Examples:
sudo useradd -m -c "John Doe" -s /bin/bash john
sudo useradd -m -G sudo,developers alice
sudo useradd -m -e 2024-06-30 tempuser
```

**adduser - Interactive User Creation (Debian/Ubuntu):**
```bash
sudo adduser username       # Interactive prompts for details
# Prompts for: password, full name, room number, work phone, home phone

# Example:
sudo adduser bob
# Creates user, home directory, and prompts for information
```

**usermod - Modify User Account:**
```bash
sudo usermod -l newname oldname         # Change username
sudo usermod -d /new/home -m username   # Change home directory
sudo usermod -s /bin/zsh username       # Change shell
sudo usermod -c "New Comment" username  # Change comment
sudo usermod -L username                # Lock account
sudo usermod -U username                # Unlock account
sudo usermod -e 2024-12-31 username     # Set expiration
sudo usermod -e "" username             # Remove expiration
sudo usermod -aG groupname username     # Add to group (append)
sudo usermod -G group1,group2 username  # Set groups (replace)

# Examples:
sudo usermod -aG sudo john              # Add john to sudo group
sudo usermod -s /bin/zsh alice          # Change alice's shell
sudo usermod -L tempuser                # Lock temporary user
```

**userdel - Delete User:**
```bash
sudo userdel username                   # Delete user (keep home)
sudo userdel -r username                # Delete user and home directory
sudo userdel -f username                # Force deletion

# Examples:
sudo userdel tempuser                   # Delete user, keep files
sudo userdel -r olduser                 # Delete user and all files
```

#### Password Management

**passwd - Change Password:**
```bash
passwd                      # Change your own password
sudo passwd username        # Change another user's password
sudo passwd -l username     # Lock user account
sudo passwd -u username     # Unlock user account
sudo passwd -d username     # Delete password (passwordless login)
sudo passwd -e username     # Expire password (force change on next login)
sudo passwd -S username     # Show password status

# Examples:
passwd                      # Change your password
sudo passwd john            # Set john's password
sudo passwd -e alice        # Force alice to change password
sudo passwd -S bob          # Check bob's password status
```

**chage - Change Password Aging:**
```bash
chage -l username           # List password aging information
sudo chage -M 90 username   # Max 90 days before password expires
sudo chage -m 7 username    # Min 7 days between password changes
sudo chage -W 14 username   # Warn 14 days before expiration
sudo chage -I 30 username   # Inactive 30 days after expiration
sudo chage -E 2024-12-31 username  # Account expires on date
sudo chage -d 0 username    # Force password change on next login

# Examples:
sudo chage -M 90 -m 7 -W 14 john    # Set password policy
sudo chage -E 2024-06-30 tempuser   # Set account expiration
chage -l $USER                       # View your password info
```

#### User Information Commands

```bash
whoami                      # Show current username
id                          # Show UID, GID, and groups
id username                 # Show info for specific user
groups                      # Show groups you belong to
groups username             # Show groups for specific user
who                         # Show logged-in users
w                           # Show logged-in users and activity
last                        # Show login history
lastlog                     # Show last login for all users
finger username             # Show user information (if installed)

# Examples:
whoami                      # Output: john
id                          # uid=1000(john) gid=1000(john) groups=1000(john),27(sudo)
groups john                 # john : john sudo developers
who                         # Show current users
last -n 10                  # Show last 10 logins
```

### 3.2 Group Management

#### Understanding Groups

**Purpose of Groups:**
- Organize users with similar access needs
- Simplify permission management
- Enable collaboration on shared resources
- Implement role-based access control

**Group Types:**
- **Primary Group**: Default group for user (GID in /etc/passwd)
- **Secondary Groups**: Additional groups user belongs to
- **System Groups**: For system services (GID < 1000)
- **User Groups**: For regular users (GID >= 1000)

#### Group Configuration Files

**/etc/group - Group Information:**
```bash
# Format: groupname:x:GID:members
root:x:0:
sudo:x:27:john,alice
developers:x:1001:john,bob,alice
www-data:x:33:

# Fields explained:
# 1. Group name
# 2. Password placeholder (x = in /etc/gshadow)
# 3. Group ID (GID)
# 4. Comma-separated list of members

# View file:
cat /etc/group
grep groupname /etc/group
getent group groupname
```

**/etc/gshadow - Group Passwords:**
```bash
# Format: groupname:password:admins:members
sudo:*::john,alice
developers:!:john:bob,alice

# Fields explained:
# 1. Group name
# 2. Encrypted password (* or ! = no password)
# 3. Group administrators
# 4. Group members

# View (requires root):
sudo cat /etc/gshadow
```

#### Creating and Managing Groups

**groupadd - Create New Group:**
```bash
sudo groupadd groupname                 # Create group
sudo groupadd -g 1500 groupname         # Specify GID
sudo groupadd -r groupname              # Create system group
sudo groupadd -f groupname              # Force (don't fail if exists)

# Examples:
sudo groupadd developers                # Create developers group
sudo groupadd -g 2000 admins            # Create with specific GID
sudo groupadd -r servicegroup           # Create system group
```

**groupmod - Modify Group:**
```bash
sudo groupmod -n newname oldname        # Rename group
sudo groupmod -g 2000 groupname         # Change GID

# Examples:
sudo groupmod -n devs developers        # Rename group
sudo groupmod -g 1500 admins            # Change GID
```

**groupdel - Delete Group:**
```bash
sudo groupdel groupname                 # Delete group

# Examples:
sudo groupdel oldgroup                  # Delete group
# Note: Cannot delete primary group of existing user
```

#### Adding/Removing Users from Groups

**usermod - Manage Group Membership:**
```bash
sudo usermod -aG groupname username     # Add user to group (append)
sudo usermod -G group1,group2 username  # Set groups (replace all)
sudo usermod -g groupname username      # Change primary group

# Examples:
sudo usermod -aG sudo john              # Add john to sudo group
sudo usermod -aG developers,testers alice  # Add to multiple groups
sudo usermod -G developers bob          # Set bob's groups (removes others)
```

**gpasswd - Group Administration:**
```bash
sudo gpasswd -a username groupname      # Add user to group
sudo gpasswd -d username groupname      # Remove user from group
sudo gpasswd -A username groupname      # Set group administrator
sudo gpasswd -M user1,user2 groupname   # Set group members

# Examples:
sudo gpasswd -a john developers         # Add john to developers
sudo gpasswd -d alice sudo              # Remove alice from sudo
sudo gpasswd -A john developers         # Make john group admin
```

**newgrp - Switch Primary Group:**
```bash
newgrp groupname            # Switch to group (creates new shell)
exit                        # Return to previous group

# Example:
newgrp developers           # Switch to developers group
touch file.txt              # File owned by developers group
exit                        # Return to original group
```

#### Viewing Group Information

```bash
groups                      # Show your groups
groups username             # Show user's groups
id -Gn                      # Show group names
id -G                       # Show group IDs
getent group groupname      # Show group details
lid -g groupname            # List users in group (if installed)

# Examples:
groups john                 # john : john sudo developers
id -Gn alice                # alice sudo developers testers
getent group sudo           # sudo:x:27:john,alice
```

## Practical Exercises
1. **Basic Navigation**
   - Navigate through the file system using relative and absolute paths
   - List files with different options (ls -l, ls -a, ls -lh)
   - Create a directory structure for a project

2. **File Operations**
   - Create, copy, move, and delete files and directories
   - Search for specific files using find and locate
   - Practice using wildcards (*, ?, [])

3. **Text Processing**
   - Create a text file and practice various text processing commands
   - Use grep to search for patterns in files
   - Combine commands using pipes to process text data

4. **User Management**
   - Create multiple user accounts with different properties
   - Set and change passwords
   - Create groups and manage group memberships

## Daily Challenge
Create a script that:
1. Creates a backup directory with the current date
2. Finds all .log files in /var/log
3. Copies them to the backup directory
4. Compresses the backup directory
5. Sets appropriate permissions

## Additional Resources
- [Linux Journey - The Command Line](https://linuxjourney.com/)
- [The Linux Command Line](https://linuxcommand.org/tlcl.php)
- [Linux File System Hierarchy](https://www.tldp.org/LDP/Linux-Filesystem-Hierarchy/html/)

## Next Steps
Tomorrow we'll dive deeper into system administration tasks including package management, process control, and system monitoring.
