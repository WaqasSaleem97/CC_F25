# 🧪 Lab 7 – Environment Variables, PATH, UFW, and SSH Key Authentication

Estimated Duration: 3 hours  
Instructions: Complete all tasks on the SAME Ubuntu Server VM used in Lab 6. Create a repository named `Lab7`. When finished, push your work to a repository named `CC_<student_Name>_<student_roll_number>/Lab7`.

---

## 🎯 Objective

In this lab you will practice:

- Viewing and filtering environment variables
- Defining temporary and persistent environment variables
- Understanding differences between shell-only exports, ~/.bashrc persistence, and /etc/environment (system-wide)
- Updating PATH to run scripts without a `./` prefix
- Using UFW to block and allow SSH (port 22)
- Setting up SSH public-key authentication (authorized_keys) so logins do not require a password

---

## 🧩 Prerequisites

- The same Ubuntu Server VM used for Lab 6
- A sudo-capable user on the VM
- SSH access from your host (Windows) machine
- Basic familiarity with terminal usage

Notes:
- Editing `/etc/environment` requires sudo and affects all users — use caution.
- `~/.bashrc` affects interactive non-login shells; `source ~/.bashrc` reloads it in the current session.

---

## 📋 Task List (Lab 7)

- [Task 1: Print & filter environment variables](#task-1--print--filter-environment-variables)
- [Task 2: Export DB_* variables temporarily and observe scope](#task-2--export-db_-variables-temporarily-and-observe-scope)
- [Task 3: Make DB_* variables persistent in ~/.bashrc](#task-3--make-db_-variables-persistent-in-bashrc)
- [Task 4: System-wide environment variable, welcome script, and PATH](#task-4--system-wide-environment-variable-welcome-script-and-path)
- [Task 5: Block and allow SSH using ufw (firewall)](#task-5--block-and-allow-ssh-using-ufw-firewall)
- [Task 6: Configure SSH key-based login from Windows host](#task-6--configure-ssh-key-based-login-from-windows-host)
- [Exam Evaluation Questions](#exam-evaluation-questions)
- [Submission](#submission)
- [Checklist](#checklist)
- [Troubleshooting & Notes](#troubleshooting--notes)

---

Important screenshot convention requested by instructor:
- All "print" commands that use grep (e.g., `printenv | grep ...`, `printenv | grep '^DB_'`, etc.) must be captured together in a single screenshot per logical step (not one screenshot per grep).
- All consecutive variable-definition commands (e.g., multiple `export` statements executed one after another) must be captured in a single screenshot showing those definitions together.
- Other commands and edits should be captured as previously specified (one screenshot per logically distinct action/edit/run).

---

## Task 1 — Print & filter environment variables

Goal: Show environment variables and filter them using grep.

Commands and required screenshots (grouped as requested):

1) Print all environment variables:
```bash
printenv
```
- Save screenshot as: `task1_printenv_all.png`

2) Filter for SHELL, HOME and USER — run these greps together and capture one combined screenshot:
```bash
printenv | grep SHELL
printenv | grep HOME
printenv | grep USER
```
- Save screenshot as: `task1_grep_shell_home_user.png`  (single screenshot showing all three grep outputs together)

📸 Screenshots:
- `task1_printenv_all.png`
- `task1_grep_shell_home_user.png`

---

## Task 2 — Export DB_* variables temporarily and observe scope

Goal: Create env variables with export in the current shell, verify them, then close shell and show variables are gone.

Per the requested grouping rule: capture all the variable-definition (export) commands in a single screenshot; capture the echo/print checks grouped logically.

Steps and required screenshots:

1) Define all DB_* variables (run the three exports one after another). Capture them in one screenshot showing the three export commands and their execution:
```bash
export DB_URL="postgres://db.example.local:5432/mydb"
export DB_USER="labuser"
export DB_PASSWORD="labpass123"
```
- Save screenshot as: `task2_exports_all.png`  (single screenshot showing all three export commands shown/executed)

2) Echo the three variables (run the three echo commands together) and capture one screenshot showing their outputs:
```bash
echo "$DB_URL"
echo "$DB_USER"
echo "$DB_PASSWORD"
```
- Save screenshot as: `task2_echoes_all.png`

3) Show all DB_ variables with a single grep command (capture that output):
```bash
printenv | grep '^DB_'
```
- Save screenshot as: `task2_printenv_grep_db.png`

4) Close the bash session (e.g., `exit`) and reopen a new terminal. Verify the variables are gone by running the echo(s) and the grep together; capture both checks in one screenshot:
```bash
echo "$DB_URL"
printenv | grep '^DB_'
```
- Save screenshot as: `task2_after_restart_checks.png`  (single screenshot showing echo (empty) and `printenv | grep '^DB_'` with no results)

Optional combined evidence (if you want to also save a combined screenshot showing steps 1–3 together for convenience):
- `task2_export_echo_printenv_combined.png`

📸 Screenshots (required):
- `task2_exports_all.png`
- `task2_echoes_all.png`
- `task2_printenv_grep_db.png`
- `task2_after_restart_checks.png`
- (Optional) `task2_export_echo_printenv_combined.png`

Notes:
- The single-screenshot-per-group rule helps graders quickly see related commands and their immediate outputs together.

---

## Task 3 — Make DB_* variables persistent in ~/.bashrc

Goal: Add DB_* variables to `~/.bashrc`, reload, and verify persistence. Grouped captures: show the three export lines in ~/.bashrc together, and group the post-source checks into one screenshot.

Steps and required screenshots:

1) Open `~/.bashrc` in an editor and append the three export lines. Capture the editor showing the three lines added (single screenshot):
```bash
vim ~/.bashrc
# add at the end:
# Lab 7 persistent DB variables
export DB_URL="postgres://db.example.local:5432/mydb"
export DB_USER="labuser"
export DB_PASSWORD="labpass123"
```
- Save screenshot as: `task3_bashrc_added.png`  (single screenshot showing the three export lines in the editor)

2) Source `~/.bashrc` and capture the source command in one screenshot together with the next verification commands (grouped): run `source ~/.bashrc` and then immediately run the three echoes and a single grep, capturing all of these in one screenshot:
```bash
source ~/.bashrc
echo "$DB_URL"
echo "$DB_USER"
echo "$DB_PASSWORD"
printenv | grep '^DB_'
```
- Save screenshot as: `task3_source_and_verification.png` (single screenshot showing source, the three echoes, and the grep output)

3) Close and reopen terminal. Verify persistence by running one echo and the grep together — capture both in one screenshot:
```bash
echo "$DB_URL"
printenv | grep '^DB_'
```
- Save screenshot as: `task3_after_restart_persistent.png` (single screenshot showing echo with value and grep output listing DB_ variables)

📸 Screenshots (required):
- `task3_bashrc_added.png`
- `task3_source_and_verification.png`
- `task3_after_restart_persistent.png`

Notes:
- The instructor requires the editor view showing the three export lines (step 1) and grouped verification screenshots (steps 2–3).

---

## Task 4 — System-wide environment variable, welcome script, and PATH

Goal: Add Class variable to `/etc/environment`, view PATH, create a `welcome` script at `~/welcome`, make it executable, and add PATH entry in ~/.bashrc so `welcome` can be executed without `./`. Capture grouped screenshots as applicable.

Steps and required screenshots (grouping applies to "print with grep" type commands and grouped variable definitions — in this task there is a single system variable definition so a standard per-action capture is used):

1) View `/etc/environment`:
```bash
sudo cat /etc/environment
```
- Save screenshot as: `task4_etc_environment_before.png`

