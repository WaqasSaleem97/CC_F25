# 🧪 Lab 6 – Linux Users, Groups, Permissions, Pipes, and Bash Scripting

Estimated Duration: 3–4 hours  
Instructions: Complete all tasks on the SAME Ubuntu Server VM used in Lab 5. Create a repository named `Lab6`. When finished, push your work to a repository named `CC_<student_Name>_<student_roll_number>`.

---

## 🎯 Objective

In this lab you will practice:

- Switching to the root account and back to a normal user
- Creating and managing users and groups
- Viewing account data in `/etc/passwd`, `/etc/group`, and `/etc/shadow`
- Setting primary and secondary groups; adding/removing supplemental group membership
- File ownership and permission management (symbolic and numeric modes)
- Using pipes, pagers, grep, and redirections (`>`, `>>`)
- Writing shell scripts with variables, conditionals, loops, and functions

---

## 🧩 Prerequisites

- The same Ubuntu Server VM used for Lab 5
- A sudo-capable user on the VM
- SSH access (recommended)
- Basic familiarity with terminal usage

Notes:
- On Ubuntu, the root account is disabled by default (no password set). For this lab, you will temporarily set a root password to use `su -`, then you may disable or leave as is after the lab (your choice).

---

## 📋 Task List (Lab 6)

