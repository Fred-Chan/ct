# 静态网站快速部署指南

> 本指南适用于纯静态 HTML/CSS/JS 项目的快速部署流程

## 📋 前置要求（仅首次）

```bash
# 检查工具是否已安装
which gh        # GitHub CLI
which vercel    # Vercel CLI

# 如未安装，执行：
brew install gh vercel

# 首次使用需要登录
gh auth login
vercel login
```

---

## ⚡ 全局命令（最快速）

如果你已经配置了全局部署工具，可以在**任何目录**使用以下命令：

### 一键部署命令

```bash
# 基本用法：仅部署
deploy <项目名>
deploy my-website

# 完整用法：部署并绑定域名
deploy <项目名> <域名>
deploy my-website example.com
deploy ct ct.itccc.app

# 完整命令名（与 deploy 相同）
deploy-static my-website subdomain.itccc.app
```

### 辅助命令

```bash
# 初始化配置文件（创建 .gitignore 和 vercel.json）
di
# 或
deployment-init

# 查看完整部署指南
dg
# 或
deployment-guide

# 复制部署脚本到当前目录
deployment-copy
```

### 使用示例

```bash
# 1. 进入项目目录
cd ~/my-new-project

# 2. 确保有 index.html
ls index.html

# 3. 一键部署（自动完成所有步骤）
deploy my-project mysite.com

# 完成！自动完成：
# ✓ Git 初始化
# ✓ 创建配置文件
# ✓ 提交代码
# ✓ 创建 GitHub 仓库
# ✓ 部署到 Vercel
# ✓ 绑定域名
```

---

## 🚀 标准部署流程（5步完成）

> 如果你没有配置全局命令，或想手动执行每一步，请按以下流程操作：

### 第1步：检查并初始化 Git 仓库

```bash
# 进入项目目录
cd /path/to/your/project

# 检查是否已在 git 仓库中
git status

# 如果提示不是 git 仓库，初始化一个新的
git init

# 如果已在父级 git 仓库中，需要在当前目录单独创建仓库
# （会覆盖父级的 git 配置）
git init
```

### 第2步：创建必要的配置文件

#### `.gitignore` - Git 忽略文件
```bash
cat > .gitignore << 'EOF'
.vercel
.DS_Store
node_modules/
EOF
```

#### `vercel.json` - Vercel 配置（可选但推荐）
```bash
cat > vercel.json << 'EOF'
{
  "routes": [
    {
      "src": "/(.*)",
      "dest": "/index.html"
    }
  ]
}
EOF
```

### 第3步：提交代码到本地 Git

```bash
git add .
git commit -m "feat: 初始化项目"
```

### 第4步：创建 GitHub 仓库并推送

```bash
# 创建 GitHub 仓库并推送（一条命令完成）
# 将 your-project-name 替换为你的项目名称
gh repo create your-project-name --public --source=. --remote=origin

# 推送代码
git push -u origin main
```

### 第5步：部署到 Vercel 并绑定域名

```bash
# 一条命令完成部署
vercel --prod --yes

# 添加自定义域名（如果有）
vercel domains add your-domain.com
```

---

## ⚡ 一键部署脚本（超快速）

将以下内容保存为 `deploy.sh`，然后修改变量后执行：

