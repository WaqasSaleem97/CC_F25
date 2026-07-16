# 🧪 Lab 07 – Environment Variables, PATH, UFW, and SSH Key Authentication

Estimated duration: 3 hours  
Instructions: Complete all tasks on the **same Ubuntu Server VM used in Lab 06**. Store this work inside your course repository at `CC/Labs/Lab07/` and push the completed work to GitHub.

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

## 📋 Task List

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

## 📸 Screenshot and identity rules

- Save every guided-task and Exam Evaluation screenshot in `Labs/Lab07/screenshots/`.
- Use every filename exactly as written, including capitalization and `.png`.
- Every step uses the consistent instruction `- Save screenshot as: <Screenshotname>`.
- Every guided task ends with a `📸 Screenshots (required):` list.
- A terminal screenshot must show the relevant command and its complete output.
- Ubuntu terminal evidence must show your normal prompt using your GitHub username, for example `githubusername@ubuntu`.
- Do not crop away the terminal prompt, relevant command, result, window title, or information needed to identify the task.
- Run related `grep`, `echo`, or consecutive variable-definition commands together when the step requests one combined screenshot.
- Never show or upload passwords, private SSH keys, access tokens, or other real secret values. The `DB_PASSWORD` used in this guided lab is a training-only placeholder and must never be replaced with a real password.
- Screenshots must be clear and readable. Blurred, incomplete, renamed, or unrelated evidence may receive zero marks.

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
- Save screenshot as: `task1_grep_shell_home_user.png` (single screenshot showing all three commands and outputs)

📸 Screenshots (required):
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
- Save screenshot as: `task2_exports_all.png` (single screenshot showing all three export commands)

2) Echo the three variables (run the three echo commands together) and capture one screenshot showing their outputs:
```bash
echo "$DB_URL"
echo "$DB_USER"
echo "$DB_PASSWORD"
```
- Save screenshot as: `task2_echoes_all.png` (single screenshot showing all three echo commands and outputs)

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
- Save screenshot as: `task2_after_restart_checks.png` (single screenshot showing the empty echo and no `DB_` results)

Notes:
- The single-screenshot-per-group rule helps graders quickly see related commands and their immediate outputs together.

📸 Screenshots (required):
- `task2_exports_all.png`
- `task2_echoes_all.png`
- `task2_printenv_grep_db.png`
- `task2_after_restart_checks.png`

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
- Save screenshot as: `task3_bashrc_added.png` (single screenshot showing all three export lines in the editor)

2) Source `~/.bashrc` and capture the source command in one screenshot together with the next verification commands (grouped): run `source ~/.bashrc` and then immediately run the three echoes and a single grep, capturing all of these in one screenshot:
```bash
source ~/.bashrc
echo "$DB_URL"
echo "$DB_USER"
echo "$DB_PASSWORD"
printenv | grep '^DB_'
```
- Save screenshot as: `task3_source_and_verification.png` (single screenshot showing `source`, all echoes, and the `grep` output)

3) Close and reopen terminal. Verify persistence by running one echo and the grep together — capture both in one screenshot:
```bash
echo "$DB_URL"
printenv | grep '^DB_'
```
- Save screenshot as: `task3_after_restart_persistent.png` (single screenshot showing the persistent value and listed `DB_` variables)

Notes:
- The instructor requires the editor view showing the three export lines (step 1) and grouped verification screenshots (steps 2–3).

📸 Screenshots (required):
- `task3_bashrc_added.png`
- `task3_source_and_verification.png`
- `task3_after_restart_persistent.png`

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
- Save screenshot as: `task4_etc_environment_edit_vim.png` (editor showing the new `Class` line)

After saving the file, display it again so the saved `Class` line is visible.

```bash
sudo cat /etc/environment
```

- Save screenshot as: `task4_etc_environment_after.png` (saved file showing the new `Class` line)

4) Re-login or open a new shell and show Class and PATH together (grouped prints): run `echo $Class` and `echo $PATH` together and capture in a single screenshot:
```bash
echo $Class
echo "$PATH"
```
- Save screenshot as: `task4_echo_class_and_path.png` (single screenshot showing both outputs)

