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

- [Task 1: Print & filter environment variables](#task-1---print--filter-environment-variables)
- [Task 2: Export DB_* variables temporarily and observe scope](#task-2---export-db_variables-temporarily-and-observe-scope)
- [Task 3: Make DB_* variables persistent in ~/.bashrc](#task-3---make-db_variables-persistent-in-bashrc)
- [Task 4: System-wide environment variable, welcome script, and PATH](#task-4---system-wide-environment-variable-welcome-script-and-path)
- [Task 5: Block and allow SSH using ufw (firewall)](#task-5---block-and-allow-ssh-using-ufw)
- [Task 6: Configure SSH key-based login from Windows host](#task-6---configure-ssh-key-based-login-from-windows-host)
- [Submission and Checklist](#submission-and-checklist)
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

Goal: Add Class variable to `/etc/environment`, view PATH, create `welcome` script on Desktop, make executable, and add PATH entry in ~/.bashrc. Capture grouped screenshots as applicable.

Steps and required screenshots (grouping applies to "print with grep" type commands and grouped variable definitions — in this task there is a single system variable definition so a standard per-action capture is used):

1) View `/etc/environment`:
```bash
sudo cat /etc/environment
```
- Save screenshot as: `task4_etc_environment_before.png`

2) Show current PATH (single print) — this is not a grep, but keep it as a dedicated screenshot:
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

5) Create `welcome` script on Desktop and make it executable (capture the heredoc creation and chmod together in one screenshot if possible):
```bash
cat > ~/Desktop/welcome <<'EOF'
#!/bin/bash
echo "Welcome to Cloud Computing $USER"
EOF

chmod +x ~/Desktop/welcome
```
- Save screenshot as: `task4_welcome_create_and_chmod.png` (single screenshot showing heredoc creation command and chmod output/listing)

6) Run the script from Desktop using `./welcome` (dedicated screenshot):
```bash
cd ~/Desktop
./welcome
```
- Save screenshot as: `task4_welcome_run_dot.png`

7) Add Desktop to PATH in `~/.bashrc` (show the editor containing the PATH addition in one screenshot) and then source; capture the source and a single invocation of `welcome` (from home) together:
```bash
vim ~/.bashrc
# add at end:
PATH=$PATH:~/Desktop
export PATH

source ~/.bashrc
cd ~
welcome
```
- Save screenshot as: `task4_bashrc_path_added_and_run.png` (single screenshot showing ~/.bashrc with PATH line and the subsequent `source` + `welcome` output)

📸 Screenshots (required):
- `task4_etc_environment_before.png`
- `task4_echo_path_before.png`
- `task4_etc_environment_edit_vim.png`
- `task4_etc_environment_after.png`
- `task4_echo_class_and_path.png`
- `task4_welcome_create_and_chmod.png`
- `task4_welcome_run_dot.png`
- `task4_bashrc_path_added_and_run.png`

Notes:
- If you prefer `~/bin`, substitute `~/Desktop` accordingly and capture the same grouped screenshots.

---

## Task 5 — Block and allow SSH using ufw

Goal: Use ufw to deny and allow SSH then verify SSH connectivity changes from host. Save screenshots after each logical command/step; group related print checks when appropriate.

Steps and required screenshots:

1) Enable ufw and show status (group both commands in one screenshot if you run them together):
```bash
sudo ufw enable
sudo ufw status verbose
```
- Save screenshot as: `task5_ufw_enable_and_status.png`

2) Deny TCP port 22 and show status (run deny and `status numbered` together and capture in one screenshot):
```bash
sudo ufw deny proto tcp to any port 22
sudo ufw status numbered
```
- Save screenshot as: `task5_ufw_deny_22_and_status.png`

3) From Windows host attempt to SSH (expected to fail) — capture the host-side SSH attempt in one screenshot:
```powershell
ssh username@<server_ip>
```
- Save screenshot as: `task5_ssh_attempt_blocked.png`

4) Allow SSH back and reload, then show status (group allow, reload, status in one screenshot if run together):
```bash
sudo ufw allow proto tcp to any port 22
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
ssh-keygen -t ed25519 -f ~/.ssh/id_ed25519 -C "lab_key"
ls -la ~/.ssh
```
- Save screenshot as: `task6_windows_sshkey_and_list.png` (single screenshot showing keygen result and `ls` of .ssh folder)

2) Show the public key content (single screenshot):
```powershell
type $env:USERPROFILE\.ssh\id_ed25519.pub
# or on Git Bash: cat ~/.ssh/id_ed25519.pub
```
- Save screenshot as: `task6_windows_public_key.png`

3) (Optional) Show known_hosts after first connection (single screenshot):
```powershell
type $env:USERPROFILE\.ssh\known_hosts
```
- Save screenshot as: `task6_windows_known_hosts.png`

B. On Ubuntu server — group related server-side commands:

1) Inspect `.ssh` and `authorized_keys` before adding key (capture both outputs in one screenshot):
```bash
ls -la ~/.ssh
cat ~/.ssh/authorized_keys || true
```
- Save screenshot as: `task6_server_ssh_folder_and_authorized_before.png`

2) Create `~/.ssh`, set permissions, append the public key (manual method), and show the resulting `authorized_keys` — capture the mkdir/chmod/echo and the final `cat` in one screenshot:
```bash
mkdir -p ~/.ssh
chmod 700 ~/.ssh
# paste public key line from Windows client into the echo below (or use ssh-copy-id)
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
ssh -i ~/.ssh/id_ed25519 username@<server_ip>
```
- Save screenshot as: `task6_ssh_with_identity_file.png`

Important notes:
- Do NOT show or upload private key files.
- Ensure server-side permissions are strict: `~/.ssh` 700, `authorized_keys` 600.

📸 Screenshots (required):
- `task6_windows_sshkey_and_list.png`
- `task6_windows_public_key.png`
- `task6_windows_known_hosts.png`
- `task6_server_ssh_folder_and_authorized_before.png`
- `task6_server_add_key_and_show.png`
- `task6_ssh_passwordless_login.png`
- `task6_ssh_with_identity_file.png`

---

## Submission

Create a repository `CC_<YourName>_<YourRollNumber>/Lab7` with:

```
Lab7/
  workspace/                    # any files you created (optional)
  screenshots/                  # include all screenshots listed in this lab
  Lab7.md                       # this lab manual (the file you are preparing)
```

Upload the screenshots with the exact filenames listed above. Make sure each grouped screenshot corresponds to the grouped commands described (e.g., all export commands in one screenshot; all related greps/prints together in one screenshot).

---

## Checklist (for students)

- [ ] Task 1: printed environment variables and saved combined grep screenshot  
- [ ] Task 2: exported DB_* variables (single screenshot for exports), ran grouped echoes and grep (single screenshots), and verified after restart (single screenshot)  
- [ ] Task 3: appended exports to ~/.bashrc (editor screenshot), sourced and verified (grouped screenshot), verified after restart (grouped screenshot)  
- [ ] Task 4: edited /etc/environment, added Class, created welcome script, updated PATH in ~/.bashrc, and saved grouped screenshots for related commands  
- [ ] Task 5: enabled ufw, denied and allowed SSH, attempted SSH from host (blocked and allowed) and saved grouped screenshots for related command sequences  
- [ ] Task 6: generated/located Windows SSH public key, pasted to server authorized_keys, and saved grouped screenshots for client/server steps and tests  
- [ ] Created and pushed `CC_<YourName>_<YourRollNumber>/Lab7` with Lab7.md and all screenshots

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