```bash
#!/bin/bash

# === 配置变量（修改这里） ===
PROJECT_NAME="your-project-name"
CUSTOM_DOMAIN="your-domain.com"  # 可选，如果不需要域名可以留空

# === 开始部署 ===
echo "🚀 开始部署项目: $PROJECT_NAME"

# 检查是否在 git 仓库中
if [ -d .git ]; then
    echo "✓ 已存在 Git 仓库"
else
    echo "→ 初始化 Git 仓库"
    git init
fi

# 创建配置文件（如果不存在）
if [ ! -f .gitignore ]; then
    echo "→ 创建 .gitignore"
    cat > .gitignore << 'EOF'
.vercel
.DS_Store
node_modules/
EOF
fi

if [ ! -f vercel.json ]; then
    echo "→ 创建 vercel.json"
    cat > vercel.json << 'EOF'
{
  "routes": [
    {
      "src": "/(.*)",
      "dest": "/index.html"
    }
  ]
}
EOF
fi

# 提交代码
echo "→ 提交代码到 Git"
git add .
git commit -m "feat: 部署项目" || echo "没有新的改动需要提交"

# 检查是否已有 origin remote
if git remote | grep -q origin; then
    echo "✓ 已存在 GitHub 远程仓库"
    git push -u origin main
else
    echo "→ 创建 GitHub 仓库"
    gh repo create $PROJECT_NAME --public --source=. --remote=origin
    git push -u origin main
fi

# 部署到 Vercel
echo "→ 部署到 Vercel"
vercel --prod --yes

# 添加自定义域名（如果配置了）
if [ -n "$CUSTOM_DOMAIN" ]; then
    echo "→ 添加自定义域名: $CUSTOM_DOMAIN"
    vercel domains add $CUSTOM_DOMAIN
fi

# 完成
echo ""
echo "✅ 部署完成！"
echo "📦 GitHub: https://github.com/$(gh api user -q .login)/$PROJECT_NAME"
if [ -n "$CUSTOM_DOMAIN" ]; then
    echo "🌐 访问: https://$CUSTOM_DOMAIN"
fi
```

使用方法：
```bash
chmod +x deploy.sh
./deploy.sh
```

---

## 🔄 更新已部署的网站

```bash
# 修改代码后
git add .
git commit -m "更新说明"
git push

# Vercel 会自动重新部署，无需手动操作！
```

---

## 🌐 域名配置说明

### 如果域名在 Vercel 管理

- ✅ **完全自动配置**，无需任何操作
- DNS 会自动指向 Vercel 的服务器

### 如果域名在其他服务商（如 Cloudflare）

需要在域名服务商处添加 DNS 记录：

| 类型 | 名称 | 值 | 代理状态 |
|------|------|-----|----------|
| CNAME | 子域名（如 www） | cname.vercel-dns.com | 开启（橙色云） |

或者使用 A 记录：

| 类型 | 名称 | 值 |
|------|------|-----|
| A | @ 或子域名 | 76.76.21.21 |

---

## 🎯 关键优化点

### ❌ 不需要的操作（浪费时间）
- ~~手动检查 DNS 是否生效~~（DNS 生效需要时间）
- ~~curl 测试访问~~（Vercel 部署成功即可访问）
- ~~多次运行 vercel domains inspect~~（添加后自动配置）

### ✅ 必要的检查（仅在出问题时）
```bash
# 检查域名 DNS 解析
dig your-domain.com +short

# 检查 HTTP 状态
curl -I https://your-domain.com

# 查看 Vercel 域名详情
vercel domains inspect your-domain.com
```

---

## 🔧 常见问题排查

### 问题1：git push 失败

```bash
# 检查远程仓库
git remote -v

# 如果没有 origin，添加远程仓库
git remote add origin https://github.com/username/repo.git

# 如果 URL 不对，更新 URL
git remote set-url origin https://github.com/username/repo.git
```

### 问题2：域名无法访问

```bash
# 1. 检查 DNS 是否生效（需要等待几分钟到几小时）
dig your-domain.com +short

# 2. 检查 Vercel 域名配置
vercel domains inspect your-domain.com

# 3. 检查部署状态
vercel ls
```

### 问题3：Vercel 部署失败

```bash
# 查看部署日志
vercel logs

# 重新部署
vercel --prod --force
```

---

## 📊 时间对比

| 方式 | 耗时 | 说明 |
|------|------|------|
| 手动逐步操作 | ~5-10分钟 | 需要多次命令和检查 |
| 使用本指南 | ~2分钟 | 按步骤执行命令 |
| 使用一键脚本 | ~30秒 | 修改变量后一键完成 |
| **使用全局命令** | **~20秒** | **最快速：`deploy my-project example.com`** |

---

## 💡 最佳实践

1. **使用有意义的提交信息**
   - ✅ `feat: 添加导航菜单`
   - ✅ `fix: 修复移动端样式问题`
   - ❌ `update`、`fix`