5) Create `welcome` script at your home directory (`~/welcome`) and make it executable (capture the heredoc creation and chmod together in one screenshot if possible):
```bash
cat > ~/welcome <<'EOF'
#!/bin/bash
echo "Welcome to Cloud Computing $USER"
EOF

chmod +x ~/welcome
ls -l ~/welcome
```
- Save screenshot as: `task4_welcome_create_and_chmod.png` (single screenshot showing creation, `chmod`, and the executable file listing)

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
- Save screenshot as: `task4_bashrc_path_line.png` (editor clearly showing the added `PATH` line)

8) Apply the change and run `welcome` — capture these runtime commands in a separate screenshot (must be taken separately from the editor screenshot):
```bash
source ~/.bashrc
cd ~
welcome
```
- Save screenshot as: `task4_bashrc_source_and_welcome.png` (single screenshot showing the reload command and successful `welcome` output)

Notes:
- If you prefer `~/bin`, substitute `~/welcome` accordingly and capture the same grouped screenshots.

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
> ⚠️ Perform this step from the Ubuntu VM console, not from your only SSH session, so you do not lock yourself out.
>
- Save screenshot as: `task5_ufw_deny_22_and_status.png` (single screenshot showing the deny command and numbered status)

3) From Windows host attempt to SSH (expected to fail) — capture the host-side SSH attempt in one screenshot:
```powershell
ssh username@<server_ip>
```
- Save screenshot as: `task5_ssh_attempt_blocked.png` (Windows-side failed or timed-out SSH attempt)

4) Allow SSH back and reload, then show status (group allow, reload, status in one screenshot if run together). Use short form as requested:
```bash
sudo ufw allow 22/tcp
sudo ufw reload
sudo ufw status
```
- Save screenshot as: `task5_ufw_allow_reload_status.png` (single screenshot showing allow, reload, and final status)

5) From Windows host attempt SSH again (should succeed) — capture successful login in one screenshot:
```powershell
ssh username@<server_ip>
```
- Save screenshot as: `task5_ssh_success_after_allow.png` (Windows-side successful login showing the Ubuntu prompt)

Notes:
- When running multiple ufw commands consecutively, grouping them in one screenshot shows the command sequence and resulting status together.

📸 Screenshots (required):
- `task5_ufw_enable_and_status.png`
- `task5_ufw_deny_22_and_status.png`
- `task5_ssh_attempt_blocked.png`
- `task5_ufw_allow_reload_status.png`
- `task5_ssh_success_after_allow.png`

---

## Task 6 — Configure SSH key-based login from Windows host

Goal: Copy your public key from the Windows host into the Ubuntu server's `~/.ssh/authorized_keys` to allow passwordless SSH. Save grouped screenshots for the client-side actions and the server-side edits/checks.

A. On Windows host (client) — group related client actions:

1) Generate ed25519 key pair (if needed) and show the generated files in one screenshot (run `ssh-keygen` and then list `~/.ssh`):
```powershell
ssh-keygen -t ed25519 -f ~/.ssh/id_lab7 -C "lab_key"
ls -la ~/.ssh
```
- Save screenshot as: `task6_windows_sshkey_and_list.png` (key generation and file listing without private-key contents)

2) Show the public key content (single screenshot):
```powershell
type $env:USERPROFILE\.ssh\id_lab7.pub
# or on Git Bash: cat ~/.ssh/id_lab7.pub
```
- Save screenshot as: `task6_windows_public_key.png` (public key only; never display `id_lab7` private-key contents)

3) Clear the known_hosts file content and verify it is empty (single screenshot):
```powershell
# Clear contents (PowerShell)
Clear-Content $env:USERPROFILE\.ssh\known_hosts

# View the file (should be empty)
type $env:USERPROFILE\.ssh\known_hosts
```
- Save screenshot as: `task6_windows_known_hosts_cleared_and_empty.png` (clear command and empty verification)