- [Task 1: Switch to root with su - and back to a normal user](#task-1--switch-to-root-with-su---and-back-to-a-normal-user)
- [Task 2: Create user tom and verify in passwd/group/shadow](#task-2--create-user-tom-and-verify-in-passwdgroupshadow)
- [Task 3: Create groups; change tom’s primary and secondary groups](#task-3--create-groups-change-toms-primary-and-secondary-groups)
- [Task 4: Create/delete users (Jerry, Scooby) and groups (jolly, anime)](#task-4--createdelete-users-jerry-scooby-and-groups-jolly-anime)
- [Task 5: Create user Student; create files; set owner/group; identify file types](#task-5--create-user-student-create-files-set-ownergroup-identify-file-types)
- [Task 6: Change permissions using symbolic mode](#task-6--change-permissions-using-symbolic-mode)
- [Task 7: Change permissions using “set” symbolic form (u= g= o=)](#task-7--change-permissions-using-set-symbolic-form-u-g-o)
- [Task 8: Change permissions using numeric (octal) mode](#task-8--change-permissions-using-numeric-octal-mode)
- [Task 9: Practice pipes, pagers, grep, and redirects with /var/log/syslog](#task-9--practice-pipes-pagers-grep-and-redirects-with-varlogsyslog)
- [Task 10: Script setup.sh – variables, command substitution, file/dir checks, permissions (use vim)](#task-10--script-setupsh--variables-command-substitution-filedir-checks-permissions-use-vim)
- [Task 11: Script setup.sh – argument comparisons (eq, ne, gt, lt, ge, le) and string checks](#task-11--script-setupsh--argument-comparisons-eq-ne-gt-lt-ge-le-and-string-checks)
- [Task 12: Script setup.sh – print all arguments with a for loop](#task-12--script-setupsh--print-all-arguments-with-a-for-loop)
- [Task 13: Script setup.sh – while loop summation and a sum function](#task-13--script-setupsh--while-loop-summation-and-a-sum-function)
- [Submission](#submission)
- [Checklist (for students)](#checklist-for-students)
- [Troubleshooting & Notes](#troubleshooting--notes)

---

## Task 1 – Switch to root with su - and back to a normal user

Goal: Demonstrate switching to the root account using `su -` and exiting back to your normal user.

1) Set a root password (Ubuntu root is disabled by default; this enables `su -` temporarily for the lab):
```bash
sudo passwd root
# Enter a temporary root password for the lab
```
- Save screenshot as: `task1_set_root_password.png`

2) Switch to root and verify:
```bash
su -
whoami
id
```
- Save screenshot as: `task1_su_root.png`

3) Switch back to your normal user:
```bash
exit
whoami
```
- Save screenshot as: `task1_exit_to_user.png`

📸 Screenshots (summary list):
- `task1_set_root_password.png`
- `task1_su_root.png`
- `task1_exit_to_user.png`

---

## Task 2 – Create user tom and verify in passwd/group/shadow

Goal: Create a user named `tom`, then verify the account in system files.

1) Create user `tom` (interactive, sets password and home directory):
```bash
sudo adduser tom
```
- Save screenshot as: `task2_adduser_tom.png`

2) Verify `tom` in system files (view and visually confirm presence):
```bash
cat /etc/passwd
```
- Save screenshot as: `task2_verify_passwd.png`

```bash
cat /etc/group
```
- Save screenshot as: `task2_verify_group.png`

```bash
sudo cat /etc/shadow
```
- Save screenshot as: `task2_verify_shadow.png`

Notes:
- `/etc/shadow` stores password hashes (not plaintext). You must use `sudo` to read it.

📸 Screenshots (summary list):
- `task2_adduser_tom.png`
- `task2_verify_passwd.png`
- `task2_verify_group.png`
- `task2_verify_shadow.png`

---

## Task 3 – Create groups; change tom’s primary and secondary groups

Goal: Create groups `developer`, `devops`, and `designer`. Change `tom`’s primary group and manage secondary groups.

1) Create groups and verify by viewing `/etc/group` (visually confirm entries exist):
```bash
sudo groupadd developer
sudo groupadd devops
sudo groupadd designer
cat /etc/group
```
- Save screenshot as: `task3_groupadd.png`

2) Change `tom`’s primary group to `designer` and verify:
```bash
sudo usermod -g designer tom
id tom
```
- Save screenshot as: `task3_change_primary_group.png`

3) Add secondary groups `developer` and `devops` to `tom` and verify:
```bash
sudo usermod -aG developer,devops tom
id tom
groups tom
```
- Save screenshot as: `task3_add_secondary_groups.png`

4) Replace all secondary groups so only `tom` (user’s own group) remains and verify:
```bash
sudo usermod -G tom tom
id tom
groups tom
```
- Save screenshot as: `task3_reset_secondary_groups.png`

📸 Screenshots (summary list):
- `task3_groupadd.png`
- `task3_change_primary_group.png`
- `task3_add_secondary_groups.png`
- `task3_reset_secondary_groups.png`

---

## Task 4 – Create/delete users (Jerry, Scooby) and groups (jolly, anime)

Goal: Create users using both `adduser` and `useradd`, demonstrate login/password/home directory differences, then delete users/groups.

1) Create users:
```bash
sudo adduser Jerry
sudo useradd Scooby
```
- Save screenshot as: `task4_add_users.png`

2) Try to log in as Scooby immediately (expected authentication failure because there is no password yet):
```bash
su - Scooby
```
- Save screenshot as: `task4_scooby_su_auth_failure.png`

3) Set a password for Scooby:
```bash
sudo passwd Scooby
```
- Save screenshot as: `task4_set_password_scooby.png`

4) Try logging in as Scooby again (home directory still missing; expect a message such as “No directory, logging in with HOME=/”):
```bash
su - Scooby
```
- Save screenshot as: `task4_scooby_su_no_home.png`

5) Show that Scooby’s home directory does not exist yet and what `/etc/passwd` says:
```bash
exit
cat /etc/passwd
ls -ld /home/Scooby
```
- Save screenshot as: `task4_scooby_no_home.png`

6) Manually create Scooby’s home directory and set proper ownership and permissions:
```bash
sudo mkdir -p /home/Scooby
sudo chown Scooby:Scooby /home/Scooby
sudo chmod 750 /home/Scooby
# Optional: populate default skeleton files
# sudo cp -a /etc/skel/. /home/Scooby
# sudo chown -R Scooby:Scooby /home/Scooby
ls -ld /home/Scooby
```
- Save screenshot as: `task4_scooby_create_home.png`

7) Log in as Scooby again and verify you land in the correct home directory:
```bash
su - Scooby
pwd
ls -la
```
- Save screenshot as: `task4_scooby_login_success.png`

8) Verify users in system files:
```bash
exit
cat /etc/passwd
```
- Save screenshot as: `task4_verify_users.png`

9) Create groups:
```bash
sudo addgroup jolly
sudo groupadd anime
```
- Save screenshot as: `task4_add_groups.png`

