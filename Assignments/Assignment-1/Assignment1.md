# Assignment: Git, Gitea, GitHub, LFS, and GitHub Pages

**Instructions:**  
- Complete all tasks as described below.  
- **Deadline:** The assignment must be submitted by **16 October 2025**.
- Submit your work on **Google Classroom** as well as on your GitHub repository named `cc_<studentname>_<rollnumber>`.
- Ensure all repositories are public unless specified otherwise.
- Submit required links/screenshots in your final submission.

---

## Task List

- [Task 1: Run Gitea in Codespace and Create an Initial Repo](#task-1-run-gitea-in-codespace-and-create-an-initial-repo)
- [Task 2: Mirror README.md from Gitea to GitHub](#task-2-mirror-readmemd-from-gitea-to-github)
- [Task 3: Use Git LFS for Large Files](#task-3-use-git-lfs-for-large-files)
- [Task 4: Create a Portfolio/CV with GitHub Pages](#task-4-create-a-portfoliocv-with-github-pages)
- [Submission Checklist](#submission-checklist)

---

## Task 1: Run Gitea in Codespace and Create an Initial Repo

1. **Set up Gitea:**
   - Run a Gitea server inside your Codespace.  
   - Use HTTPS for communication (SSH is not supported in Codespace).

2. **Create a Repository:**
   - Create a new repository on your Gitea server.
   - Add a `README.md` file **listing each student's name and roll number**.

3. **Add Remote Repo:**
   - Use the following command to add your Gitea repository as a remote:
     ```
     git remote add gitea <your_gitea_repo_https_url>
     ```
   - Push your initial commit containing the `README.md` to Gitea.

---

## Task 2: Mirror README.md from Gitea to GitHub

1. **Continue Working with Your Existing Repository:**
   - You will use the same repository that you created and pushed to your Gitea server in Task 1.

2. **Create GitHub Repository:**
   - Create a new GitHub repository named `assignment 1`.

3. **Add GitHub as a Second Remote:**
   - Add your GitHub repository as a remote to your local repository:
     ```
     git remote add github <your_github_repo_https_url>
     ```

4. **Push the README.md File to GitHub:**
   - Push the contents (including the `README.md`) from your local repository to GitHub.

5. **Verify Remotes:**
   - Run `git remote -v` and ensure both remotes (`gitea` and `github`) are listed.

---

## Task 3: Use Git LFS for Large Files

1. **Install Git LFS:**
   - Set up Git LFS in your local repository.

2. **Add Large Files:**
   - Add **three files larger than 100 MB** each to your repository.
   - Track them using Git LFS:
     ```
     git lfs track "*.ext"
     ```
     Replace `.ext` with the appropriate file extension.

3. **Reference in Assignment Repo:**
   - Commit and push these large files to your GitHub `assignment 1` repo.
   - Ensure the files are referenced correctly in your repository history.

---

## Task 4: Create a Portfolio/CV with GitHub Pages

1. **Create a new repository for GitHub Pages:**
   - Create a new repository named `<your-username>.github.io`.
   - Replace `<your-username>` with your actual GitHub username (e.g., `johnsmith.github.io`).

2. **Design Your Portfolio/CV:**
   - Create your portfolio or CV in HTML/CSS (or use a static site generator).

3. **Publish with GitHub Pages:**
   - Push your portfolio/CV files to the `<your-username>.github.io` repository.
   - Enable GitHub Pages in your repository settings if not automatically enabled.
   - Publish your site and share the link.

---

## Submission Checklist

- [ ] **Screenshot of your Gitea repository (showing README listing names & roll numbers)**
- [ ] GitHub assignment 1 repo link (with README and large files)
- [ ] Screenshot or output of `git remote -v` showing both remotes
- [ ] GitHub Pages link to your CV/portfolio

---

**Note:**  
You are encouraged to explore official documentation for each tool/platform.
