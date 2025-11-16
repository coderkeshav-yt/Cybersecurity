# Day 1-5: Linux Fundamentals - Practice Questions & Answers

## Section 1: File System Navigation (10 Questions)

### Question 1
What command shows your current working directory?

**Answer:**
```bash
pwd
```

---

### Question 2
How do you list all files including hidden files in long format with human-readable sizes?

**Answer:**
```bash
ls -lah
```

---

### Question 3
What's the difference between `cd ~` and `cd -`?

**Answer:**
- `cd ~` - Changes to your home directory
- `cd -` - Changes to the previous directory you were in

---

### Question 4
How do you go up two directory levels from your current location?

**Answer:**
```bash
cd ../..
```

---

### Question 5
What does the following path represent: `/home/user/documents/file.txt`?

**Answer:**
This is an absolute path that starts from the root directory (/) and specifies the complete path to file.txt

---

### Question 6
How do you display the directory tree structure limited to 3 levels deep?

**Answer:**
```bash
tree -L 3
```

---

### Question 7
What command shows the file type of a file named `mystery_file`?

**Answer:**
```bash
file mystery_file
```

---

### Question 8
How do you list only directories in the current location?

**Answer:**
```bash
ls -d */
```

---

### Question 9
What directory contains system configuration files?

**Answer:**
`/etc` directory

---

### Question 10
How do you find the location of the `python` command?

**Answer:**
```bash
which python
```

---

## Section 2: File Operations (10 Questions)

### Question 11
How do you create an empty file named `test.txt`?

**Answer:**
```bash
touch test.txt
```

---

### Question 12
What's the command to create multiple files at once: file1.txt, file2.txt, file3.txt?

**Answer:**
```bash
touch file{1..3}.txt
```

---

### Question 13
How do you create a directory structure `project/src/main` in one command?

**Answer:**
```bash
mkdir -p project/src/main
```

---

### Question 14
What command displays the first 20 lines of a file?

**Answer:**
```bash
head -n 20 filename
```

---

### Question 15
How do you view the last 50 lines of `/var/log/syslog`?

**Answer:**
```bash
tail -n 50 /var/log/syslog
```

---

### Question 16
What command allows you to follow a log file in real-time?

**Answer:**
```bash
tail -f filename
```

---

### Question 17
How do you copy a directory and all its contents?

**Answer:**
```bash
cp -r source_dir destination_dir
```

---

### Question 18
How do you rename a file from `oldname.txt` to `newname.txt`?

**Answer:**
```bash
mv oldname.txt newname.txt
```

---

### Question 19
What's the safest way to delete a file with confirmation?

**Answer:**
```bash
rm -i filename
```

---

### Question 20
What command finds all `.log` files in `/var/log` directory?

**Answer:**
```bash
find /var/log -name "*.log"
```

---

## Section 3: User and Group Management (10 Questions)

### Question 21
What file contains user account information?

**Answer:**
`/etc/passwd`

---

### Question 22
What file contains encrypted user passwords?

**Answer:**
`/etc/shadow`

---

### Question 23
How do you create a new user with a home directory?

**Answer:**
```bash
sudo useradd -m username
```

---

### Question 24
What command changes a user's password?

**Answer:**
```bash
sudo passwd username
```

---

### Question 25
How do you add a user to the sudo group?

**Answer:**
```bash
sudo usermod -aG sudo username
```

---

### Question 26
What command deletes a user and their home directory?

**Answer:**
```bash
sudo userdel -r username
```

---

### Question 27
What command shows your current username?

**Answer:**
```bash
whoami
```

---

### Question 28
How do you display your user ID and group memberships?

**Answer:**
```bash
id
```

---

### Question 29
How do you create a new group named "developers"?

**Answer:**
```bash
sudo groupadd developers
```

---

### Question 30
What command adds a user to a group?

**Answer:**
```bash
sudo usermod -aG groupname username
```

---

## Section 4: File Permissions (10 Questions)

### Question 31
What do the three permission types (r, w, x) mean?

**Answer:**
- **r (read)**: Permission to read file contents or list directory contents
- **w (write)**: Permission to modify file or create/delete files in directory
- **x (execute)**: Permission to execute file or access directory

---

### Question 32
What does the permission string `-rw-r--r--` mean?