10) Verify groups:
```bash
cat /etc/group
```
- Save screenshot as: `task4_verify_groups.png`

11) Delete groups and users:
```bash
sudo delgroup jolly
sudo groupdel anime
cat /etc/group

sudo deluser --remove-home Jerry
sudo userdel -r Scooby
cat /etc/passwd
```
- Save screenshots as: `task4_delete_groups.png`, `task4_delete_users.png`

---

## Task 5 – Create user Student; create files; set owner/group; identify file types

1) Create `Student`:
```bash
sudo adduser Student
```
- Save screenshot as: `task5_create_student.png`

2) Switch to Student and create files:
```bash
su - Student
touch file1
mkdir -p dir1
touch dir1/file2
ls -l
```
- Save screenshot as: `task5_create_files.png`

3) Change owner then group for file1 (separate commands):
```bash
sudo chown tom file1
ls -l file1

sudo chgrp devops file1
ls -l file1
```
- Save screenshots as: `task5_chown_file1.png`, `task5_chgrp_file1.png`

4) Identify files/directories and show /dev/null:
```bash
ls -l
ls -l dir1
ls -l /dev/null
file file1 dir1 /dev/null
```
- Save screenshot as: `task5_file_types.png`

5) Exit Student:
```bash
exit
```
- Save screenshot as: `task5_exit_student.png`

---

## Task 6 – Change permissions using symbolic mode

Target file: `~/file1` (run these as the Student user)

1) Ensure Student and file present:
```bash
su - Student
cd ~
ls -l file1
```
- Save screenshot as: `task6_su_student.png`

2) Remove all permissions:
```bash
chmod -rwx file1
ls -l file1
```
- Save screenshot as: `task6_chmod_remove_rwx.png`

3) Add read to all:
```bash
chmod +r file1
ls -l file1
```
- Save screenshot as: `task6_chmod_add_r.png`

4) Add execute to user:
```bash
chmod u+x file1
ls -l file1
```
- Save screenshot as: `task6_chmod_u_plus_x.png`

5) Add write to user and group:
```bash
chmod ug+w file1
ls -l file1
```
- Save screenshot as: `task6_chmod_ug_plus_w.png`

6) Remove all permissions (explicit):
```bash
chmod ugo-rwx file1
ls -l file1
```
- Save screenshot as: `task6_chmod_ugo_minus_rwx.png`

---

## Task 7 – Change permissions using “set” symbolic form (u= g= o=)

Ensure you are Student:
```bash
su - Student
cd ~
ls -l file1
```
- Save screenshot as: `task7_student_context.png`

1) Set all to rwx:
```bash
chmod u=rwx,g=rwx,o=rwx file1
ls -l file1
```
- Save screenshot as: `task7_chmod_set_all_rwx.png`

2) Remove execute from group and others:
```bash
chmod g=rw,o=rw file1
ls -l file1
```
- Save screenshot as: `task7_remove_exec_go.png`

3) Remove all permissions:
```bash
chmod u=,g=,o= file1
ls -l file1
```
- Save screenshot as: `task7_remove_all_perms.png`

---

## Task 8 – Change permissions using numeric (octal) mode

Ensure you are Student:
```bash
su - Student
cd ~
ls -l file1
```
- Save screenshot as: `task8_student_context.png`

Run each command and capture screenshot after each ls:

1)
```bash
chmod 777 file1
ls -l file1
```
- `task8_chmod_777.png`

2)
```bash
chmod 700 file1
ls -l file1
```
- `task8_chmod_700.png`

3)
```bash
chmod 744 file1
ls -l file1
```
- `task8_chmod_744.png`

4)
```bash
chmod 640 file1
ls -l file1
```
- `task8_chmod_640.png`

5)
```bash
chmod 664 file1
ls -l file1
```
- `task8_chmod_664.png`

6)
```bash
chmod 775 file1
ls -l file1
```
- `task8_chmod_775.png`

7)
```bash
chmod 750 file1
ls -l file1
```
- `task8_chmod_750.png`

---

## Task 9 – Practice pipes, pagers, grep, and redirects with /var/log/syslog

1) less:
```bash
sudo cat /var/log/syslog | less
# quit q
```
- `task9_grep_less.png`