2. **保持项目结构清晰**
   ```
   project/
   ├── index.html        # 必须在根目录
   ├── css/
   ├── js/
   ├── images/
   ├── .gitignore
   └── vercel.json
   ```

3. **使用环境变量**（如果需要）
   - 在 Vercel 项目设置中添加环境变量
   - 不要将敏感信息提交到 Git

4. **定期备份**
   - GitHub 仓库本身就是备份
   - 重要项目可以在多个服务部署

---

## 📚 相关资源

- [GitHub CLI 文档](https://cli.github.com/manual/)
- [Vercel CLI 文档](https://vercel.com/docs/cli)
- [Git 基础教程](https://git-scm.com/book/zh/v2)

---

## 🔧 配置全局部署工具

如果你想在**任何目录**都能使用 `deploy` 命令，可以配置全局部署工具：

### 自动配置（推荐）

将部署脚本和配置添加到你的 shell 配置文件中：

```bash
# 1. 创建模板目录
mkdir -p ~/.deployment-templates

# 2. 复制部署脚本（从现有项目复制）
cp deploy-static.sh ~/.deployment-templates/
cp DEPLOYMENT.md ~/.deployment-templates/

# 3. 添加到 shell 配置（~/.zshrc 或 ~/.bashrc）
cat >> ~/.zshrc << 'EOF'

# =====================================================
# 静态网站部署工具
# =====================================================

# 快速部署静态网站到 GitHub + Vercel
deploy-static() {
    bash ~/.deployment-templates/deploy-static.sh "$@"
}

# 查看部署指南
deployment-guide() {
    if command -v bat &> /dev/null; then
        bat ~/.deployment-templates/DEPLOYMENT.md
    elif command -v less &> /dev/null; then
        less ~/.deployment-templates/DEPLOYMENT.md
    else
        cat ~/.deployment-templates/DEPLOYMENT.md
    fi
}

# 复制部署脚本到当前目录
deployment-copy() {
    cp ~/.deployment-templates/deploy-static.sh ./deploy.sh
    chmod +x ./deploy.sh
    echo "✓ 部署脚本已复制到当前目录: ./deploy.sh"
}

# 快速初始化部署配置文件
deployment-init() {
    echo "📝 创建部署配置文件..."

    # 创建 .gitignore
    if [ ! -f .gitignore ]; then
        cat > .gitignore << 'INNER_EOF'
.vercel
.DS_Store
node_modules/
INNER_EOF
        echo "✓ .gitignore 创建完成"
    else
        echo "→ .gitignore 已存在，跳过"
    fi

    # 创建 vercel.json
    if [ ! -f vercel.json ]; then
        cat > vercel.json << 'INNER_EOF'
{
  "routes": [
    {
      "src": "/(.*)",
      "dest": "/index.html"
    }
  ]
}
INNER_EOF
        echo "✓ vercel.json 创建完成"
    else
        echo "→ vercel.json 已存在，跳过"
    fi

    echo ""
    echo "✅ 配置文件初始化完成！"
}

# 别名
alias deploy='deploy-static'
alias dg='deployment-guide'
alias di='deployment-init'
EOF

# 4. 重新加载配置
source ~/.zshrc
```

### 验证安装

```bash
# 检查命令是否可用
type deploy
type dg
type di

# 查看帮助
deploy --help

# 查看部署指南
dg
```

### 使用全局命令

配置完成后，在任何目录都可以使用：

```bash
# 进入任何项目
cd ~/any/project

# 一键部署
deploy my-project example.com

# 初始化配置
di

# 查看指南
dg
```

---

## 📝 本项目部署记录

**项目名称**: ct (计算思维网页小游戏导航)

**部署信息**:
- GitHub 仓库: https://github.com/Fred-Chan/ct
- Vercel 生产环境: https://ct-kappa-drab.vercel.app
- 自定义域名: https://ct.itccc.app
- 部署时间: 2025-01-07

**更新历史**:
- 2025-01-07: 初始部署