**Answer:**
- `-` = regular file
- `rw-` = owner can read and write
- `r--` = group can read only
- `r--` = others can read only

---

### Question 33
How do you change file permissions to 755 using numeric notation?

**Answer:**
```bash
chmod 755 filename
```

---

### Question 34
What does permission 755 mean?

**Answer:**
- **7 (owner)**: read (4) + write (2) + execute (1) = rwx
- **5 (group)**: read (4) + execute (1) = r-x
- **5 (others)**: read (4) + execute (1) = r-x

---

### Question 35
How do you give the owner execute permission using symbolic notation?

**Answer:**
```bash
chmod u+x filename
```

---

### Question 36
What command changes file ownership to user "john"?

**Answer:**
```bash
sudo chown john filename
```

---

### Question 37
How do you change both owner and group of a file?

**Answer:**
```bash
sudo chown john:developers filename
```

---

### Question 38
How do you recursively change permissions of a directory and all its contents?

**Answer:**
```bash
chmod -R 755 directory/
```

---

### Question 39
What is the SUID bit and how do you set it?

**Answer:**
SUID (Set User ID) allows a file to be executed with the permissions of the file owner.
```bash
chmod u+s filename
# or
chmod 4755 filename
```

---

### Question 40
What is the sticky bit and where is it commonly used?

**Answer:**
The sticky bit on a directory prevents users from deleting files they don't own. Commonly used on `/tmp`.
```bash
chmod +t directory
# or
chmod 1777 directory
```

---

## Section 5: Text Processing and Practical Scenarios (10 Questions)

### Question 41
How do you search for the word "error" in a file?

**Answer:**
```bash
grep "error" filename
```

---

### Question 42
What command searches for "error" case-insensitively?

**Answer:**
```bash
grep -i "error" filename
```

---

### Question 43
How do you count the number of lines in a file?

**Answer:**
```bash
wc -l filename
```

---

### Question 44
What's the pipe operator and what does it do?

**Answer:**
The pipe operator `|` takes the output of one command and uses it as input for another command.
Example: `ls -l | grep ".txt"`

---

### Question 45
How do you redirect both stdout and stderr to a file?

**Answer:**
```bash
command > file.txt 2>&1
# or
command &> file.txt
```

---

### Question 46
How do you find files larger than 100MB in your home directory?

**Answer:**
```bash
find ~ -type f -size +100M
```

---

### Question 47
What command finds files modified in the last 7 days?

**Answer:**
```bash
find /path -type f -mtime -7
```

---

### Question 48
What command creates a compressed tar archive of a directory?

**Answer:**
```bash
tar -czf archive.tar.gz directory/
```

---

### Question 49
How do you extract a tar.gz archive?

**Answer:**
```bash
tar -xzf archive.tar.gz
```

---

### Question 50
How do you find all files owned by user "john" in /home?

**Answer:**
```bash
find /home -user john
```

---

## Summary

**Total Questions: 50**

- Section 1: File System Navigation (10 questions)
- Section 2: File Operations (10 questions)
- Section 3: User and Group Management (10 questions)
- Section 4: File Permissions (10 questions)
- Section 5: Text Processing and Practical Scenarios (10 questions)

**Practice Tips:**
1. Try each command in your Linux terminal
2. Experiment with different options and flags
3. Create test files and directories to practice safely
4. Use `man command` to learn more about each command
5. Practice in a VM to avoid damaging your system
6. Review questions you got wrong
7. Time yourself to improve speed

**Scoring Guide:**
- 45-50 correct: Excellent! Ready for Day 2
- 35-44 correct: Good! Review weak areas
- 25-34 correct: Fair. More practice needed
- Below 25: Review the material and try again

**Suggested Study Schedule:**
- **Day 1**: File System Navigation (Questions 1-10)
- **Day 2**: File Operations (Questions 11-20)
- **Day 3**: User and Group Management (Questions 21-30)
- **Day 4**: File Permissions (Questions 31-40)
- **Day 5**: Text Processing and Practical Scenarios (Questions 41-50)

**Next Steps:**
- Complete 10 questions per day
- Practice commands in your terminal daily
- Review previous days before moving forward
- After Day 5, move on to Day 6-10: System Administration
- Create your own test scenarios