2) more:
```bash
sudo cat /var/log/syslog | more
```
- `task9_grep_more.png`

3) grep failures/errors:
```bash
sudo grep -E 'fail|error' /var/log/syslog | head
```
- `task9_grep_head.png`

4) redirect:
```bash
sudo grep -i systemd /var/log/syslog > ~/syslog_systemd.txt
```
- `task9_redirect_overwrite.png`

append:
```bash
sudo grep -i network /var/log/syslog >> ~/syslog_systemd.txt
cat ~/syslog_systemd.txt
```
- `task9_redirect_append.png`

Alternative (journalctl) if needed:
```bash
sudo journalctl | less
sudo journalctl -u systemd | grep -i error > ~/journal_errors.txt
```
- `task9_journalctl_alternative.png`

---

## Task 10 – Script setup.sh – variables, command substitution, file/dir checks, permissions (use vim)

Goal: Using vim, write a script named `setup.sh` that implements each numbered step below. After writing the code for each step, run the script and capture screenshots showing the vim editor (script content) and the script output for that step. Students must add the code for each step into the same file `setup.sh` step-by-step (i.e., write 1., save, run and screenshot; then append 2., save, run and screenshot; and so on).

For each step you MUST:
- Open vim and edit setup.sh
- Insert only the code shown for that step (append to the existing file)
- Save and quit vim (:wq)
- Make the file executable if not already: chmod +x setup.sh
- Run the script: ./setup.sh
- Capture two screenshots:
  - One showing the vim editor with the script content after you added the step (use the vim screen before :wq)
  - One showing the terminal output after running the script (show the command and the output)

Start in your Student home directory (recommended).

1. Include bash shebang
- Code to add (enter in vim as the first line of the file):
```bash
#!/bin/bash
```
- Steps:
  1. vim setup.sh → add the shebang line → save and quit
  2. chmod +x setup.sh
  3. ./setup.sh
- Screenshots:
  - vim editor showing the shebang: `task10_b1_vim.png`
  - script run output (likely no output but show ./setup.sh run): `task10_b1_run.png`

2. Define variable var1 and echo it
- Code to append:
```bash
# Define and show var1
var1="Hello from Lab 6"
echo "var1: $var1"
```
- Steps:
  1. vim setup.sh → append the code above → save and quit
  2. ./setup.sh
- Screenshots:
  - vim editor showing var1 code appended: `task10_b2_vim.png`
  - script run output showing var1 printed: `task10_b2_run.png`

3. Save output of ls -l into variable allFiles and echo it
- Code to append:
```bash
# Save ls -l to variable and display
allFiles="$(ls -l)"
echo "allFiles (ls -l):"
echo "$allFiles"
```
- Steps:
  1. vim setup.sh → append the code above → save and quit
  2. ./setup.sh
- Screenshots:
  - vim editor showing allFiles code appended: `task10_b3_vim.png`
  - script run output showing the ls -l content echoed: `task10_b3_run.png`

4. If directory dir1 exists echo a message; else create it
- Code to append:
```bash
# Directory check
if [ -d "dir1" ]; then
  echo "Directory dir1 exists."
else
  echo "Directory dir1 does not exist. Creating..."
  mkdir -p "dir1"
  echo "Directory dir1 created."
fi
```
- Steps:
  1. vim setup.sh → append the code above → save and quit
  2. ./setup.sh
- Screenshots:
  - vim editor showing dir1 check code: `task10_b4_vim.png`
  - script run output showing directory message or creation: `task10_b4_run.png`

5. If file dir1/file2 does not exist, create it
- Code to append:
```bash
# File check
if [ -f "dir1/file2" ]; then
  echo "file2 already exists."
else
  echo "file2 does not exist. Creating..."
  touch "dir1/file2"
  echo "file2 created."
fi
```
- Steps:
  1. vim setup.sh → append the code above → save and quit
  2. ./setup.sh
- Screenshots:
  - vim editor showing file2 check code: `task10_b5_vim.png`
  - script run output showing file creation message or existence: `task10_b5_run.png`

