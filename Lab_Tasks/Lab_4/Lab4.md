# 🧪 Lab 4 – Virtualization & Linux Fundamentals (using existing Ubuntu Server VM)

**Estimated Duration:** 3 hours  
**Instructions:** Complete all practical tasks in a repository named `Lab4`. When finished, push your work to a repository named `CC_<student_Name>_<student_roll_number>`.

---

## 🎯 Objective
This lab guides hands‑on virtualization and core Linux fundamentals. You will work with the Ubuntu Server VM already installed in VMware Workstation (no new OS install required), inspect virtualization settings, explore the Linux filesystem, perform essential command-line tasks, collect system information, and manage users.

By the end of this lab you will be able to:
- Inspect a VM's resources and networking settings in VMware Workstation  
- Explore the Linux filesystem hierarchy and hidden (dot) files  
- Use basic Linux CLI commands to navigate and manipulate files and directories  
- Read system information (kernel, CPU, memory, disk) and list processes  
- Create a non‑root user and verify its account existence  
- Create and run a small shell script (Bonus)

---

## 🧩 Prerequisites
- You already have an Ubuntu Server VM installed in VMware Workstation from Lab 1 — use that VM for all tasks in this lab.  
- Host machine: Windows (preferred for Task 2) / macOS / Linux with VMware Workstation available.  
- SSH access to the VM from your host (Command Prompt / PowerShell / Terminal) — you will use the host's terminal to connect.  
- A text editor inside VM (nano, vim, or other).  
- Note: This course has not covered pipes (`|`) or redirection (`>`, `>>`), and you should not use them for this lab.

---
**Navigation:** Click any task below to jump to its instructions.

## 📋 Task List

