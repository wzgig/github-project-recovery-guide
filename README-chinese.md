```markdown
<a id="top"></a>

<h1 align="center">GitHub 项目恢复指南</h1>

<p align="center">
  重装系统后恢复 GitHub 项目关联、修复 Git 问题、解决合并冲突、替换远程仓库内容的实用指南
</p>

<p align="center">
  <img src="https://img.shields.io/badge/GitHub-指南-black?style=for-the-badge&logo=github" alt="GitHub Guide" />
  <img src="https://img.shields.io/badge/Git-教程-F05032?style=for-the-badge&logo=git&logoColor=white" alt="Git Tutorial" />
  <img src="https://img.shields.io/badge/README-友好-blue?style=for-the-badge" alt="README Friendly" />
</p>

---

## 📚 目录

- [🚀 1. 修复 Git 命令未找到](#fix-git-not-recognized)
- [🔧 2. 丢失 .git 文件夹的项目恢复](#recover-a-project-without-git)
- [⚠️ 3. 解决代码合并冲突](#resolve-merge-conflicts)
- [📁 4. 项目路径错误问题排查](#troubleshoot-wrong-project-path-issues)
- [🗂️ 5. 覆盖远程仓库所有内容](#replace-all-contents-in-a-remote-repository)
- [🖥️ 6. GitHub Desktop 配置远程仓库](#github-desktop-remote-setup)
- [💡 7. 重要注意事项与避坑](#important-tips-and-pitfalls)
- [✅ 推荐操作流程](#recommended-workflow)
- [📄 开源协议](#license)

---

## 🚀 1. 修复 Git 命令未找到 <a id="fix-git-not-recognized"></a>

### 问题

执行 `git` 命令时提示：

```bash
git: command not found
```

通常是 Git 未正确安装或系统环境变量未配置。

### 解决方法

1. 从官网安装 Git：
   [Git 下载（Windows）](https://git-scm.com/download/win)
2. 将以下路径添加到系统 `Path` 环境变量：
   - `C:\Program Files\Git\bin`
   - `C:\Program Files\Git\cmd`
3. 重启终端或 Git Bash，然后验证：

```bash
git --version
```

出现版本号即表示安装成功。

> [!TIP]
> 修改环境变量后，必须重启终端再测试。

------

## 🔧 2. 丢失 .git 文件夹的项目恢复 <a id="recover-a-project-without-git"></a>

### 场景

本地项目文件还在，但 `.git` 文件夹丢失。
执行 `git remote -v` 等命令时提示：

```bash
not a git repository
```

### 本方法效果

- 保留本地所有项目文件
- 重新关联远程 GitHub 仓库
- 重建 Git 版本跟踪
- **无法恢复本地原有的提交历史**

### 执行命令

> 将 `YOUR_REPOSITORY_URL` 替换为你的 GitHub 仓库地址。
> 远程分支如果是 `master`，就把命令里的 `main` 换成 `master`。

```bash
# 1. 初始化本地 Git 仓库
git init

# 2. 关联远程 GitHub 仓库
git remote add origin YOUR_REPOSITORY_URL

# 3. 查看远程是否配置成功
git remote -v

# 4. 添加所有文件
git add .

# 5. 本地提交
git commit -m "重装系统后恢复项目文件"

# 6. 拉取远程历史并允许无关历史合并
git pull origin main --allow-unrelated-histories

# 7. 推送到远程并建立分支关联
git push -u origin main
```

> [!WARNING]
> 该方法只能恢复 Git 关联，**无法找回原来的本地提交历史**。

------

## ⚠️ 3. 解决代码合并冲突 <a id="resolve-merge-conflicts"></a>

### 问题

拉取远程代码时可能出现：

```bash
CONFLICT (add/add): Merge conflict in README.md
```

### 解决方法

打开冲突文件，删除类似以下的冲突标记：

```text
<<<<<<<
=======
>>>>>>>
```

手动保留你需要的代码内容。

### 冲突解决后执行

```bash
# 添加已解决冲突的文件
git add README.md

# 提交合并结果
git commit -m "解决 README 合并冲突"

# 重新推送
git push -u origin main
```

> [!TIP]
> 删除冲突标记前，请仔细核对内容，尤其是 README 等说明文档。

------

## 📁 4. 项目路径错误问题排查 <a id="troubleshoot-wrong-project-path-issues"></a>

### 问题

Git 提示：

```bash
Everything up-to-date
```

但本地文件和 GitHub 仓库内容不一致。

### 可能原因

你当前操作的**不是正确的项目文件夹**。

### 查看当前路径

```bash
pwd
cd /path/to/the/correct/project
```

### 核对本地与远程提交记录

```bash
git rev-parse main
git rev-parse origin/main
```

两个提交 ID 一致，说明本地与远程已同步。

### 额外检查

- 刷新 GitHub 网页
- 重启编辑器/IDE
- 确认你编辑的是 Git 正在跟踪的文件夹

------

## 🗂️ 5. 覆盖远程仓库所有内容 <a id="replace-all-contents-in-a-remote-repository"></a>

有两种常用方式可以完全替换 GitHub 仓库内容。

### 方法 1：网页端手动删除（推荐新手）

1. 打开 GitHub 仓库页面
2. 手动删除所有旧文件
3. 提交删除操作
4. 推送本地新项目：

```bash
git add .
git commit -m "上传全新项目内容"
git push -u origin main
```

### 方法 2：命令行清空并覆盖

> 谨慎使用！确保你清楚会删除哪些内容。

```bash
# 1. 拉取最新远程代码
git pull origin main

# 2. 本地清空所有文件
rm -rf *

# 3. 提交清空并推送
git add .
git commit -m "清空仓库原有内容"
git push origin main

# 4. 添加新文件，提交并推送
git add .
git commit -m "上传全新项目内容"
git push -u origin main
```

### 重要说明

```bash
rm -rf *
```

**不会删除隐藏文件**，执行前请手动检查目录。

> [!WARNING]
> 使用 `rm -rf *` 务必确认当前目录，避免误删文件。

------

## 🖥️ 6. GitHub Desktop 配置远程仓库 <a id="github-desktop-remote-setup"></a>

如果你使用 GitHub Desktop：

1. 进入 **File → Add Local Repository**
2. 选择本地项目文件夹
3. 打开 **Repository → Repository Settings**
4. 在 **Remote** 栏填写 GitHub 仓库地址
5. 保存设置

------

## 💡 7. 重要注意事项与避坑 <a id="important-tips-and-pitfalls"></a>

### 1. 备份项目时务必保留 `.git` 文件夹

一旦 `.git` 丢失，代码文件还在，但本地完整 Git 历史会丢失。

### 2. 谨慎使用强制推送

```bash
git push -f origin main
```

会直接覆盖远程历史，极易丢失代码。

### 3. 重大操作前先备份仓库

清空或覆盖仓库内容前，先备份一份。

### 4. 注意分支名称

有的仓库默认是 `main`，有的是 `master`。

查看远程分支命令：

```bash
git branch -r
```

> [!TIP]
> 推送前务必确认远程默认分支名称。

------

## ✅ 推荐操作流程 <a id="recommended-workflow"></a>

本地文件还在，但丢失 `.git` 时，最安全流程：

```bash
git init
git remote add origin YOUR_REPOSITORY_URL
git add .
git commit -m "恢复项目文件"
git pull origin main --allow-unrelated-histories
git push -u origin main
```