2) Show current PATH:
```bash
echo "$PATH"
```
- Save screenshot as: `task4_echo_path_before.png`

3) Edit `/etc/environment` and add Class:
```bash
sudo vim /etc/environment
# add line: Class="CC-<your_class_name>"
```
- Save screenshot as: `task4_etc_environment_edit_vim.png`  (editor with edit)
- Save screenshot as: `task4_etc_environment_after.png`     (cat or editor view showing the new Class line)

4) Re-login or open a new shell and show Class and PATH together (grouped prints): run `echo $Class` and `echo $PATH` together and capture in a single screenshot:
```bash
echo $Class
echo "$PATH"
```
- Save screenshot as: `task4_echo_class_and_path.png`  (single screenshot showing both outputs)

5) Create `welcome` script at your home directory (`~/welcome`) and make it executable (capture the heredoc creation and chmod together in one screenshot if possible):
```bash
cat > ~/welcome <<'EOF'
#!/bin/bash
echo "Welcome to Cloud Computing $USER"
EOF

chmod +x ~/welcome
```
- Save screenshot as: `task4_welcome_create_and_chmod.png` (single screenshot showing heredoc creation command and chmod output/listing)

6) Run the script from your home directory using `./welcome`:
```bash
cd ~
./welcome
```
- Save screenshot as: `task4_welcome_run_dot.png`