4) Connect to the Ubuntu server using the standard SSH command (this will prompt to accept the server host key because known_hosts is empty). Capture the connection prompt/accept step in one screenshot:
```powershell
ssh username@<server_ip>
# Accept the host key prompt (yes) and complete the login (enter password or key passphrase)
```
- Save screenshot as: `task6_windows_ssh_accept_hostkey_and_login.png` (host-key acceptance and successful login)

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
chmod 600 ~/.ssh/authorized_keys
ls -ld ~/.ssh
ls -l ~/.ssh/authorized_keys
```
> ⚠️ This guided step removes existing authorized keys. Do it only on the disposable lab VM and ensure password or console access is available first.
>
- Save screenshot as: `task6_server_clear_authorized_keys.png` (preparation commands and resulting permissions)

2) Append the public key, set file permissions, and show the resulting `authorized_keys` (capture commands and resulting file content in one screenshot):
```bash
# paste public key name id_lab7.pub from Windows client into the echo below
echo "ssh-ed25519 AAAA... yourpublickey ... comment" >> ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
cat ~/.ssh/authorized_keys
ls -l ~/.ssh/authorized_keys
```
- Save screenshot as: `task6_server_add_key_and_show.png` (public-key entry and file permissions without any private key)

3) From Windows host test passwordless login (capture successful login in one screenshot):
```powershell
ssh username@<server_ip>
```
- Save screenshot as: `task6_ssh_passwordless_login.png` (successful login without a password prompt)

4) Also demonstrate explicit identity usage (single screenshot):
```powershell
ssh -i ~/.ssh/id_lab7 username@<server_ip>
```
- Save screenshot as: `task6_ssh_with_identity_file.png` (explicit identity command and successful Ubuntu login)

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

These are evaluation scenarios, not guided repetitions of Tasks 1–6. You must
decide which commands and configuration changes are appropriate. Commands and
copy-paste solutions are intentionally not provided.

### Q1: Create a security-conscious environment inventory

**Scenario:** A support engineer needs an environment report for diagnosing a
failed deployment. The report must contain useful configuration information but
must not disclose secret values.

**Required outcomes:**

1. Create a temporary local file named `environment-audit.txt` using values
   obtained from the running system rather than manually typing the final
   values.
2. The report must contain:
   - the current date and time;
   - the current user and hostname;
   - the effective `SHELL`, `HOME`, and `LANG` values;
   - the total number of entries in `PATH`;
   - every `PATH` directory displayed on a separate line.
3. Search the current environment for variable **names** containing
   `PASSWORD`, `PASS`, `TOKEN`, `SECRET`, or `KEY`. Display names only; do not
   display their values.
4. Verify that `environment-audit.txt` does not contain any complete password,
   token, private key, or secret value.
5. After taking the required screenshots, do not add
   `environment-audit.txt` to the repository. Only its screenshots are required.

📸 Screenshots (required):

- Save screenshot as: `EE_q1_audit_generation.png` (terminal evidence showing creation of the report from live environment information)
- Save screenshot as: `EE_q1_audit_report.png` (the displayed text file showing all required fields and one `PATH` directory per line)
- Save screenshot as: `EE_q1_sensitive_names_only.png` (the sensitive-name audit and verification that secret values were not written to the report)

---

### Q2: Prove the difference between a shell variable and an exported variable

**Scenario:** A program launched from a terminal cannot read a configuration
value even though the value appears in the current shell. Diagnose the scope
problem using a parent shell and a child Bash process.

**Required outcomes:**

1. In the parent shell, create `APP_MODE` with the value `staging` without
   exporting it.
2. Demonstrate that the parent shell can expand the value but that it is absent
   from the environment and unavailable to a newly launched child Bash process.
3. Export the same variable and demonstrate that both the environment and the
   child Bash process can now read it.
4. Remove `APP_MODE` and prove that it is absent from both the parent and a new
   child process.

📸 Screenshots (required):

- Save screenshot as: `EE_q2_unexported_scope.png` (parent value visible while environment and child-process checks show it is unavailable)
- Save screenshot as: `EE_q2_exported_scope.png` (exported value visible in the parent environment and child process)
- Save screenshot as: `EE_q2_unset_cleanup.png` (removal and final absence checks)

---

### Q3: Separate user-specific persistence from system-wide configuration

**Scenario:** A course setting should be available only to your account, while
an institutional setting should be available to every login account.

**Required outcomes:**

1. Configure a persistent user-specific variable named `USER_NOTICE`. Its value
   must contain your registration number in the form
   `Lab07-<registration-number>`.
2. Configure a system-wide variable named `DEPARTMENT` with the value
   `Computer Science`.
3. Start a fresh login session for your normal account and prove that both
   variables are available.
4. Start a login session for a different non-root account and prove that
   `DEPARTMENT` is available but `USER_NOTICE` is not inherited from your
   personal shell configuration. If the VM has no second non-root account,
   create a temporary test account and remove it after completing the evidence.
5. After collecting evidence, remove both exam variables from their respective
   configuration files and prove the cleanup.

📸 Screenshots (required):

- Save screenshot as: `EE_q3_user_persistence_config.png` (user-specific configuration containing `USER_NOTICE` and the unique registration-number value)
- Save screenshot as: `EE_q3_system_scope_config.png` (system-wide configuration containing `DEPARTMENT`)
- Save screenshot as: `EE_q3_owner_fresh_login.png` (a fresh normal-user login showing both values)
- Save screenshot as: `EE_q3_other_user_scope.png` (another non-root login showing the system value while the personal value is absent)
- Save screenshot as: `EE_q3_configuration_cleanup.png` (both exam entries removed after testing)

---

### Q4: Repair a command that cannot be found or executed

**Scenario:** A utility exists on disk, but users receive either
`command not found` or `permission denied` when they try to run it by name.
Diagnose and correct both deployment problems.

**Required outcomes:**

1. Create the directory `~/lab7-tools` and a Bash utility named `syscheck`
   inside it.
2. The utility must print the current user, hostname, working directory, and
   current date/time using values collected when the utility runs.
3. Initially leave the tool directory outside `PATH` and leave the utility
   non-executable. Demonstrate the initial failure without deleting the file.
4. Correct the executable permission and configure the tool directory as a
   persistent addition to your existing `PATH`. Do not replace or discard the
   existing `PATH` value.
5. Open a fresh session, change to `/tmp`, and run `syscheck` using only its
   command name—without an absolute path, relative path, or `./` prefix.
6. Show the resolved command location and successful output.

📸 Screenshots (required):

- Save screenshot as: `EE_q4_initial_command_failure.png` (the file exists but running it by name fails before configuration is repaired)
- Save screenshot as: `EE_q4_tool_and_path_fix.png` (utility contents, corrected executable permission, and persistent `PATH` configuration)
- Save screenshot as: `EE_q4_fresh_session_success.png` (a fresh session running from `/tmp`, the resolved command location, and complete `syscheck` output)

---

### Q5: Protect a temporary web service with a source-restricted firewall rule

**Scenario:** A temporary diagnostic web service must be reachable from your
Windows host but not broadly exposed to every network client. SSH access must
remain available throughout the evaluation.

**Required outcomes:**

1. Create a temporary local `index.html` containing your registration number
   and GitHub username.
2. Start a temporary HTTP service bound to the Ubuntu server network interface
   on TCP port `8080`, and prove that the port is listening.
3. From the Windows host, establish and capture a successful baseline request
   that returns your unique page.
4. Add a UFW rule that blocks TCP port `8080` and prove from the Windows host
   that the request now fails or times out.
5. Remove the broad block and configure access to TCP port `8080` for only your
   Windows host's source IP address. Do not create a broad allow-from-anywhere
   rule for port `8080`.
6. Prove that the Windows host can again retrieve the unique page and show the
   numbered firewall rules that enforce source-restricted access.
7. Show that the existing SSH rule remains allowed.
8. Stop the temporary service and remove all exam-specific port `8080` rules.
9. Do not add `index.html` to the repository. Only screenshots showing its
   contents and the working service are required.

📸 Screenshots (required):

- Save screenshot as: `EE_q5_service_and_listener.png` (displayed HTML contents plus evidence that TCP `8080` is listening)
- Save screenshot as: `EE_q5_client_baseline_success.png` (successful Windows-client response showing the unique page before the block)
- Save screenshot as: `EE_q5_ufw_block_rule.png` (numbered UFW status showing the temporary block without removing SSH access)
- Save screenshot as: `EE_q5_client_blocked.png` (failed or timed-out Windows-client request)
- Save screenshot as: `EE_q5_source_restricted_rule.png` (numbered UFW status showing port `8080` allowed only from the Windows client's source address and SSH still allowed)
- Save screenshot as: `EE_q5_client_restricted_success.png` (successful response after applying the source-restricted rule)
- Save screenshot as: `EE_q5_firewall_cleanup.png` (service stopped and exam-specific `8080` rules removed)

---

### Q6: Verify SSH key identity with fingerprints and non-interactive tests

**Scenario:** An administrator suspects that SSH access is succeeding through
an SSH agent or an unintended key. Prove exactly which key is authorized and
used, and demonstrate that an unregistered key is rejected.

**Required outcomes:**

1. On the Windows host, create a separate Ed25519 key pair named
   `id_lab7_exam`. Use a public-key comment containing your GitHub username and
   `lab7-exam`.
2. Display the new public-key fingerprint and list the key files without
   displaying the private-key contents.
3. Add only the new public key to the Ubuntu account's authorized keys while
   preserving any legitimate existing keys.
4. Verify the server-side SSH directory and authorized-key file permissions.
5. Generate a fingerprint for the newly authorized server-side public key and
   prove that it matches the Windows public-key fingerprint.
6. Perform a non-interactive SSH test that:
   - disables password prompting;
   - explicitly selects `id_lab7_exam`;
   - prevents unrelated agent identities from being tried;
   - runs remote identity commands that show the Ubuntu user and hostname.
7. Repeat the non-interactive test using a different, unregistered identity and
   prove that authentication is rejected.
8. Remove only the exam public-key entry from the server and preserve the
   student's normal Lab 07 key.

📸 Screenshots (required):

- Save screenshot as: `EE_q6_client_key_fingerprint.png` (client key-file listing and public-key fingerprint without private-key contents)
- Save screenshot as: `EE_q6_server_key_permissions.png` (server authorized-key entry and required SSH file permissions)
- Save screenshot as: `EE_q6_fingerprint_match.png` (clear evidence that client and server fingerprints match)
- Save screenshot as: `EE_q6_explicit_key_success.png` (non-interactive explicit-identity login showing the remote user and hostname without a password prompt)
- Save screenshot as: `EE_q6_unregistered_key_failure.png` (non-interactive authentication failure with an unregistered identity)
- Save screenshot as: `EE_q6_exam_key_cleanup.png` (only the exam key removed while the normal Lab 07 key remains intact)

### Exam evaluation checklist

- [ ] Q1 environment inventory contains useful values but no secret values.
- [ ] Q2 proves local shell, exported environment, child-process, and unset
      behavior.
- [ ] Q3 proves the difference between user-specific and system-wide
      persistence, followed by cleanup.
- [ ] Q4 diagnoses and fixes both executable permission and persistent `PATH`
      configuration.
- [ ] Q5 demonstrates baseline, blocked, source-restricted, and cleaned-up
      network access without disrupting SSH.
- [ ] Q6 proves key identity by fingerprint, succeeds with the explicit key,
      fails with an unregistered key, and cleans up safely.

---


## Submission

Use the following structure in your `CC` course repository:

```
CC/
└── Labs/
    └── Lab07/
        ├── Lab07.md                     # optional
        ├── Lab07_solution.docx          # optional
        ├── Lab07_solution.pdf           
        └── screenshots/                 # all Task 1–6 and Exam Evaluation screenshots
