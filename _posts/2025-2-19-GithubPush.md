---
layout: post
title: How to push a Jekyll site to GitHub and keep it updated
description: Github Push
tag: How to push a Jekyll site to GitHub and keep it updated
---

### **如何将 Jekyll 站点推送到 GitHub 并持续更新**

在这个数字化时代，拥有一个个人博客已经成为很多人的选择，而 **GitHub Pages** 提供了一种 **免费的方式** 来托管静态网站。结合 **Jekyll**，我们可以轻松搭建一个个人博客，并部署到 GitHub 上，让全世界的人都可以访问它。

在这篇文章中，我将详细记录 **如何将本地 Jekyll 站点推送到 GitHub**，以及 **如何在本地修改后同步到 GitHub**。希望这篇文章能帮助到正在搭建个人博客的你！🚀

------

## **一、将 Jekyll 站点首次推送到 GitHub**

当你在本地搭建好 Jekyll 站点后，需要将它上传到 GitHub 进行托管。这个过程包含 **创建 GitHub 仓库**、**初始化 Git**、**推送代码** 和 **启用 GitHub Pages**。

### **1. 创建 GitHub 仓库**

首先，我们需要在 GitHub 上创建一个仓库来存放 Jekyll 站点的代码。

1. **登录 GitHub**：https://github.com

2. **点击右上角 `+` 按钮，选择 `New repository`**

3. 输入仓库名称

   ：

   - 如果你希望 GitHub 直接托管在 `https://你的GitHub用户名.github.io/`，那么你的 **仓库名称必须是 `你的GitHub用户名.github.io`**。
   - 如果你选择其他名字（例如 `my-blog`），你的访问地址会变成 `https://你的GitHub用户名.github.io/你的仓库名/`。

4. **选择 `Public`（公开）**，**不要勾选 `Initialize this repository with a README`**

5. **点击 `Create repository`**，GitHub 会创建一个空的仓库。

------

### **2. 在本地终端连接 GitHub**

接下来，我们需要 **初始化 Git 并连接远程仓库**。

1. **打开终端（Windows Terminal / Git Bash / VS Code Terminal）**

2. 进入 Jekyll 站点所在的目录

   ：

   ```
   sh


   CopyEdit
   cd C:\Users\junha\mz-jhz.github.io
   ```

3. 初始化 Git（如果还没初始化）

   ：

   ```
   sh


   CopyEdit
   git init
   ```

4. 添加 GitHub 远程仓库

   ：

   ```
   sh


   CopyEdit
   git remote add origin https://github.com/MZ-JHZ/mz-jhz.github.io.git
   ```

5. 检查远程仓库是否正确添加

   ：

   ```
   sh


   CopyEdit
   git remote -v
   ```

   你应该看到类似的输出：

   ```
   bashCopyEditorigin  https://github.com/MZ-JHZ/mz-jhz.github.io.git (fetch)
   origin  https://github.com/MZ-JHZ/mz-jhz.github.io.git (push)
   ```

------

### **3. 提交并推送代码**

1. 添加所有文件到 Git

   ：

   ```
   sh


   CopyEdit
   git add .
   ```

2. 提交代码

   ：

   ```
   sh


   CopyEdit
   git commit -m "Initial commit"
   ```

3. 推送到 GitHub

   ：

   ```
   sh


   CopyEdit
   git push -u origin master  # 或者 `git push -u origin main`
   ```

------

### **4. 启用 GitHub Pages**

代码已经成功推送到 GitHub，接下来，我们需要 **启用 GitHub Pages** 让网站可以被访问。

1. **进入你的 GitHub 仓库**

2. **点击 `Settings`（设置）**

3. **在左侧菜单中找到 `Pages`**

4. **在 `Source` 里选择 `master`（或 `main`），然后点击 `Save`**

5. **等待几分钟后，GitHub Pages 会自动部署**

6. 访问你的网站

   ：

   ```
   arduino


   CopyEdit
   https://mz-jhz.github.io/
   ```

🎉 **恭喜，你的 Jekyll 站点已经成功部署到 GitHub Pages！**

------

## **二、如何更新 Jekyll 站点并同步到 GitHub**

当你在本地更新博客后，你需要将修改同步到 GitHub，让 GitHub Pages 自动更新你的站点。

### **1. 进入你的本地仓库**

```
sh


CopyEdit
cd C:\Users\junha\mz-jhz.github.io
```

确保你在正确的项目目录中。

------

### **2. 查看修改的文件**

在推送更新前，你可以查看有哪些文件被修改：

```
sh


CopyEdit
git status
```

如果你修改了 `_config.yml` 和 `index.html`，终端会显示：

```
makefileCopyEditmodified: _config.yml
modified: index.html
```

------

### **3. 提交更新**

1. 添加所有修改的文件

   ：

   ```
   sh


   CopyEdit
   git add .
   ```

2. 提交修改

   ：

   ```
   sh


   CopyEdit
   git commit -m "更新站点内容"
   ```

3. 推送到 GitHub

   ：

   ```
   sh


   CopyEdit
   git push origin master  # 或 `git push origin main`
   ```

------

### **4. 等待 GitHub Pages 自动部署**

GitHub Pages 通常会在 **1-5 分钟内** 自动部署更新。你可以访问 `https://mz-jhz.github.io/` 查看网站是否更新。

------

## **三、常见问题与解决方案**

### **1. `fatal: unable to auto-detect email address`**

如果在提交时出现：

```
vbnet


CopyEdit
fatal: unable to auto-detect email address
```

说明你的 Git **没有配置用户信息**，请执行：

```
shCopyEditgit config --global user.name "MZ-JHZ"
git config --global user.email "你的GitHub绑定邮箱"
```

然后再次提交：

```
shCopyEditgit commit -m "Update site"
git push origin master
```

------

### **2. `error: failed to push some refs`**

如果 `git push` 时报错：

```
vbnet


CopyEdit
error: failed to push some refs
```

原因是 **GitHub 上的代码比本地新**，你需要先拉取最新代码：

```
sh


CopyEdit
git pull origin master --rebase
```

然后再推送：

```
sh


CopyEdit
git push origin master
```

------

### **3. 网站更新后仍显示旧版本**

如果你更新了代码，但网站还是显示旧版本，可能的原因：

- **GitHub Pages 部署需要时间**（通常 1-5 分钟）。
- **浏览器缓存问题**，尝试按 `Ctrl + Shift + R` 强制刷新。
- **检查 GitHub Actions 是否有错误**（进入 `GitHub` → `Actions`）。