6. Check read, write, execute permissions on dir1/file2; grant missing user perms and show final ls
- Code to append:
```bash
# Permission checks for dir1/file2 (user permissions)
f="dir1/file2"
if [ ! -r "$f" ]; then
  echo "Read permission missing; granting to user..."
  chmod u+r "$f"
fi

if [ ! -w "$f" ]; then
  echo "Write permission missing; granting to user..."
  chmod u+w "$f"
fi

if [ ! -x "$f" ]; then
  echo "Execute permission missing; granting to user..."
  chmod u+x "$f"
fi

echo "Final permissions for $f:"
ls -l "$f"
```
- Steps:
  1. vim setup.sh → append the code above → save and quit
  2. ./setup.sh
- Screenshots:
  - vim editor showing permission-check code: `task10_b6_vim.png`
  - script run output showing the permission grants and final `ls -l dir1/file2`: `task10_b6_run.png`

Important notes for Task 10:
- Students MUST add the code incrementally exactly as described above (do not replace the file each time — append).
- Capture the vim editor screen (showing your code in the buffer) before you save and quit for each step (these are the `task10_b*_vim.png` files).
- After saving, run the script and capture the terminal output for that run (these are the `task10_b*_run.png` files).
- If a step reports that a directory/file already exists, that is acceptable — still capture the output screenshot showing the script's message.

---

## Task 11 – Script setup.sh – argument comparisons (eq, ne, gt, lt, ge, le) and string checks

Updated: replace the previous single-script approach with an incremental exercise. Students will overwrite setup.sh and then add each individual if-test one-by-one. After adding each if-test they must run the script with example arguments and capture screenshots. This teaches the individual comparison operators and makes each if statement a separate step.

Important overall instructions
- Start by overwriting setup.sh (vim setup.sh) and add only what the step asks (do not add all tests at once).
- After editing in vim, save (:wq), make executable (chmod +x setup.sh) if needed, then run the script with the example commands shown for each step.
- For each step capture two screenshots:
  - A vim screenshot showing the current file buffer with the newly added lines (before :wq) — name as specified for the step.
  - A terminal screenshot showing the commands you ran (chmod +x setup.sh if necessary) and the script outputs for the example invocations — name as specified for the step.
- For the numeric comparisons, set a variable num=$1 at the top of the file before adding the individual if-tests (this will be the initial step). For string checks, set str=$2 before adding the string if-tests.

1. create file with shebang and set num and str variables
- In vim create/overwrite setup.sh and insert:
```bash
#!/bin/bash
num=$1
str=$2
```
- Save and quit (:wq)
- Make executable and run with examples:
```bash
chmod +x setup.sh
./setup.sh 10 Student
```
- Screenshots:
  - vim content: `task11_b0_vim.png`
  - run output: `task11_b0_run.png`

2. add the -eq test (equal)
- Append to setup.sh:
```bash
if [ "$num" -eq 10 ]; then
  echo "$num is equal to 10 (-eq)."
else
  echo "$num is NOT equal to 10 (-eq)."
fi
```
- Save and quit; then run these commands (capture both in one terminal screenshot):
```bash
./setup.sh 10 Student
./setup.sh 7 Student
```
- Screenshots:
  - vim content after edit: `task11_b1_vim.png`
  - run output demonstrating both cases: `task11_b1_run.png`

3. add the -ne test (not equal)
- Append to setup.sh:
```bash
if [ "$num" -ne 10 ]; then
  echo "$num is not equal to 10 (-ne)."
else
  echo "$num is equal to 10 (-ne false)."
fi
```
- Save and quit; run:
```bash
./setup.sh 7 Student
./setup.sh 10 Student
```
- Screenshots:
  - vim content: `task11_b2_vim.png`
  - run output: `task11_b2_run.png`

4. add the -gt test (greater than)
- Append:
```bash
if [ "$num" -gt 10 ]; then
  echo "$num is greater than 10 (-gt)."
else
  echo "$num is NOT greater than 10 (-gt)."
fi
```
- Run:
```bash
./setup.sh 12 Student
./setup.sh 9 Student
```
- Screenshots:
  - vim content: `task11_b3_vim.png`
  - run output: `task11_b3_run.png`