7) Add your home directory to PATH in `~/.bashrc`. NOTE: per your instruction we do not include an `export PATH` line here — only add the PATH modification line in the file. Capture the editor showing that PATH line in one screenshot:
```bash
vim ~/.bashrc
# add at end:
PATH=$PATH:~
```
- Save screenshot as: `task4_bashrc_path_line.png` (editor screenshot showing the PATH line only)

8) Apply the change and run `welcome` — capture these runtime commands in a separate screenshot (must be taken separately from the editor screenshot):
```bash
source ~/.bashrc
cd ~
welcome
```
- Save screenshot as: `task4_bashrc_source_and_welcome.png` (single screenshot showing the `source` command and the `welcome` output)

📸 Screenshots (required):
- `task4_etc_environment_before.png`
- `task4_echo_path_before.png`
- `task4_etc_environment_edit_vim.png`
- `task4_etc_environment_after.png`
- `task4_echo_class_and_path.png`
- `task4_welcome_create_and_chmod.png`
- `task4_welcome_run_dot.png`
- `task4_bashrc_path_line.png`
- `task4_bashrc_source_and_welcome.png`

Notes:
- If you prefer `~/bin`, substitute `~/welcome` accordingly and capture the same grouped screenshots.

---

## Task 5 — Block and allow SSH using ufw (firewall)

Goal: Use ufw to deny and allow SSH then verify SSH connectivity changes from host. Save screenshots after each logical command/step; group related print checks when appropriate.

Steps and required screenshots:

1) Enable ufw and show status (group both commands in one screenshot if you run them together):
```bash
sudo ufw enable
sudo ufw status verbose
```
- Save screenshot as: `task5_ufw_enable_and_status.png`

2) Deny TCP port 22 and show status (run deny and `status numbered` together and capture in one screenshot). Use short form as requested:
```bash
sudo ufw deny 22/tcp
sudo ufw status numbered
```
- Save screenshot as: `task5_ufw_deny_22_and_status.png`

3) From Windows host attempt to SSH (expected to fail) — capture the host-side SSH attempt in one screenshot:
```powershell
ssh username@<server_ip>
```
- Save screenshot as: `task5_ssh_attempt_blocked.png`

4) Allow SSH back and reload, then show status (group allow, reload, status in one screenshot if run together). Use short form as requested:
```bash
sudo ufw allow 22/tcp
sudo ufw reload
sudo ufw status
```
- Save screenshot as: `task5_ufw_allow_reload_status.png`

5) From Windows host attempt SSH again (should succeed) — capture successful login in one screenshot:
```powershell
ssh username@<server_ip>
```
- Save screenshot as: `task5_ssh_success_after_allow.png`

📸 Screenshots (required):
- `task5_ufw_enable_and_status.png`
- `task5_ufw_deny_22_and_status.png`
- `task5_ssh_attempt_blocked.png`
- `task5_ufw_allow_reload_status.png`
- `task5_ssh_success_after_allow.png`

Notes:
- When running multiple ufw commands consecutively, grouping them in one screenshot shows the command sequence and resulting status together.

---

## Task 6 — Configure SSH key-based login from Windows host

Goal: Copy your public key from the Windows host into the Ubuntu server's `~/.ssh/authorized_keys` to allow passwordless SSH. Save grouped screenshots for the client-side actions and the server-side edits/checks.

A. On Windows host (client) — group related client actions:

1) Generate ed25519 key pair (if needed) and show the generated files in one screenshot (run `ssh-keygen` and then list `~/.ssh`):
```powershell
ssh-keygen -t ed25519 -f ~/.ssh/id_lab7 -C "lab_key"
ls -la ~/.ssh
```
- Save screenshot as: `task6_windows_sshkey_and_list.png` (single screenshot showing keygen result and `ls` of .ssh folder)

2) Show the public key content (single screenshot):
```powershell
type $env:USERPROFILE\.ssh\id_lab7.pub
# or on Git Bash: cat ~/.ssh/id_lab7.pub
```
- Save screenshot as: `task6_windows_public_key.png`

3) Clear the known_hosts file content and verify it is empty (single screenshot):
```powershell
# Clear contents (PowerShell)
Clear-Content $env:USERPROFILE\.ssh\known_hosts

# View the file (should be empty)
type $env:USERPROFILE\.ssh\known_hosts
```
- Save screenshot as: `task6_windows_known_hosts_cleared_and_empty.png`

