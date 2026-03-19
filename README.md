# Git 代码上传完整指南

## 📁 完整上传流程

### 步骤1：准备工作

```bash
# 配置用户名和邮箱（只需配置一次）
git config --global user.name "你的名字"
git config --global user.email "你的邮箱@example.com"
```

### 步骤2：初始化本地仓库

```bash
cd /path/to/your/project
git init
git status
```

### 步骤3：添加文件到暂存区

```bash
# 添加所有文件
git add .

# 或添加特定文件
git add filename1.py filename2.js
```

### 步骤4：提交到本地仓库

```bash
git commit -m "首次提交：项目初始化"
```

### 步骤5：连接 GitHub 远程仓库

```bash
# 在 GitHub 上创建新仓库（不要初始化 README）
git remote add origin https://github.com/用户名/仓库名.git

# 验证连接
git remote -v
```

### 步骤6：推送到 GitHub

```bash
# 第一次推送
git push -u origin main

# 后续推送
git push
```

---

## 🔧 常见错误及解决方案

### 错误1：`src refspec main does not match any`

本地是 `master` 分支，GitHub 默认是 `main`。

```bash
# 方案1：重命名分支
git branch -M main
git push -u origin main

# 方案2：直接推送 master
git push -u origin master
```

### 错误2：`fatal: remote origin already exists`

```bash
git remote -v              # 查看当前配置
git remote remove origin   # 移除旧配置
git remote add origin https://github.com/用户名/仓库名.git
```

### 错误3：`failed to push some refs`

远程有本地没有的更改，先拉取再推送。

```bash
git pull origin main --allow-unrelated-histories
git add .
git commit -m "合并远程更改"
git push -u origin main
```

### 错误4：认证失败

推荐改用 SSH，比 HTTPS 更稳定（尤其是 WSL2）。

```bash
# 1. 生成 SSH 密钥
ssh-keygen -t rsa -b 4096 -C "你的邮箱"

# 2. 将 ~/.ssh/id_rsa.pub 内容添加到 GitHub → Settings → SSH Keys

# 3. 更改远程地址为 SSH
git remote set-url origin git@github.com:用户名/仓库名.git
```

---

## 📋 一键上传脚本

创建 `gitpush.sh`：

```bash
#!/bin/bash
echo "=== Git 自动上传脚本 ==="

git add .

commit_msg="${1:-自动提交}"
git commit -m "$commit_msg"

if git push origin main; then
    echo "✅ 上传成功！"
else
    echo "❌ 上传失败，尝试设置上游..."
    git push --set-upstream origin main
fi
```

```bash
chmod +x gitpush.sh
./gitpush.sh "我的提交信息"
```

---

## 🎯 快捷命令别名

添加到 `~/.bashrc` 或 `~/.zshrc`：

```bash
alias gs='git status'
alias ga='git add .'
alias gcm='git commit -m'
alias gp='git push'
alias gpl='git pull'

# 一键上传：gu "提交信息"
gu() {
    git add .
    git commit -m "$1"
    git push origin main
}
```

---

## 📁 完整操作示例

```bash
cd ~/projects/myproject

# 初始化
git init

# 创建 .gitignore
cat > .gitignore << EOF
__pycache__/
*.pyc
.env
node_modules/
EOF

# 添加、提交、推送
git add .
git commit -m "初始化项目"
git remote add origin https://github.com/yourname/myproject.git
git branch -M main
git push -u origin main
```

---

## 💡 最佳实践

| 习惯 | 说明 |
|------|------|
| 提交前检查 | `git status` + `git diff` |
| 清晰的提交信息 | `修复登录页面样式` 而不是 `修复bug` |
| 小步提交 | 每次解决一个问题，避免大批量提交 |
| 使用分支 | 新功能开 `feature/xxx` 分支，完成后合并 |

```bash
git checkout -b feature/new-feature   # 创建并切换分支
git checkout main                      # 切回主分支
git merge feature/new-feature          # 合并分支
```

---

## 🆘 紧急恢复

```bash
# 备份当前代码
cp -r project/ project-backup/

# 重置到远程状态
git fetch origin
git reset --hard origin/main

# 重新提交
git add .
git commit -m "重新提交"
git push
```

---

## 💎 命令速查

```
init → add → commit → remote add → push
```

```bash
git config --global user.name "你的名字"
git config --global user.email "你的邮箱"
cd 项目目录
git init
git add .
git commit -m "提交说明"
git remote add origin https://github.com/用户名/仓库.git
git branch -M main
git push -u origin main
```

> 遇到具体错误可运行 `git push -v` 查看详细输出。