```

Before pushing, confirm that every required screenshot is in the correct folder
and uses the exact filename stated immediately after its step.

Do not upload `environment-audit.txt` or the temporary `index.html`. Submit only
the screenshots that display the required contents and results.

---

## Checklist

- [ ] Task 1: printed environment variables and saved combined grep screenshot  
- [ ] Task 2: exported DB_* variables (single screenshot for exports), ran grouped echoes and grep (single screenshots), and verified after restart (single screenshot)  
- [ ] Task 3: appended exports to ~/.bashrc (editor screenshot), sourced and verified (grouped screenshot), verified after restart (grouped screenshot)  
- [ ] Task 4: edited /etc/environment, added Class, created ~/welcome script, updated PATH in ~/.bashrc (PATH line only, no export), and saved separate screenshots for the editor and for the source+run steps  
- [ ] Task 5: enabled ufw, denied and allowed SSH (using `sudo ufw deny 22/tcp` and `sudo ufw allow 22/tcp`), attempted SSH from host (blocked and allowed) and saved grouped screenshots for related command sequences  
- [ ] Task 6: generated/located Windows SSH public key using filename `id_lab7`, cleared known_hosts and demonstrated host-key acceptance, prepared server `~/.ssh` and cleared `authorized_keys`, pasted public key to server authorized_keys, and saved grouped screenshots for client/server steps and tests  
- [ ] Stored all guided-task screenshots in `Labs/Lab07/screenshots/` using the exact filenames  
- [ ] Completed all six Exam Evaluation Questions and stored their screenshots in `Labs/Lab07/screenshots/`  
- [ ] Did not upload `environment-audit.txt` or the temporary `index.html`  
- [ ] Confirmed that no private key, real password, token, or other secret was committed  
- [ ] Pushed the complete `CC/Labs/Lab07/` directory to the `CC` repository

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

Good luck — take each screenshot immediately after its requested step, use the
exact filename, and group related outputs only where the step explicitly asks
for one combined screenshot.