4) Connect to the Ubuntu server using the standard SSH command (this will prompt to accept the server host key because known_hosts is empty). Capture the connection prompt/accept step in one screenshot:
```powershell
ssh username@<server_ip>
# Accept the host key prompt (yes) and complete the login (enter password or key passphrase)
```
- Save screenshot as: `task6_windows_ssh_accept_hostkey_and_login.png`

5) After the successful connection, view the known_hosts file to show the server host key was added (single screenshot):
```powershell
type $env:USERPROFILE\.ssh\known_hosts
```
- Save screenshot as: `task6_windows_known_hosts_after_connect.png`

B. On Ubuntu server — group related server-side commands:

1) Prepare the `~/.ssh` directory and clear `authorized_keys` (this will create the directory if missing, set the correct directory permissions, and truncate the authorized_keys file). Capture this command sequence and its output in one screenshot:
```bash
mkdir -p ~/.ssh
chmod 700 ~/.ssh
> ~/.ssh/authorized_keys
```
- Save screenshot as: `task6_server_clear_authorized_keys.png`

2) Append the public key, set file permissions, and show the resulting `authorized_keys` (capture commands and resulting file content in one screenshot):
```bash
# paste public key name id_lab7.pub from Windows client into the echo below
echo "ssh-ed25519 AAAA... yourpublickey ... comment" >> ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
cat ~/.ssh/authorized_keys
```
- Save screenshot as: `task6_server_add_key_and_show.png` (single screenshot showing the commands and resulting authorized_keys content)

3) From Windows host test passwordless login (capture successful login in one screenshot):
```powershell
ssh username@<server_ip>
```
- Save screenshot as: `task6_ssh_passwordless_login.png`

4) Also demonstrate explicit identity usage (single screenshot):
```powershell
ssh -i ~/.ssh/id_lab7 username@<server_ip>
```
- Save screenshot as: `task6_ssh_with_identity_file.png`

Important notes:
- Do NOT show or upload private key files.
- Ensure server-side permissions are strict: `~/.ssh` 700, `authorized_keys` 600.

📸 Screenshots (required):
- `task6_windows_sshkey_and_list.png`
- `task6_windows_public_key.png`
- `task6_windows_known_hosts_cleared_and_empty.png`
- `task6_windows_ssh_accept_hostkey_and_login.png`
- `task6_windows_known_hosts_after_connect.png`
- `task6_server_clear_authorized_keys.png`
- `task6_server_add_key_and_show.png`
- `task6_ssh_passwordless_login.png`
- `task6_ssh_with_identity_file.png`

---

## Exam Evaluation Questions

Below are exam-style evaluation questions that test the same core concepts from Tasks 1–6 but present different scenarios and prompts so students must apply their understanding rather than repeat exact task steps. Do NOT provide solutions in your submission — students must perform the actions and upload the requested screenshots as evidence. Each sub-question lists the evidence filenames to be submitted.

Note: The prompts avoid repeating the exact lab task wording while keeping the concepts intact (environment inspection and filtering, temporary vs persistent variables, PATH/script execution, firewall rules for SSH/ping, and SSH key authentication).

### Q1 — Quick Environment Audit
- Objective: Demonstrate you can inspect the current environment and extract a few key variables.
- Actions & evidence:
  1. Run a single command to display environment variables and capture its output.
     - Save screenshot: `EE_q1_env_all.png`
  2. In the same terminal session, run three filters (one per line) to show values for PATH, LANG, and PWD, then capture a single screenshot showing the three outputs together.
     - Save screenshot: `EE_q1_env_filters.png`

### Q2 — Short-lived Student Info
- Objective: Show how temporary environment variables behave (session-scoped).
- Actions & evidence:
  1. In one terminal, set three variables (STUDENT_NAME, STUDENT_ROLL_NUMBER, STUDENT_SEMESTER) using export — execute all three consecutively and capture them in one screenshot (show the commands executed).
     - Save screenshot: `EE_q2_exports.png`
  2. Still in the same session, print the three values with echo (grouped) and capture the outputs in one screenshot.
     - Save screenshot: `EE_q2_echoes.png`
  3. Use a single printenv|grep command to list any STUDENT_ variables and capture the result.
     - Save screenshot: `EE_q2_printenv_grep.png`
  4. Exit that shell, open a fresh terminal, and show that the STUDENT_ variables are not set (use echo and printenv|grep together) — capture in one screenshot.
     - Save screenshot: `EE_q2_after_restart.png`