- [Task 1: Verify VM resources in VMware](#task-1--verify-vm-resources-in-vmware)  
- [Task 2: Start VM and log in (use your preferred host terminal method only)](#task-2--start-vm-and-log-in-use-your-preferred-host-terminal-method-only)  
- [Task 3: Filesystem exploration — root tree and dotfiles](#task-3--filesystem-exploration--root-tree-and-dotfiles)  
- [Task 4: Essential CLI tasks — navigation and file operations](#task-4--essential-cli-tasks--navigation-and-file-operations)    
- [Task 5: System info, resources & processes](#task-5--system-info-resources--processes)  
- [Task 6: Users and account verification (no sudo group change)](#task-6--users-and-account-verification-no-sudo-group-change)  
- [Bonus Task 7: Create a small demo script using an editor and run it (optional)](#bonus-task-7--create-a-small-demo-script-using-an-editor-and-run-it-optional)  
- [Exam Evaluation Questions](#exam-evaluation-questions)  
- [Summary](#summary)  
- [Submission](#submission)

---

## Task 1 – Verify VM resources in VMware

Confirm the VM resources that were allocated in Lab 1.

### Steps
1. Open VMware Workstation and locate the Ubuntu Server VM you used in Lab 1.  
2. Inspect VM settings and note the following (no commands required for GUI): VM name, RAM, CPU, disk, and network adapter type.  
3. Take a screenshot of the VM settings window showing RAM, CPU, disk and networking. Save screenshot as: `vm_settings.png`

📸 Screenshot Required:
- `vm_settings.png`

---

## Task 2 – Start VM and log in (use your preferred host terminal method only)

Use a single preferred host-terminal method to connect to the VM. Do not switch between methods during the task.

### Steps
1. Start (or resume) the VM in VMware Workstation on your host.  
2. From your host, open your preferred terminal (for example: Windows Command Prompt, PowerShell, macOS Terminal, or Linux Terminal) and connect to the VM using SSH. Example:

```bash
ssh student@<vm-ip-address>
```

- After connecting, save a screenshot of your host terminal showing the SSH login prompt/results as: `vm_login.png`

3. After logging in, run both commands and capture them together in a single screenshot:

```bash
whoami
pwd
```

- Save a single screenshot that clearly shows both outputs as: `whoami_pwd.png`

📸 Screenshot Required:
- `vm_login.png` (optional if `whoami_pwd.png` provided)  
- `whoami_pwd.png` (required; must show both `whoami` and `pwd` outputs)

---

## Task 3 – Filesystem exploration — root tree and dotfiles

Explore Linux filesystem layout and hidden files. Capture outputs using screenshots only (do not create text files). For the short explanation required in this task, write the paragraph in an editor and capture it as a screenshot (`answers_md.png`) — do not supply the `.md` file.

### Steps (run inside VM terminal)
1. List root directory contents:

```bash
ls -la /
```

- Save screenshot as: `ls_root.png`

2. View OS release information:

```bash
cat /etc/os-release
```

- Save screenshot as: `os_release.png`

3. Inspect these directories (run each command and screenshot the output):

```bash
ls -la /bin
```
- Save screenshot as `ls_bin.png`.

```bash
ls -la /sbin
```
- Save screenshot as `ls_sbin.png`.

```bash
ls -la /usr
```
- Save screenshot as `ls_usr.png`.

```bash
ls -la /opt
```
- Save screenshot as `ls_opt.png`.

```bash
ls -la /etc
```
- Save screenshot as `ls_etc.png`.

```bash
ls -la /dev
```
- Save screenshot as `ls_dev.png`.

```bash
ls -la /var
```
- Save screenshot as `ls_var.png`.

```bash
ls -la /tmp
```
- Save screenshot as `ls_tmp.png`.

4. List your home directory and show hidden (dot) files:

```bash
ls -la ~
```

- Save screenshot as: `home_ls.png`

5. Write a short paragraph (3–5 sentences) that explains the difference between `/bin`, `/usr/bin` and `/usr/local/bin`. Open your editor:

```bash
nano ~/answers.md
```

- Type the paragraph in the editor, save and exit.  
- After saving, open the editor display (or show the file) and capture a screenshot of the paragraph. Save that screenshot as: `answers_md.png`

> Reminder: Do not upload the `.md` file—submit the screenshot `answers_md.png` as proof of your written answer.

📸 Screenshot Required:
- `ls_root.png`  
- `os_release.png`  
- `ls_bin.png`  
- `ls_sbin.png`  
- `ls_usr.png`  
- `ls_opt.png`  
- `ls_etc.png`  
- `ls_dev.png`  
- `ls_var.png`  
- `ls_tmp.png`  
- `home_ls.png`  
- `answers_md.png`

---

## Task 4 – Essential CLI tasks — navigation and file operations

Practice basic commands used daily on Linux systems. Use screenshots only for evidence.

### Steps (inside VM terminal)
1. Create a workspace and navigate:

```bash
mkdir -p ~/lab4/workspace/python_project
```
- Save screenshot as `mkdir_workspace.png`.

```bash
cd ~/lab4/workspace/python_project
```
- Save screenshot as `cd_workspace.png`.

```bash
pwd
```
- Save screenshot as `pwd_workspace.png`.

2. Create files using an editor (open each editor session and save a screenshot showing content):

```bash
nano README.md
```
- Inside nano add: `Lab 4 README` and save.  
- Save screenshot of the editor after saving as `nano_readme.png`.

```bash
nano main.py
```
- Inside nano add: `print("hello lab4")` and save.  
- Save screenshot as `nano_main.png`.

```bash
nano .env
```
- Inside nano add: `ENV=lab4` and save.  
- Save screenshot as `nano_env.png`.

3. List files and capture:

```bash
ls -la
```
- Save screenshot as `workspace_ls.png`.

4. Copy, move and remove:

```bash
cp README.md README.copy.md
```
- After running, save screenshot as `cp_readme.png`.

```bash
mv README.copy.md README.dev.md
```
- After running, save screenshot as `mv_readme.png`.

```bash
rm README.dev.md
```
- After running, save screenshot as `rm_readme.png`.

```bash
mkdir -p ~/lab4/workspace/java_app
```
- Save screenshot as `mkdir_java_app.png`.

```bash
cp -r ~/lab4/workspace/python_project ~/lab4/workspace/java_app_copy
```
- After running, save screenshot as `cp_recursive.png`.

```bash
ls -la ~/lab4/workspace
```
- Save screenshot as `copy_verify.png`.

5. Use command history and tab completion:

```bash
history
```
- Save screenshot as `history.png`.

- Demonstrate tab completion (type partial name and press Tab) and capture that action as `tab_completion.png`.

📸 Screenshot Required:
- `mkdir_workspace.png`  
- `cd_workspace.png`  
- `pwd_workspace.png`  
- `nano_readme.png`  
- `nano_main.png`  
- `nano_env.png`  
- `workspace_ls.png`  
- `cp_readme.png`  
- `mv_readme.png`  
- `rm_readme.png`  
- `mkdir_java_app.png`  
- `cp_recursive.png`  
- `copy_verify.png`  
- `history.png`  
- `tab_completion.png`

---

## Task 5 – System info, resources & processes

Collect system information and observe processes. Use screenshots only.

### Steps (inside VM terminal)
1. Kernel and OS:

```bash
uname -a
```
- Save screenshot as `uname.png`.

2. CPU (ensure `model name` visible):

```bash
cat /proc/cpuinfo
```
- Save screenshot as `cpuinfo.png`.

3. Memory:

```bash
free -h
```
- Save screenshot as `meminfo.png`.

4. Disk:

```bash
df -h
```
- Save screenshot as `diskinfo.png`.

5. Block devices:

```bash
lsblk
```
- Save screenshot as `lsblk.png`.

6. Processes (show top lines of ps output):

```bash
ps aux
```
- Save screenshot as `processes.png`.

📸 Screenshot Required:
- `uname.png`  
- `cpuinfo.png`  
- `meminfo.png`  
- `diskinfo.png`  
- `lsblk.png`  
- `processes.png`

---

## Task 6 – Users and account verification (no sudo group change)

Create a non‑root user and verify the account exists. This task does NOT add the created user to the sudo group.

### Steps (inside VM terminal)
1. Create a new user named `lab4user`:

```bash
sudo adduser lab4user
```
- During prompts, capture the terminal and save screenshot as `adduser_lab4user.png`.

2. Verify the user entry:

```bash
getent passwd lab4user
```
- Save screenshot as `lab4user_passwd.png`.

3. Switch to the new user to verify login:

```bash
su - lab4user
```
- Save screenshot as `su_lab4user.png`.

4. (Optional) From the new user you may attempt a sudo command to show that sudo is not available for this account (expected failure), e.g.:

```bash
sudo whoami
```
- If attempted, capture the terminal output (expected: "lab4user is not in the sudoers file" or similar) and save screenshot as `sudo_whoami.png`.

5. Return to the original user:

```bash
exit
```
- Save screenshot as `exit_back.png`.

6. (Optional) Remove the test user when finished:

```bash
sudo deluser --remove-home lab4user
```
- If run, save screenshot as `deluser.png`.

📸 Screenshot Required:
- `adduser_lab4user.png`  
- `lab4user_passwd.png`  
- `su_lab4user.png`  
- `sudo_whoami.png` (optional — only if you attempted the sudo command and want to show the expected failure)  
- `exit_back.png`  
- (optional) `deluser.png`

---

## Bonus Task 7 – Create a small demo script using an editor and run it (optional)

This task is optional — complete it for extra practice or extra credit. It is not required for passing the core lab tasks.

### Steps (inside VM)
1. Open an editor to create the script:

```bash
nano ~/lab4/workspace/run-demo.sh
```
- Type the following lines into the editor (manually or paste), save and exit:

```
#!/bin/bash
echo "Lab 4 demo: current user is $(whoami)"
echo "Current time: $(date)"
uptime
free -h
```

- Save screenshot of the editor after saving the file as `nano_run_demo.png`.

2. Make the script executable:

```bash
chmod +x ~/lab4/workspace/run-demo.sh
```
- Save screenshot as `chmod_run_demo.png`.

3. Run the script as your regular user:

```bash
~/lab4/workspace/run-demo.sh
```
- Save screenshot of the script output as `run_demo_output.png`.

4. Optionally run it with sudo:

```bash
sudo ~/lab4/workspace/run-demo.sh
```
- Save screenshot as `run_demo_output_sudo.png`.

📸 Screenshot (Optional / Bonus) — Recommended if you complete this task:
- `nano_run_demo.png`  
- `chmod_run_demo.png`  
- `run_demo_output.png`  
- `run_demo_output_sudo.png` (optional)

---

## Exam Evaluation Questions

Answer these short tasks and include screenshots as proof. For written answers, use your editor and capture them as `answers_md.png` (a screenshot). Do NOT submit a `.txt` or `.md` file — only screenshots are accepted as proof.

### Q1. VM verification
- Deliverable screenshot: `vm_settings.png`.  
- In your editor write one sentence explaining why you chose the VM's network mode (NAT / Bridged / Host-only), capture it as `answers_md.png`.

### Q2. Filesystem knowledge
- Deliverable screenshots: `ls_root.png`, `ls_etc.png`, `home_ls.png`.  
- In your editor write a one-line answer: where are system configuration files stored? Capture it as `answers_md.png` (same screenshot may include multiple answers).

### Q3. CLI basics
- Deliverable screenshots: `workspace_ls.png`, `history.png`.  
- In your editor write one-line answer: how do you recursively copy a directory on Linux? Capture it as `answers_md.png`.

### Q4. System info
- Deliverable screenshots: `cpuinfo.png`, `meminfo.png`.  
- In your editor write one-line answer: how much memory does your VM have? (report the number shown by `free -h`) and capture as `answers_md.png`.

### Q5. Users & privileges
- Deliverable screenshots: `lab4user_passwd.png`, `su_lab4user.png`.  
- If you attempted the sudo test, include `sudo_whoami.png` showing the expected failure (optional).  
- In your editor write one-line answer: why should you avoid running commands as root unless necessary? Capture it as `answers_md.png`.

📸 Screenshot Required (Summary of all required screenshots)
- `vm_settings.png`  
- `vm_login.png` (optional)  
- `whoami_pwd.png`  
- `ls_root.png`  
- `os_release.png`  
- `ls_bin.png`  
- `ls_sbin.png`  
- `ls_usr.png`  
- `ls_opt.png`  
- `ls_etc.png`  
- `ls_dev.png`  
- `ls_var.png`  
- `ls_tmp.png`  
- `home_ls.png`  
- `answers_md.png`  
- `mkdir_workspace.png`  
- `cd_workspace.png`  
- `pwd_workspace.png`  
- `nano_readme.png`  
- `nano_main.png`  
- `nano_env.png`  
- `workspace_ls.png`  
- `cp_readme.png`  
- `mv_readme.png`  
- `rm_readme.png`  
- `mkdir_java_app.png`  
- `cp_recursive.png`  
- `copy_verify.png`  
- `history.png`  
- `tab_completion.png`  
- `uname.png`  
- `cpuinfo.png`  
- `meminfo.png`  
- `diskinfo.png`  
- `lsblk.png`  
- `processes.png`  
- `adduser_lab4user.png`  
- `lab4user_passwd.png`  
- `su_lab4user.png`  
- `sudo_whoami.png` (optional)  
- `exit_back.png`  
- (Optional / Bonus) `nano_run_demo.png`  
- (Optional / Bonus) `chmod_run_demo.png`  
- (Optional / Bonus) `run_demo_output.png`  
- (Optional / Bonus) `run_demo_output_sudo.png`  
- (Optional cleanup) `deluser.png`

---

## Summary

In this lab you:
- Verified VM configuration in VMware Workstation.  
- Logged into the existing Ubuntu Server VM using the Windows host terminal and explored the filesystem.  
- Practiced core Linux CLI commands for navigation and file manipulation (evidence via screenshots).  
- Collected system and process information and created a non‑root user account without granting sudo.  
- (Optional) Wrote and executed a simple demo script using an editor.

---

## Submission

- Create a ZIP or push a Git repository named `CC_<student_Name>_<student_roll_number>` containing:
  - `Lab4.md` (this file)  
  - `vm_plan.md` — one paragraph describing VM resources and networking choice (use Markdown file, not .txt)  
  - `workspace/` — folder containing files you created in the workspace:
    - `README.md`, `main.py`, `.env` (required)
    - `run-demo.sh` (optional / bonus)
  - `screenshots/` — folder containing all required screenshots listed above (preferred), including `answers_md.png`  
  - Optional: export notes or snapshot if your instructor requests it

- ZIP the repository (or push to GitHub) and submit link or ZIP file per instructor guidelines. Include PDF or Markdown versions of your answers if required by instructor.

---

## Checklist (for students)
- [ ] Verified VM settings (`vm_settings.png`)  
- [ ] Logged into the VM from host and captured `vm_login.png` or `whoami_pwd.png`  
- [ ] Collected filesystem and OS screenshots (`ls_root.png`, `os_release.png`, `home_ls.png`, etc.)  
- [ ] Completed CLI tasks and captured `workspace_ls.png`, `history.png`  
- [ ] Collected system info screenshots (`cpuinfo.png`, `meminfo.png`, `diskinfo.png`, `lsblk.png`, `uname.png`)  
- [ ] Created `lab4user` and verified account (`lab4user_passwd.png`, `su_lab4user.png`)  
- [ ] Captured written answers as `answers_md.png`  
- [ ] (Optional) Created and ran `run-demo.sh` and captured output (`run_demo_output.png`)  
- [ ] Packaged and pushed repository `CC_<student_Name>_<student_roll_number>`

---

**Notes & Tips**
- Use screenshots to capture command outputs and written answers since we are not using redirection or pipes in this course.  
- Save your short written answers in an editor (e.g., nano) and capture them as `answers_md.png`. Do not submit the `.md` file—submit the screenshot only.  
- If you need to copy terminal output into a file, open an editor (nano/vim), paste the text, and save the file. Do not use `>` redirection for this course.  
- Take snapshots before risky changes in VMware Workstation.

---

## Submission (format)
- ZIP the repository (or push to GitHub) and submit link or ZIP file per instructor guidelines. Include both PDF and Markdown or a Word export of your answers if required.

Good luck — when you finish, provide your repository link here and I will run a quick checklist to verify required files exist.
