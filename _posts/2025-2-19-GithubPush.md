---
layout: post
title: How to push a Jekyll site to GitHub and keep it updated
description: Github Push
tag: How to push a Jekyll site to GitHub and keep it updated
---

# How to Push a Jekyll Site to GitHub and Keep It Updated

In today's digital world, having a personal blog is a great way to share ideas and document your journey. **GitHub Pages** provides a **free** way to host static websites, and with **Jekyll**, you can easily build and deploy your own blog.

In this article, I will walk you through **how to push your Jekyll site to GitHub for the first time** and **how to update it whenever you make changes locally**.

---

## Part 1: Pushing Your Jekyll Site to GitHub for the First Time

Once you have built your Jekyll site locally, you need to upload it to **GitHub Pages** to make it publicly accessible. This involves **creating a GitHub repository, initializing Git, pushing your code, and enabling GitHub Pages**.

### Step 1: Create a GitHub Repository  

1. **Go to GitHub**: [https://github.com](https://github.com)  
2. **Click the `+` button in the top right corner, then select `New repository`**  
3. **Enter a repository name**:
   - If you want GitHub to host your site at `https://your-github-username.github.io/`, your **repository name must be** `your-github-username.github.io`.
   - If you choose another name (e.g., `my-blog`), your site will be hosted at `https://your-github-username.github.io/my-blog/`, and you’ll need additional configuration in `_config.yml`.  
4. **Set the repository to `Public` and DO NOT check `Initialize this repository with a README`**  
5. **Click `Create repository`**, and GitHub will generate an empty repository.  

---

### Step 2: Connect Your Local Jekyll Site to GitHub

1. **Open your terminal (Command Prompt, Git Bash, or VS Code Terminal)**  
2. **Navigate to your Jekyll site directory**:
   ```sh
   cd C:\Users\junha\mz-jhz.github.io
   ```
3. **Initialize Git (if you haven’t already)**:
   ```sh
   git init
   ```
4. **Add the GitHub repository as a remote origin**:
   ```sh
   git remote add origin https://github.com/MZ-JHZ/mz-jhz.github.io.git
   ```
5. **Verify that the remote repository was added correctly**:
   ```sh
   git remote -v
   ```
   Expected output:
   ```
   origin  https://github.com/MZ-JHZ/mz-jhz.github.io.git (fetch)
   origin  https://github.com/MZ-JHZ/mz-jhz.github.io.git (push)
   ```

---

### Step 3: Commit and Push Your Code to GitHub

1. **Add all files to Git**:
   ```sh
   git add .
   ```
2. **Commit the changes**:
   ```sh
   git commit -m "Initial commit"
   ```
3. **Push to GitHub**:
   ```sh
   git push -u origin master
   ```
   Or use:
   ```sh
   git push -u origin main
   ```
   If your default branch is `main`.

---

### Step 4: Enable GitHub Pages

1. **Go to your GitHub repository**
2. **Click `Settings`**
3. **Scroll down to `Pages` in the left menu**
4. **Under `Source`, select `master` (or `main`), then click `Save`**
5. **Wait a few minutes for GitHub Pages to deploy**
6. **Visit your site at**:
   ```
   https://mz-jhz.github.io/
   ```

🎉 **Congratulations! Your Jekyll site is now live on GitHub Pages!**

---

## Part 2: Updating Your Jekyll Site and Syncing with GitHub

### Step 1: Navigate to Your Local Repository
```sh
cd C:\Users\junha\mz-jhz.github.io
```

---

### Step 2: Check Which Files Have Changed
```sh
git status
```
For example, if you modified `_config.yml` and `index.html`, you will see:
```sh
modified: _config.yml
modified: index.html
```

---

### Step 3: Commit Your Changes

1. **Add all modified files**:
   ```sh
   git add .
   ```
2. **Commit with a meaningful message**:
   ```sh
   git commit -m "Updated site content"
   ```
3. **Push your changes to GitHub**:
   ```sh
   git push origin master
   ```
   Or use:
   ```sh
   git push origin main
   ```

---

### Step 4: Wait for GitHub Pages to Deploy
GitHub Pages usually **takes 1-5 minutes** to deploy changes. Visit `https://mz-jhz.github.io/` and refresh the page to see if the updates are live.

---

## Part 3: Troubleshooting Common Issues

### Issue 1: `fatal: unable to auto-detect email address`
If you get this error:
```sh
fatal: unable to auto-detect email address
```
You need to configure your Git identity:
```sh
git config --global user.name "MZ-JHZ"
git config --global user.email "your-github-email@example.com"
```
Then, try committing again:
```sh
git commit -m "Fix identity issue"
git push origin master
```

---

### Issue 2: `error: failed to push some refs`
If you see this error:
```sh
error: failed to push some refs
```
It means your GitHub repository has updates that your local repository doesn’t. Fix it by pulling the latest changes:
```sh
git pull origin master --rebase
```
Then push your updates:
```sh
git push origin master
```

---

### Issue 3: Updates Don’t Show Up on the Website
If your website is still showing the old version after updating:
- **Wait a few minutes** (GitHub Pages can take time to update)
- **Force refresh** by pressing `Ctrl + Shift + R`
- **Check GitHub Actions** to see if the site deployment failed
- **Verify `_config.yml` settings**:
  ```yaml
  future: true
  ```

---

## Part 4: Summary

| Task | Command |
|------|---------|
| **Initialize Git and Push for the First Time** | `git init` → `git remote add origin` → `git add .` → `git commit -m "Initial commit"` → `git push origin master` |
| **Update and Push Changes** | `git add .` → `git commit -m "Update"` → `git push origin master` |
| **Check Modified Files** | `git status` |
| **Pull Latest Changes Before Pushing** | `git pull origin master --rebase` |

By following this guide, you can **effortlessly maintain your Jekyll blog on GitHub Pages** and keep it up to date. 🚀