5. add the -lt test (less than)
- Append:
```bash
if [ "$num" -lt 10 ]; then
  echo "$num is less than 10 (-lt)."
else
  echo "$num is NOT less than 10 (-lt)."
fi
```
- Run:
```bash
./setup.sh 5 Student
./setup.sh 11 Student
```
- Screenshots:
  - vim content: `task11_b4_vim.png`
  - run output: `task11_b4_run.png`

6. add the -ge test (greater than or equal)
- Append:
```bash
if [ "$num" -ge 10 ]; then
  echo "$num is greater than or equal to 10 (-ge)."
else
  echo "$num is NOT greater than or equal to 10 (-ge)."
fi
```
- Run:
```bash
./setup.sh 10 Student
./setup.sh 8 Student
```
- Screenshots:
  - vim content: `task11_b5_vim.png`
  - run output: `task11_b5_run.png`

7. add the -le test (less than or equal)
- Append:
```bash
if [ "$num" -le 10 ]; then
  echo "$num is less than or equal to 10 (-le)."
else
  echo "$num is NOT less than or equal to 10 (-le)."
fi
```
- Run:
```bash
./setup.sh 10 Student
./setup.sh 12 Student
```
- Screenshots:
  - vim content: `task11_b6_vim.png`
  - run output: `task11_b6_run.png`

8. string equality test ( = )
- Ensure `str=$2` exists at top (1.). Append:
```bash
if [ "$str" = "Student" ]; then
  echo "Second argument equals 'Student' ( = )."
else
  echo "Second argument does NOT equal 'Student' ( = )."
fi
```
- Run:
```bash
./setup.sh 10 Student
./setup.sh 10 Test
```
- Screenshots:
  - vim content: `task11_b7_vim.png`
  - run output: `task11_b7_run.png`

9. string inequality test ( != )
- Append:
```bash
if [ "$str" != "Student" ]; then
  echo "Second argument is not equal to 'Student' ( != )."
else
  echo "Second argument equals 'Student' ( != false)."
fi
```
- Run:
```bash
./setup.sh 10 Test
./setup.sh 10 Student
```
- Screenshots:
  - vim content: `task11_b8_vim.png`
  - run output: `task11_b8_run.png`

10. check if second argument is empty (zero-length)
- Append:
```bash
if [ -z "$str" ]; then
  echo "Second argument is empty (zero-length)."
else
  echo "Second argument is not empty."
fi
```
- Run:
```bash
./setup.sh 10
./setup.sh 10 Student
```
- Screenshots:
  - vim content: `task11_b9_vim.png`
  - run output: `task11_b9_run.png`

Notes for Task 11
- Students MUST follow the incremental approach: add one test at a time and capture the vim buffer and run output screenshots for each step.
- Using the two example runs per step in a single terminal screenshot helps demonstrate both true and false results for the operator being taught.
- If a non-numeric value is used for $1 during integer comparisons the shell may print an error; that is expected here because we are demonstrating the operator behavior step-by-step. (If you want to avoid runtime errors, you can add integer validation before the comparisons — but for this exercise we are isolating each if-test into its own step.)

Screenshots summary for Task 11 (all required):
- `task11_b0_vim.png`
- `task11_b0_run.png`
- `task11_b1_vim.png`
- `task11_b1_run.png`
- `task11_b2_vim.png`
- `task11_b2_run.png`
- `task11_b3_vim.png`
- `task11_b3_run.png`
- `task11_b4_vim.png`
- `task11_b4_run.png`
- `task11_b5_vim.png`
- `task11_b5_run.png`
- `task11_b6_vim.png`
- `task11_b6_run.png`
- `task11_b7_vim.png`
- `task11_b7_run.png`
- `task11_b8_vim.png`
- `task11_b8_run.png`
- `task11_b9_vim.png`
- `task11_b9_run.png`

---

## Task 12 – Script setup.sh – print all arguments with a for loop

Clear the previous code of setup.sh and write a new script that prints all user-entered arguments using a for loop over $*.

Instructions (numbered):

1. Create the script with shebang and basic structure
- Open vim and overwrite setup.sh:
```bash
vim setup.sh
```
- Insert these lines (first step — shebang and a short comment):
```bash
#!/bin/bash
# Script to demonstrate printing all user-entered arguments using $*
```
- Save and quit (:wq)
- Screenshots:
  - vim editor showing the shebang and comment: `task12_b1_vim.png`
  - run (no output expected but show ./setup.sh run): `task12_b1_run.png`

