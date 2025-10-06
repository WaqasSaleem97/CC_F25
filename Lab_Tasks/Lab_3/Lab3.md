# 🧪 Lab 3 – Working with Git History, Stashing, and Reverting Commits

**Estimated Duration:** 3 hours  
**Instructions:** Complete all practical tasks in a repository named `Lab3`. When finished, push your work to a repository named `CC_<student_Name>_<student_roll_number>`.  

---

## 🎯 Objective
This lab will guide you through advanced Git operations that help manage commit history, recover previous versions, and safely undo mistakes both locally and remotely.

By the end of this lab, you will be able to:
- Handle local and remote commit conflicts  
- Resolve merge conflicts manually  
- Manage ignored files using `.gitignore`  
- Temporarily store and recover uncommitted changes  
- Reset commits (soft vs hard reset)  
- Amend recent commits  
- Revert commits locally and remotely  
- Inspect commit history using hashes  
- Work safely with `git push --force`

---

## 🧩 Prerequisites
- A working Git repository (can be your previous lab repo or a new one)
- Visual Studio Code (or any text editor)
- Git Bash or terminal access
- An active GitHub repository connected via SSH

---
**Navigation:** Click any task below to jump to its instructions.
## 📋 Task List

- [Task 1: Handling Local and Remote Commit Conflicts (Pull vs Pull --rebase)](#task-1--handling-local-and-remote-commit-conflicts-pull-vs-pull---rebase)
- [Task 2: Creating and Resolving Merge Conflicts Manually](#task-2--creating-and-resolving-merge-conflicts-manually)
- [Task 3: Managing Ignored Files with .gitignore and Removing Tracked Files](#task-3--managing-ignored-files-with-gitignore-and-removing-tracked-files)
- [Task 4: Create Temporary Changes and Use git stash](#task-4--create-temporary-changes-and-use-git-stash)
- [Task 5: Checkout a Specific Commit Using git log](#task-5--checkout-a-specific-commit-using-git-log)
- [Task 6: Resetting Commits (Soft vs Hard Reset)](#task-6--resetting-commits-soft-vs-hard-reset-with-verification-steps)
- [Task 7: Amending the Last Commit](#task-7--amending-the-last-commit)
- [Task 8: Reverting a Commit (Safe Undo on Remote Branch)](#task-8--reverting-a-commit-safe-undo-on-remote-branch)
- [Task 9: Force Push (With Caution)](#task-9--force-push-with-caution)
- [Task 10: Running Gitea in GitHub Codespaces via Docker Compose](#task-10--running-gitea-in-github-codespaces-via-docker-compose)
- [Hands-On Practical Exam Questions](#hands-on-practical-exam-questions)
- [Summary](#summary)
- [Submission](#submission)

---

## Task 1 – Handling Local and Remote Commit Conflicts (Pull vs Pull --rebase)

This task demonstrates what happens when your local repository and remote repository both have new commits — and how `git pull` and `git pull --rebase` behave differently.

### Steps

1. Open your repository on **GitHub** and edit the `README.md` file directly in the browser.  
   - Add a new line such as:  
     ```
     This line was added remotely from GitHub.
     ```
   - Commit your change on GitHub (e.g., message: *Remote update to README*).
   - Save a screenshot of your browser showing the change as `remote_edit.png`.

2. On your **local machine**, open the same repository folder.  
   - Edit the same `README.md` file locally, for example:  
     ```
     This line was added locally.
     ```
   - Stage and commit your change:
     ```bash
     git add README.md
     git commit -m "Local update to README"
     ```
   - Save a screenshot of your terminal as `local_commit.png`.

3. Try to push:
   ```bash
   git push origin main
   ```
   You’ll see an error message similar to:
   ```
   ! [rejected] main -> main (fetch first)
   error: failed to push some refs to 'github.com:username/repo.git'
   hint: Updates were rejected because the remote contains work that you do not have locally.
   ```
   - Save a screenshot of this error as `push_error.png`.

4. To fix this, pull the latest changes from remote:
   ```bash
   git pull origin main
   ```
   - Git will **merge** your local and remote commits, creating a *merge commit*.
   - Save a screenshot as `merge_commit.png`.

5. Push again:
   ```bash
   git push origin main
   ```
   - Save a screenshot as `push_after_merge.png`.

6. Now repeat the same process, but this time use **rebase instead of merge**:
   - Make another remote edit on GitHub in `README.md`.  
   - Then make another local edit and commit it.  
   - Instead of using `git pull`, run:
     ```bash
     git pull --rebase origin main
     ```
     - Git will **replay your local commits on top** of the pulled commits, keeping history linear.
     - Save a screenshot as `rebase_pull.png`.

📸 **Screenshot Required:**  
- `remote_edit.png`  
- `local_commit.png`  
- `push_error.png`  
- `merge_commit.png`  
- `push_after_merge.png`  
- `rebase_pull.png`

---

**Summary:**  
- `git pull` merges remote changes with your local commits, possibly creating a merge commit.
- `git pull --rebase` keeps commit history linear by replaying your local commits on top of the updated remote branch.
- Both are ways to resolve push conflicts when your local repo is behind the remote.

---

## Task 2 – Creating and Resolving Merge Conflicts Manually

This task will help you understand how Git handles file conflicts when two people modify the same line of code differently.

### Steps

1. On **GitHub (remote)**, open your `README.md` file and change an existing line.  
   For example, if it currently says:
   ```
   This line was added remotely from GitHub.
   ```
   Change it to:
   ```
   This line was updated remotely again.
   ```
   - Commit your change with the message: *Remote conflicting change*  
   - Save a screenshot as `remote_conflict_edit.png`.

2. On your **local machine**, edit the **same line** in the same file but make a **different change**:  
   ```
   This line was updated locally at the same time.
   ```
   - Save a screenshot of your edit in VS Code as `local_conflict_edit.png`.

3. Stage and commit your local change:
   ```bash
   git add README.md
   git commit -m "Local conflicting change"
   ```
   - Save a screenshot as `local_conflict_commit.png`.

4. Try to push:
   ```bash
   git push origin main
   ```
   - The push will be **rejected**, since the remote version has conflicting changes.  
   - Save a screenshot of this error as `conflict_push_error.png`.

5. Pull with rebase to bring in remote changes:
   ```bash
   git pull --rebase origin main
   ```
   - Git will **pause** the rebase and notify you about a conflict.  
   - Save a screenshot of the conflict message as `conflict_message.png`.

6. Open the `README.md` file in your editor — you’ll see conflict markers like this:
   ```
   <<<<<<< HEAD
   This line was updated locally at the same time.
   =======
   This line was updated remotely again.
   >>>>>>> origin/main
   ```
   - Manually edit the file to keep the correct line (for example, merge both ideas).  
   - Save a screenshot of the resolved file as `resolved_readme.png`.

7. After fixing the file, mark the conflict as resolved and continue the rebase:
   ```bash
   git add README.md
   git rebase --continue
   ```
   - Save a screenshot of this step as `rebase_continue.png`.

8. Finally, push your changes:
   ```bash
   git push origin main
   ```
   - Save a screenshot as `push_after_resolve.png`.

📸 **Screenshot Required:**  
`remote_conflict_edit.png`, `local_conflict_edit.png`, `local_conflict_commit.png`,  
`conflict_push_error.png`, `conflict_message.png`, `resolved_readme.png`,  
`rebase_continue.png`, `push_after_resolve.png`

---

**Summary:**  
- When two people edit the same line, Git will pause and ask for manual conflict resolution.
- Conflict markers appear in the file and you must choose what to keep.
- After resolving, continue the rebase and push to remote.
- This process ensures both sets of changes are handled safely.

---

## Task 3 – Managing Ignored Files with .gitignore and Removing Tracked Files

In this task, you will learn how to use a `.gitignore` file to exclude files or directories from version control and how to remove them from Git’s tracking while keeping them locally.

### Steps

1. Create a new folder named `textfiles` inside your repository:
   ```bash
   mkdir textfiles
   ```

2. Inside the `textfiles` folder, create three text files:
   ```bash
   echo "File A content" > textfiles/a.txt
   echo "File B content" > textfiles/b.txt
   echo "File C content" > textfiles/c.txt
   ```

3. Add and commit the new directory:
   ```bash
   git add .
   git commit -m "Added textfiles directory with three files"
   git push origin main
   ```
   - Save a screenshot as `push_textfiles.png`.

4. Now, create a `.gitignore` file in the root of your repository:
   ```bash
   echo "textfiles/*" > .gitignore
   ```

5. Add and commit the `.gitignore` file:
   ```bash
   git add .gitignore
   git commit -m "Added .gitignore to ignore textfiles directory"
   git push origin main
   ```
   - Save a screenshot as `gitignore_push.png`.

6. Go to **GitHub** and check your repository — notice that the `textfiles` directory is **still visible** on the remote.
   - Save a screenshot as `repo_still_has_textfiles.png`.

7. To remove already tracked files from Git (but not from your local system), run:
   ```bash
   git rm -r --cached textfiles
   ```

8. Commit and push again:
   ```bash
   git add .
   git commit -m "Removed tracked textfiles directory"
   git push origin main
   ```
   - Save a screenshot as `rm_cached_push.png`.

9. Check your GitHub repository again — the `textfiles` folder should now be **deleted remotely**.
   - Save a screenshot as `repo_textfiles_removed.png`.

📸 **Screenshot Required:**  
`push_textfiles.png`, `gitignore_push.png`, `repo_still_has_textfiles.png`,  
`rm_cached_push.png`, `repo_textfiles_removed.png`

---

**Summary:**  
- `.gitignore` prevents new files from being tracked, but does not remove ones already tracked.
- To untrack files that are already committed, use `git rm --cached`.
- This lets you keep files locally but remove them from the remote repo.

---

## Task 4 – Create Temporary Changes and Use git stash

This task demonstrates how `git stash` allows you to temporarily save uncommitted changes so that you can safely switch branches without losing work.

### Steps

1. Open your repository folder in **VS Code**.  
2. Modify any file (for example, `README.md`) by adding a few test lines.  
   - Save a screenshot as `modified_readme.png`.

3. Without committing or stashing the changes, try to switch to another branch:
   ```bash
   git checkout main
   ```
   or
   ```bash
   git checkout -b new-feature
   ```
   You’ll see an error message similar to:
   ```
   error: Your local changes to the following files would be overwritten by checkout:
   README.md
   Please commit your changes or stash them before you switch branches.
   ```
   - Save a screenshot as `checkout_error.png`.

4. To fix this, **stash your changes**:
   ```bash
   git stash
   ```
   - Save a screenshot as `stash_command.png`.

5. Try switching branches again — it should now work:
   ```bash
   git checkout main
   ```
   - Save a screenshot as `branch_switched.png`.

6. Return to your previous branch (for example, `feature-branch`):
   ```bash
   git checkout feature-branch
   ```
   - Save a screenshot as `back_to_feature.png`.

7. Check your working directory status:
   ```bash
   git status
   ```
   It should show:
   ```
   nothing to commit, working tree clean
   ```
   - Save a screenshot as `status_clean.png`.

8. Now restore your stashed changes:
   ```bash
   git stash pop
   ```
   You’ll see a message indicating that changes were reapplied:
   ```
   On branch feature-branch
   Changes not staged for commit:
     (use "git add <file>..." to update what will be committed)
     (use "git restore <file>..." to discard changes in working directory)
   ```
   - Save a screenshot as `stash_pop.png`.

9. Confirm that your previous edits are back in the file.

📸 **Screenshot Required:**  
`modified_readme.png`, `checkout_error.png`, `stash_command.png`, `branch_switched.png`,  
`back_to_feature.png`, `status_clean.png`, `stash_pop.png`

---

**Summary:**  
- `git stash` saves your uncommitted changes, letting you switch branches safely.
- You can restore stashed changes anytime with `git stash pop`.
- This is useful for quickly saving work-in-progress without cluttering commit history.

---

## Task 5 – Checkout a Specific Commit Using git log

1. View commit history:
   ```bash
   git log --oneline
   ```
   - Save a screenshot as `log_before_checkout.png`.

2. Copy any previous commit hash (e.g., `a1b2c3d`).  
3. Checkout that commit:
   ```bash
   git checkout <commit-hash>
   ```
   - Save a screenshot as `detached_head.png`.

4. To return to your main branch:
   ```bash
   git checkout main
   ```
   - Save a screenshot as `back_to_main.png`.

📸 **Screenshot Required:**  
`log_before_checkout.png`, `detached_head.png`, `back_to_main.png`

---

**Summary:**  
- You can revisit past versions by checking out a specific commit hash.
- In detached HEAD state, changes are not attached to any branch.
- Always return to your main branch after inspecting old commits.

---

## Task 6 – Resetting Commits (Soft vs Hard Reset) **(With Verification Steps)**

This task demonstrates the difference between a soft and hard reset in Git. In this version, you will **verify the presence of changes in files and confirm the existence of commits after each reset**.

### Steps

1. **Add a new line in any file and commit it:**
   ```bash
   # Edit any file (e.g., README.md), add a line
   git add .
   git commit -m "Added test line"
   ```
   - Save a screenshot as `first_commit.png`.

2. **Add another change and commit again:**
   ```bash
   # Edit the file again, add a different line
   git add .
   git commit -m "Second test commit"
   ```
   - Save a screenshot as `second_commit.png`.

3. **View the history before reset:**
   ```bash
   git log --oneline
   ```
   - Save a screenshot as `log_before_reset.png`.

4. **Check file contents for both changes:**
   - Open the edited file (e.g., README.md) and visually confirm BOTH added lines exist.
   - Save a screenshot as `file_before_reset.png`.


5. **Perform a soft reset (keeps changes in working directory):**
   ```bash
   git reset --soft HEAD~1
   ```
   - Save a screenshot as `soft_reset.png`.

6. **Check commit history after soft reset:**
   ```bash
   git log --oneline
   ```
   - Save a screenshot as `log_after_soft_reset.png`.

7. **Verify changes in the file:**
   - Open the file again and confirm both edits are still present (nothing lost).
   - Save a screenshot as `file_after_soft_reset.png`.

8. **Check git status:**  
   ```bash
   git status
   ```
   - You should see your changes are staged (ready to commit again).

9. **Perform commit**
   ```bash
   git commit -m "Second Test commit"
   ```
   - Save a screenshot as `file_after_hard_reset.png`.

10. **Perform a hard reset (discards changes completely):**
   ```bash
   git reset --hard HEAD~1
   ```
   - Save a screenshot as `hard_reset.png`.

11. **Check commit history after hard reset:**
    ```bash
    git log --oneline
    ```
    - Save a screenshot as `log_after_hard_reset.png`.

12. **Verify changes in the file:**
    - Open the file and confirm the **second edit is gone** (reverted to previous commit).
    - Save a screenshot as `file_after_hard_reset.png`.

13. **Check git status:**
    ```bash
    git status
    ```
    - Should report "nothing to commit, working tree clean".

---

### 📸 **Screenshot Required:**
- `first_commit.png`
- `second_commit.png`
- `log_before_reset.png`
- `file_before_reset.png`
- `soft_reset.png`
- `log_after_soft_reset.png`
- `file_after_soft_reset.png`
- `file_after_hard_reset.png`
- `hard_reset.png`
- `log_after_hard_reset.png`
- `file_after_hard_reset.png`

---

**Summary:**  
- Soft reset keeps your changes in the file (you can recommit them).
- Hard reset removes the latest commit AND the associated changes from your files.
- Always check both the commit history and your file contents after resetting to understand what was kept and what was discarded.

---

## Task 7 – Amending the Last Commit

1. Make a small change in any file.  
2. Stage it and commit:
   ```bash
   git add .
   git commit -m "Fix log message"
   ```
   - Save a screenshot as `first_amend_commit.png`.

3. Realize you forgot to change another file. Update it now.  
4. Add it and amend the last commit:
   ```bash
   git add .
   git commit --amend
   ```
   - Save a screenshot as `amend_commit.png`.

📸 **Screenshot Required:**  
`first_amend_commit.png`, `amend_commit.png`

---

**Summary:**  
- `git commit --amend` lets you modify your last commit, including its message or staged files.
- Useful for quick fixes before pushing to remote.
- Once pushed, amending requires force-push to update remote history.

---

## Task 8 – Reverting a Commit (Safe Undo on Remote Branch)

1. Make a change and commit it:
   ```bash
   echo "temporary text" >> temp.txt
   git add .
   git commit -m "Added temporary text file"
   git push origin main
   ```
   - Save a screenshot as `commit_temp_file.png`.

2. Now revert that commit safely (do not delete it):
   ```bash
   git log --oneline
   git revert <commit-hash>
   ```
   - Save a screenshot as `revert_commit.png`.

3. Push the revert commit:
   ```bash
   git push origin main
   ```
   - Save a screenshot as `revert_push.png`.

📸 **Screenshot Required:**  
`commit_temp_file.png`, `revert_commit.png`, `revert_push.png`

---

**Summary:**  
- `git revert <hash>` creates a new commit that undoes changes from a specific commit.
- It does not rewrite history, making it safe for shared branches.
- Always use revert (not reset) for undoing changes on remote branches.

---

## Task 9 – Force Push (With Caution)

> ⚠️ Use this only in your own branch (never on `main` or `develop`).

1. Create a new branch:
   ```bash
   git checkout -b test-force
   ```
   - Save a screenshot as `new_branch.png`.

2. Make and commit a small change.
   - Save a screenshot as `force_commit.png`.

3. Push it:
   ```bash
   git push origin test-force
   ```
   - Save a screenshot as `push_force_branch.png`.

4. Perform a hard reset:
   ```bash
   git reset --hard HEAD~1
   ```
   - Save a screenshot as `hard_reset_force.png`.

5. Now force-push to remote:
   ```bash
   git push origin test-force --force
   ```
   - Save a screenshot as `force_push.png`.

📸 **Screenshot Required:**  
`new_branch.png`, `force_commit.png`, `push_force_branch.png`, `hard_reset_force.png`, `force_push.png`

---

**Summary:**  
- Force push (`git push --force`) rewrites remote branch history, overwriting changes.
- Only use on non-shared branches to avoid disrupting collaborators.
- Always double-check before force-pushing.

---

## Task 10 – Running Gitea in GitHub Codespaces via Docker Compose

This task will guide you through forking a repository, running a full web application in a GitHub Codespace, and interacting with Gitea (a self-hosted Git service) inside Codespaces.

### Steps

1. **Fork the Gitea Repository**
   - Go to [WaqasSaleem97/Gitea](https://github.com/WaqasSaleem97/Gitea).
   - Click the **Fork** button at the top right and fork it into your own GitHub account.
   - Save a screenshot of your forked repository page as `forked_gitea.png`.

2. **Open the Forked Repo in GitHub Codespaces**
   - On your forked repo, click **Code** > **Codespaces** > **Create codespace on main**.
   - Save a screenshot of the Codespace loading as `codespace_loading.png`.

3. **Start Gitea with Docker Compose**
   - In the Codespace terminal, run:
     ```bash
     docker compose up
     ```
   - Wait for the containers to start.
   - Save a screenshot of the terminal showing the containers running as `docker_up.png`.

4. **Access Gitea Web Interface**
   - In Codespaces, forward **port 3000** (see Codespaces port forwarding UI).
   - Click the forwarded port 3000 link to open Gitea in your browser tab.
   - Save a screenshot of the Gitea install page as `gitea_install_page.png`.

5. **Install Gitea**
   - Fill out the installation form, providing an **admin username** and **password** (e.g., `admin` / `yourpassword`).
   - Save a screenshot of the completed setup (show the admin account setup) as `admin_setup.png`.

6. **Log In to Gitea**
   - Use your admin credentials to log in.
   - Save a screenshot of the Gitea dashboard after login as `gitea_dashboard.png`.

7. **Create a New Repository in Gitea**
   - Click **New Repository** or **+** > **New Repository** in Gitea.
   - Fill out the repository details and create it.
   - Save a screenshot of the new repository page as `gitea_new_repo.png`.

📸 **Screenshot Required:**  
- `forked_gitea.png`  
- `codespace_loading.png`  
- `docker_up.png`  
- `gitea_install_page.png`  
- `admin_setup.png`  
- `gitea_dashboard.png`  
- `gitea_new_repo.png`

---

**Summary:**  
- You forked a repo and ran a real-world application using Docker Compose in Codespaces.
- You installed and configured a self-hosted Git service (Gitea) from scratch.
- You practiced using both GitHub and Gitea to create and manage repositories.

---

## Hands-On Practical Exam Questions

Answer the following practical exam questions to demonstrate your understanding of the Git concepts covered in this lab. Provide commands, screenshots, and explanations as required. **Do not provide solutions here; only attempt these questions in your submission file.**

1. **Local vs Remote Conflict Resolution:**  
   Simulate a scenario where both your local and remote repositories have new commits on the same file. Demonstrate how to resolve the conflict using both `git pull` (merge) and `git pull --rebase`. Show the difference in history and provide screenshots.

2. **Manual Merge Conflict Handling:**  
   Create a manual conflict between your local and remote branches on the same line in a file. Show how to resolve the conflict, commit the resolution, and push the changes. Provide conflict markers and the final resolved file screenshot.

3. **Managing Ignored and Tracked Files:**  
   Add a directory with files to your repo, then add it to your `.gitignore`. Show the process and commands needed to remove the directory from tracking while keeping it locally. Include before and after screenshots of your repo.

4. **Using Stash for Temporary Changes:**  
   Make changes to a file without committing. Use `git stash` to save work, switch branches, and then restore stashed changes. Document the process with commands and screenshots showing the stash and restore steps.

5. **Commit History Manipulation and Recovery:**  
   Make two commits, then perform a soft reset and a hard reset, confirming the state of your files and commit history after each. Explain the difference and show evidence with screenshots.

---

## Summary

In this lab, you practiced advanced Git commands used in real-world development, cloud computing and DevOps workflows:
- `git stash` – save temporary work  
- `git reset` – undo commits locally (soft/hard)  
- `git commit --amend` – modify the latest commit  
- `git checkout <hash>` – revisit previous versions  
- `git revert` – safely undo commits remotely  
- `git push --force` – override remote history (use carefully)

---

## Submission
- Upload a **Word file and PDF or .md file** containing:
  - Step outputs or terminal screenshots for each task  
  - Your answers to the hands-on practical exam questions  
  - Your GitHub repository link showing relevant commits