### Q3 — Make It Sticky (Persistence Check for Student Info)
- Objective: Demonstrate persistence of environment variables across sessions via shell configuration.
- Actions & evidence:
  1. Edit `~/.bashrc` (or your chosen interactive shell rc file) and append the three STUDENT_* exports. Capture a screenshot of the editor showing the new lines.
     - Save screenshot: `EE_q3_bashrc_editor.png`
  2. Reload your shell config with a single command and then verify the three variables and show `printenv | grep '^STUDENT_'` — capture these verification outputs together in one screenshot.
     - Save screenshot: `EE_q3_after_source.png`
  3. Close and re-open a terminal and demonstrate the STUDENT_NAME variable is available (echo and printenv grep together) — capture in one screenshot.
     - Save screenshot: `EE_q3_after_restart.png`

### Q4 — Firewall Rules: Block and Restore Ping (ICMP)
- Objective: Demonstrate you can block ping (ICMP echo) traffic using ufw and then re-allow it; show effect from a client.
- Actions & evidence:
  1. Enable ufw and capture the enable command and status together in one screenshot.
     - Save screenshot: `EE_q5_ufw_enable_status.png`
  2. Add a rule to block ping (ICMP echo) and show `ufw status numbered` in the same screenshot.
     - Suggested command example:
       ```bash
       sudo ufw deny proto icmp from any to any
       sudo ufw status numbered
       ```
     - Save screenshot: `EE_q5_ufw_deny_ping_status.png`
  3. From your Windows host (or another client), attempt to ping the server while the rule is active and capture the blocked/failing ping in one screenshot.
     - Save screenshot: `EE_q5_ping_blocked.png`
  4. Re-allow ping (ICMP) (or remove the deny rule) and capture the allow/reload/status sequence in one screenshot.
     - Suggested command example:
       ```bash
       sudo ufw allow proto icmp from any to any
       sudo ufw reload
       sudo ufw status
       ```
     - Save screenshot: `EE_q5_ufw_allow_ping_status.png`
  5. From the client, ping the server again and capture successful replies in one screenshot.
     - Save screenshot: `EE_q5_ping_success.png`

---


## Submission

Create a repository `CC_<YourName>_<YourRollNumber>/Lab7` with:

```
Lab7/
  workspace/                    # any files you created (optional)
  screenshots/                  # include all screenshots listed in this lab (optional)
  Exam_Evaluation/              # add evidence and README as above
  Lab7.md                       # this lab manual (the file you are preparing)
  Lab7_solution.docx            # this lab solution in MS word format
  Lab7_solution.pdf             # This lab solution in PDF format
```

Upload the screenshots with the exact filenames listed in the Exam Evaluation Question and the lab tasks.

---

## Checklist

- [ ] Task 1: printed environment variables and saved combined grep screenshot  
- [ ] Task 2: exported DB_* variables (single screenshot for exports), ran grouped echoes and grep (single screenshots), and verified after restart (single screenshot)  
- [ ] Task 3: appended exports to ~/.bashrc (editor screenshot), sourced and verified (grouped screenshot), verified after restart (grouped screenshot)  
- [ ] Task 4: edited /etc/environment, added Class, created ~/welcome script, updated PATH in ~/.bashrc (PATH line only, no export), and saved separate screenshots for the editor and for the source+run steps  
- [ ] Task 5: enabled ufw, denied and allowed SSH (using `sudo ufw deny 22/tcp` and `sudo ufw allow 22/tcp`), attempted SSH from host (blocked and allowed) and saved grouped screenshots for related command sequences  
- [ ] Task 6: generated/located Windows SSH public key using filename `id_lab7`, cleared known_hosts and demonstrated host-key acceptance, prepared server `~/.ssh` and cleared `authorized_keys`, pasted public key to server authorized_keys, and saved grouped screenshots for client/server steps and tests  
- [ ] Created and pushed `CC_<YourName>_<YourRollNumber>/Lab7` with Lab7.md and all screenshots  
- [ ] Complete Exam Evaluation Questions and save screenshots in `Exam_Evaluation/evidence/`  
- [ ] Push repository `CC_<YourName>_<YourRollNumber>/Lab7` with all files

---

## Troubleshooting & Notes

- If exported variables disappear after logout, that’s expected unless made persistent (`~/.bashrc` or `/etc/environment`).
- Use `source ~/.bashrc` to reload changes without logging out.
- If you accidentally break PATH, restore a safe PATH:
```bash
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
export PATH
```
- SSH keys: keep the private key private. Only the public key goes into `authorized_keys`.
- UFW: on remote VMs, keep a console access method to recover if you lock yourself out.

Good luck — follow the exact screenshot filenames above, and remember: group all grep/print outputs and group consecutive variable-definition commands into single screenshots as instructed.