2. Append the for loop using $* and print each argument
- Re-open setup.sh in vim and append the following lines:
```bash
# Print all arguments using $*
echo "Printing all arguments using \$*:"
for arg in $*; do
  echo "Argument: $arg"
done
```
- Save and quit (:wq)
- Make the script executable and run it with example arguments:
```bash
chmod +x setup.sh
./setup.sh one "two words" three
```
- Screenshots:
  - vim editor showing the for-loop appended: `task12_b2_vim.png`
  - script run output showing the printed arguments: `task12_b2_run.png`

Important notes for Task 12:
- The loop uses $* (unquoted) so it will split arguments on whitespace — this is intentional for the exercise so students can observe the difference between `$*` and `"$@"` in later tasks.
- Students must clear the previous contents of setup.sh before starting this task (overwrite in vim).
- Capture the vim editor screen (showing the buffer) before saving for each numbered step and the terminal output after running the script.

Screenshots summary for Task 12:
- `task12_b1_vim.png`
- `task12_b1_run.png`
- `task12_b2_vim.png`
- `task12_b2_run.png`

---

## Task 13 – Script setup.sh – while loop summation and a sum function

Clear the previous code of setup.sh and write a new script, step-by-step, that:

- Starts with a shebang line
- Implements a while loop that prompts the user to enter numbers and keeps a running sum until the user types q to quit
- Implements a function that prompts for two numbers, returns their sum via echo, and the script displays that returned sum
- Students must add the script code incrementally and capture vim buffer and run output screenshots for each numbered step

Instructions (numbered):

1. Add the shebang line
- Open vim and overwrite setup.sh with the shebang line:
```bash
#!/bin/bash
```
- Save and quit (:wq)
- Make executable and run (no output expected):
```bash
chmod +x setup.sh
./setup.sh
```
- Screenshots:
  - vim editor showing shebang: `task13_b1_vim.png`
  - run output: `task13_b1_run.png`

2. Add the while-loop summation code
- Re-open setup.sh in vim and append the while-loop that:
  - initializes sum=0
  - prompts the user with "Enter a number (or 'q' to quit): "
  - if the user enters q, the loop breaks and final sum is shown
  - if the user enters a valid integer it is added to sum and the running sum is displayed
  - otherwise show "Invalid number, try again."
- Append the following code:
```bash
# While-loop summation (interactive)
sum=0
while true; do
  read -p "Enter a number (or 'q' to quit): " input
  if [ "$input" = "q" ]; then
    break
  fi
  if [[ "$input" =~ ^-?[0-9]+$ ]]; then
    sum=$((sum + input))
    echo "Running sum: $sum"
  else
    echo "Invalid number, try again."
  fi
done
echo "Final sum: $sum"
```
- Save and quit (:wq)
- Run the script and demonstrate a short session (example): enter `5`, then `7`, then `q`
```bash
./setup.sh
# interactively enter:
# 5
# 7
# q
```
- Screenshots:
  - vim editor showing while-loop appended: `task13_b2_vim.png`
  - run output showing the interactive session and final sum: `task13_b2_run.png`

3. Add the sum function that reads two numbers and returns their sum via echo
- Re-open setup.sh in vim and append the function and a demonstration call:
```bash
# Function to sum two numbers and return via echo
sum_two() {
  read -p "Enter first number: " a
  read -p "Enter second number: " b
  if ! [[ "$a" =~ ^-?[0-9]+$ ]] || ! [[ "$b" =~ ^-?[0-9]+$ ]]; then
    echo "Invalid input. Returning 0"
    echo 0
    return
  fi
  echo $((a + b))
}

# Demonstrate the function
echo "Now calling sum_two function:"
result=$(sum_two)
echo "Sum from function: $result"
```
- Save and quit (:wq)
- Run the script and demonstrate the function (example interactive inputs: `3` and `4`):
```bash
./setup.sh
# after the while-loop demonstration, when prompted by sum_two enter:
# 3
# 4
```
- Screenshots:
  - vim editor showing function appended: `task13_b3_vim.png`
  - run output showing the function prompts and returned sum: `task13_b3_run.png`

