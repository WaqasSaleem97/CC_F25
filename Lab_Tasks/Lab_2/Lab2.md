# Lab2: Hands-On Git & Version Control Lab  
**Estimated Duration:** 3 hours  
**Instructions:** Complete all practical tasks in a repository named `Lab2`. When finished, push your work to a repository named `CC_<student_Name>_<student_roll_number>`.  
**Navigation:** Click any task below to jump to its instructions.

---

## Task List

- [Getting Started](#getting-started)
- [Task 1: Create Private GitHub Repository](#task-1-create-private-github-repository)
- [Task 2: Connect Repository via SSH](#task-2-connect-repository-via-ssh)
- [Task 3: Configure Git Username and Email](#task-3-configure-git-username-and-email)
- [Task 4: Explore the .git Folder](#task-4-explore-the-git-folder)
- [Task 5: Local Repository Management](#task-5-local-repository-management)
- [Task 6: File Status & Staging](#task-6-file-status--staging)
- [Task 7: Branch Creation Using GitHub GUI](#task-7-branch-creation-using-github-gui)
- [Task 8: Branch Creation and Push Using Git Bash](#task-8-branch-creation-and-push-using-git-bash)
- [Task 9: Branching & Merging](#task-9-branching--merging)
- [Task 10: Pull Request and Branch Review (GitHub GUI)](#task-10-pull-request-and-branch-review-github-gui)
- [Task 11: Detailed Branch Strategy (Develop/Staging)](#task-11-detailed-branch-strategy-developstaging)
- [Bonus Task: Simulated Team Collaboration](#bonus-task-simulated-team-collaboration)
- [Exam Evaluation Questions](#exam-evaluation-questions)

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

## Task 3: Configure Git Username and Email

1. **Set up your Git identity (this ensures all commits are linked to you):**
   ```bash
   git config --global user.name "Your Name"
   git config --global user.email "your_email@example.com"
   ```
   - Save a screenshot of your terminal as `git_identity.png`.

2. **Verify your configuration:**
   ```bash
   git config --list
   ```
   - Screenshot as `git_config_list.png`.

---

## Task 4: Explore the .git Folder

1. Navigate into your cloned repository folder.
2. Show hidden files and locate the `.git` directory.
3. Explore what’s inside using:
   ```bash
   ls -a .git
   ```
   - Take a screenshot as `git_folder.png`.

4. Note: This folder contains history, logs, and config for your repository. Do **not** modify these files manually.

---

## Task 5: Local Repository Management

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
   git push -u origin main
   ```
   - Save a screenshot as `first_push.png`.

---

## Task 6: File Status & Staging

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

## Task 7: Branch Creation Using GitHub GUI

1. **On GitHub (web interface), create a branch named `bugfix/user-auth-error`.**
2. **Pull the branch to your local repository to sync.**
   ```bash
   git pull origin bugfix/user-auth-error
   ```
   - Take a screenshot of branch creation in the GUI as `bugfix_branch_gui.png`.
   - Take a screenshot of your local git branch list after pulling as `bugfix_branch_local.png`.

---

## Task 8: Branch Creation and Push Using Git Bash

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

## Task 9: Branching & Merging

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
3. Switch back to `main` and merge:
   ```bash
   git checkout main
   git merge feature-1
   ```
   - Screenshot as `merge.png`.
4. Push all branches:
   ```bash
   git push origin main
   git push origin feature-1
   ```
   - Screenshot as `push_branches.png`.

---

## Task 10: Pull Request and Branch Review (GitHub GUI)

1. **On GitHub, create a Pull Request from the branch `feature/db-connection` to `main`.**
2. **Review the Pull Request and merge it using the GitHub GUI.**
3. **After merging, delete the `feature/db-connection` branch using the GitHub GUI.**
   - Take a screenshot of:
     - The Pull Request creation (`pr_creation.png`)
     - The Pull Request review and merge (`pr_merge.png`)
     - The branch deletion (`branch_delete.png`)

---

## Task 11: Detailed Branch Strategy (Develop/Staging)

1. Create the following branches to simulate a professional branching strategy:
   - `develop`
   - `staging`
   - `feature/*`
   - `bugfix/*`

2. Example workflow:
   - Developers work on `feature/*` and `bugfix/*` branches.
   - Merge into `develop` after completion.
   - `develop` is merged into `staging` for testing.
   - Finally, `staging` is merged into `main` (production).

3. Document the workflow with screenshots:
   - Branch list (`branch_strategy.png`)
   - Merges into `develop` and `staging` (`branch_merges.png`)
   - Final merge into `main` (`final_merge.png`)

---

## Bonus Task: Simulated Team Collaboration

1. Create a branch `collab`.
2. Add a collaborator on your GitHub repo.
3. Both users:
   - Pull latest changes.
   - Add their name and fun fact to `notes.txt`.
   - Commit and push to the `collab` branch.
   - Screenshot as `collab_commit.png`.
4. Merge `collab` into `main` and push.
   ```bash
   git checkout main
   git merge collab
   git push origin main
   ```
   - Screenshot as `collab_merge.png`.

---
## Exam Evaluation Questions

1. **Advanced Branching & Merge Verification**  
   - Create a new branch in your repository.  
   - Make a small change in a file and commit it.  
   - Merge this branch back into the main branch.  
   - Show the history of commits in a way that verifies the merge.  
   - Screenshot as `Q1_Advanced Branching & Merge Verification.png`.

2. **Multi-Stage Workflow Simulation**  
   - Set up a branching workflow with three branches: `main`, `develop`, and `staging`.  
   - Create a feature branch from `develop`, make changes, and commit them.  
   - Merge the feature branch into `develop`, then move changes step by step into `staging` and finally into `main`.  
   - Provide proof that each stage contains the updated changes before it reaches `main`.
   - Screenshot as `Q2_Multi-Stage Workflow Simulation.png`.  

3. **Collaboration & Conflict Resolution**  
   - Work with a collaborator: both contributors should modify the same file but in separate branches.  
   - Attempt to merge the branches and capture the conflict.  
   - Resolve the conflict so that both contributions are preserved.  
   - Provide evidence that the final version of the file contains both collaborators’ changes.  
   - Screenshot as `Q3_Collaboration & Conflict Resolution.png`. 

---

## Submission

- Ensure all screenshots and files are committed and pushed to your repo `CC_<student_Name>_<student_roll_number>`.
- Include your name and roll number in `README.md`.
- No theoretical descriptions required—focus only on practical steps and screenshots.

---

*This lab is designed for hands-on mastery of essential Git concepts as covered in your course materials. All steps reflect real workflows you’ll use in professional software development.*
