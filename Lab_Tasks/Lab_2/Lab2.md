# Lab2: Hands-On Git & Version Control Lab  
**Estimated Duration:** 3 hours  
**Instructions:** Complete all practical tasks in a repository named `Lab2`. When finished, push your work to a repository named `CC_<student_Name>_<student_roll_number>`.  
**Navigation:** Click any task below to jump to its instructions.

---

## Task List

- [Getting Started](#getting-started)
- [Task 1: Create Private GitHub Repository](#task-1-create-private-github-repository)
- [Task 2: Connect Repository via SSH](#task-2-connect-repository-via-ssh)
- [Task 3: Local Repository Management](#task-3-local-repository-management)
- [Task 4: File Status & Staging](#task-4-file-status--staging)
- [Task 5: Branch Creation Using GitHub GUI](#task-5-branch-creation-using-github-gui)
- [Task 6: Branch Creation and Push Using Git Bash](#task-6-branch-creation-and-push-using-git-bash)
- [Task 7: Branching & Merging](#task-7-branching--merging)
- [Task 8: Pull Request and Branch Review (GitHub GUI)](#task-8-pull-request-and-branch-review-github-gui)
- [Bonus Task: Simulated Team Collaboration](#bonus-task-simulated-team-collaboration)

---

## Getting Started

1. **Install Git on your PC.**  
   - [Download Git for Windows, Mac, or Linux](https://git-scm.com/downloads)
   - Take a screenshot of your installation and save it as `git_installation.png` in your Lab2 repo.

---

## Task 1: Create Private GitHub Repository

1. **Create a new private repository named `Lab2` on GitHub.**
2. Take a screenshot of your repo settings showing it's private. Save as `repo_private.png` in your Lab2 repo.

---

## Task 2: Connect Repository via SSH

1. **Generate a new SSH key using PowerShell:**
   ```powershell
   ssh-keygen -t ed25519 -C "your_email@example.com"
   ```
   - Save a screenshot of your terminal after key generation as `ssh_keygen.png`.
2. **Add your SSH public key to GitHub (Settings > SSH and GPG keys).**
   - Take a screenshot of the added key as `github_sshkey.png`.
3. **Clone your `Lab2` repo using SSH.**
   ```bash
   git clone git@github.com:<yourusername>/Lab2.git
   ```
   - Save a screenshot as `ssh_clone.png`.

---

## Task 3: Local Repository Management

1. **Delete the existing `.git` folder from your cloned repo using Git Bash:**
   ```bash
   rm -rf .git
   ```
   - Take a screenshot as `delete_git.png`.
2. **Re-initialize the local git repository:**
   ```bash
   git init
   ```
   - Save a screenshot as `git_init.png`.
3. **Add a file named `README.md` and commit it:**
   ```bash
   echo "# Lab2 Git Practice" > README.md
   git add README.md
   git commit -m "Initial commit"
   ```
   - Save a screenshot as `first_commit.png`.
4. **Connect your local repo to GitHub and push:**
   ```bash
   git remote add origin git@github.com:<yourusername>/Lab2.git
   git push -u origin master
   ```
   - Save a screenshot as `first_push.png`.

---

## Task 4: File Status & Staging

1. Create a new file `notes.txt` and write a note.
2. Check status:
   ```bash
   git status
   ```
   - Screenshot as `status1.png`.
3. Stage and commit:
   ```bash
   git add notes.txt
   git commit -m "Add notes.txt"
   ```
   - Screenshot as `commit_notes.png`.
4. Edit `notes.txt` and repeat status/commit steps.

---

## Task 5: Branch Creation Using GitHub GUI

1. **On GitHub (web interface), create a branch named `bugfix/user-auth-error`.**
2. **Pull the branch to your local repository to sync.**
   ```bash
   git pull origin bugfix/user-auth-error
   ```
   - Take a screenshot of branch creation in the GUI as `bugfix_branch_gui.png`.
   - Take a screenshot of your local git branch list after pulling as `bugfix_branch_local.png`.

---

## Task 6: Branch Creation and Push Using Git Bash

1. **Create a branch named `feature/db-connection` using Git Bash:**
   ```bash
   git checkout -b feature/db-connection
   ```
2. **Push the branch to the remote repository:**
   ```bash
   git push origin feature/db-connection
   ```
   - Take a screenshot of the branch creation and push as `feature_db_branch.png`.

---

## Task 7: Branching & Merging

1. Create and switch to a branch `feature-1`:
   ```bash
   git checkout -b feature-1
   ```
   - Screenshot as `branch_create.png`.
2. Modify `main.py` (add a function) and commit.
   ```bash
   git add main.py
   git commit -m "Add new function to main.py"
   ```
   - Screenshot as `feature_commit.png`.
3. Switch back to `master` and merge:
   ```bash
   git checkout master
   git merge feature-1
   ```
   - Screenshot as `merge.png`.
4. Push all branches:
   ```bash
   git push origin master
   git push origin feature-1
   ```
   - Screenshot as `push_branches.png`.

---

## Task 8: Pull Request and Branch Review (GitHub GUI)

1. **On GitHub, create a Pull Request from the branch `feature/db-connection` to `main`.**
2. **Review the Pull Request and merge it using the GitHub GUI.**
3. **After merging, delete the `feature/db-connection` branch using the GitHub GUI.**
   - Take a screenshot of:
     - The Pull Request creation (`pr_creation.png`)
     - The Pull Request review and merge (`pr_merge.png`)
     - The branch deletion (`branch_delete.png`)

---

## Bonus Task: Simulated Team Collaboration

1. Create a branch `collab`.
2. Add a collaborator on your GitHub repo.
3. Both users:
   - Pull latest changes.
   - Add their name and fun fact to `notes.txt`.
   - Commit and push to the `collab` branch.
   - Screenshot as `collab_commit.png`.
4. Merge `collab` into `master` and push.
   ```bash
   git checkout master
   git merge collab
   git push origin master
   ```
   - Screenshot as `collab_merge.png`.

---

## Submission

- Ensure all screenshots and files are committed and pushed to your repo `CC_<student_Name>_<student_roll_number>`.
- Include your name and roll number in `README.md`.
- No theoretical descriptions required—focus only on practical steps and screenshots.

---

*This lab is designed for hands-on mastery of essential Git concepts as covered in your course materials. All steps reflect real workflows you’ll use in professional software development.*