Important notes for Task 13:
- Students must overwrite previous contents of setup.sh at the start of Task 13 (step 1).
- Each step is incremental — add the code for that step only, save, run, and capture both the vim buffer screenshot and the run output screenshot.
- The while-loop and function are interactive; students should record a short demonstration session for the run screenshots (show both inputs and outputs).
- If students want non-interactive verification, they can temporarily modify the script to accept test inputs from heredoc or environment variables — but the required submission is interactive screenshots as described.

Screenshots summary for Task 13:
- `task13_b1_vim.png`
- `task13_b1_run.png`
- `task13_b2_vim.png`
- `task13_b2_run.png`
- `task13_b3_vim.png`
- `task13_b3_run.png`

---

## Submission

Create a repository `CC_<YourName>_<YourRollNumber>/Lab6` with:

```
Lab6/
  workspace/                    # any files you created (optional)
  screenshots/                  # include all screenshots listed in this lab
  Word file and PDF or .md file # your compiled answers/evidence
  Lab6-instructions.md          # this lab file
```

Upload:
- A Word file and a PDF or a .md file containing:
  - Terminal outputs or screenshots for each task
  - Brief explanations where asked

---

## Checklist (for students)

- [ ] Task 1: `su -` to root and `exit` back; screenshots captured  
- [ ] Task 2: Created `tom`; verified in `/etc/passwd`, `/etc/group`, `/etc/shadow`  
- [ ] Task 3: Created `developer`, `devops`, `designer`; changed `tom`’s primary/secondary groups; verified  
- [ ] Task 4: Created users (`Jerry` via adduser, `Scooby` via useradd without -m), showed `su - Scooby` fails before setting password, set password, showed missing home, created home and perms, verified login; created/deleted groups; verified all  
- [ ] Task 5: Created `Student`; created files; changed owner (then group) for file1; identified file, dir, and `/dev/null` types; exited at end  
- [ ] Task 6: Changed permissions as Student using symbolic mode; verified after each change  
- [ ] Task 7: Used `u= g= o=` forms as Student; verified results  
- [ ] Task 8: Used numeric modes (`777`, `700`, `744`, `640`, `664`, `775`, `750`) as Student (no sudo); took a screenshot after each `ls -l`  
- [ ] Task 9: Used pipes, pagers, grep, redirects `>` and `>>` with `/var/log/syslog`  
- [ ] Task 10: Wrote `setup.sh` in vim and ran it — captured screenshots for the script content and the outputs for each numbered step (var1, allFiles, dir check, file check, permissions)  
- [ ] Task 11: Wrote `setup.sh` to compare numeric argument and check second-argument string incrementally; captured all required screenshots (`task11_b0_*` … `task11_b9_*`)  
- [ ] Task 12: Cleared previous code, wrote a script printing arguments using $* in a for loop; captured screenshots (`task12_b1_vim.png`, `task12_b1_run.png`, `task12_b2_vim.png`, `task12_b2_run.png`)  
- [ ] Task 13: Cleared previous code, wrote interactive while-loop summation and a sum function; captured screenshots (`task13_b1_*`, `task13_b2_*`, `task13_b3_*`)  
- [ ] Pushed repository `CC_<YourName>_<YourRollNumber>` with `Lab6` contents

---

## Troubleshooting & Notes

- Root account (`su -`) on Ubuntu:
  - If `su -` fails with authentication, set a root password with `sudo passwd root`.
  - You can later disable root login by locking the root account: `sudo passwd -l root`.

- Viewing `/etc/shadow`:
  - Requires `sudo`. Never expect plaintext passwords; only hashes and metadata are stored there.

- User and group naming:
  - This lab uses lowercase for groups (`developer`, `devops`, `designer`, `jolly`, `anime`) to match common conventions. Linux is case-sensitive.

- File ownership and permissions:
  - You need to be file owner or root to change permissions/ownership. Use `sudo` where necessary.

- `/var/log/syslog`:
  - If missing, your system may rely on `journalctl`. You can adapt Task 9 using:
    - `sudo journalctl | less`
    - `sudo journalctl -u systemd | grep -i error > ~/journal_errors.txt`

Good luck — follow the exact screenshot filenames above when saving evidence for submission.
