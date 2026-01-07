# 部署指南

## GitHub 仓库

✅ GitHub仓库已创建成功！
- 仓库地址：https://github.com/Fred-Chan/binary-converter-game

## 推送代码到GitHub（手动操作）

如果代码还未推送，请在终端执行：

```bash
cd /Users/fredchan/Sites/binary-converter-game
git push -u origin main
```

## Vercel 部署步骤

### 方法一：通过Vercel网站部署（推荐）

1. **访问 Vercel**
   - 打开 https://vercel.com
   - 使用GitHub账号登录

2. **导入项目**
   - 点击 "Add New..." → "Project"
   - 选择 "Import Git Repository"
   - 找到并选择 `Fred-Chan/binary-converter-game` 仓库
   - 点击 "Import"

3. **配置项目**
   - **Project Name**: 保持默认或自定义
   - **Framework Preset**: 选择 "Other"（纯静态HTML项目）
   - **Root Directory**: `.` (默认)
   - **Build Command**: 留空（不需要构建）
   - **Output Directory**: `.` (默认)

4. **部署**
   - 点击 "Deploy"
   - 等待几秒钟
   - 部署完成后会得到一个URL，例如：`https://binary-converter-game.vercel.app`

### 方法二：通过Vercel CLI部署

```bash
# 安装Vercel CLI
npm install -g vercel

# 在项目目录中运行
cd /Users/fredchan/Sites/binary-converter-game
vercel

# 按照提示操作：
# - 登录Vercel账号
# - 设置项目配置
# - 确认部署

# 部署到生产环境
vercel --prod
```

### 方法三：一键部署按钮

在README.md中添加以下按钮，用户可以直接点击部署：

```markdown
[![Deploy with Vercel](https://vercel.com/button)](https://vercel.com/new/clone?repository-url=https://github.com/Fred-Chan/binary-converter-game)
```

## GitHub Pages 部署（备选方案）

### 步骤：

1. **访问仓库设置**
   - 打开 https://github.com/Fred-Chan/binary-converter-game
   - 点击 "Settings" 标签

2. **配置 GitHub Pages**
   - 在左侧菜单找到 "Pages"
   - 在 "Source" 下拉菜单中选择 "Deploy from a branch"
   - 选择 "main" 分支
   - 文件夹选择 "/ (root)"
   - 点击 "Save"

3. **访问网站**
   - 等待几分钟后，网站会部署完成
   - 访问地址：https://fred-chan.github.io/binary-converter-game/

## 自定义域名（可选）

### Vercel自定义域名：

1. 在Vercel项目设置中点击 "Domains"
2. 输入你的自定义域名
3. 按照提示配置DNS记录

### GitHub Pages自定义域名：

1. 在仓库根目录创建 `CNAME` 文件
2. 文件内容为你的域名，例如：`binary-game.example.com`
3. 在域名服务商处配置DNS记录指向GitHub Pages

## 持续部署

✅ **自动部署已配置**

- Vercel和GitHub Pages都支持自动部署
- 每次推送到main分支，网站会自动更新
- 无需手动重新部署

## 故障排查

### 如果Vercel部署失败：

1. 检查项目根目录是否有 `index.html`
2. 确认文件名是 `index.html` 而不是其他名称
3. 查看Vercel部署日志获取详细错误信息

### 如果GitHub Pages不显示：

1. 确认 `index.html` 在仓库根目录
2. 等待5-10分钟让GitHub Pages完成部署
3. 清除浏览器缓存后重试

## 性能优化建议

由于这是纯静态HTML项目，性能已经很好。可选优化：

1. **启用Vercel Analytics**
   - 在项目设置中启用分析功能
   - 追踪访问量和性能指标

2. **添加favicon**
   - 在根目录添加 `favicon.ico` 文件
   - 提升专业度

3. **添加meta标签**
   - Open Graph标签用于社交媒体分享
   - SEO优化标签

## 下一步

1. ✅ 代码已提交到本地Git仓库
2. ✅ GitHub仓库已创建
3. ⏳ 推送代码到GitHub（如遇网络问题，请手动执行）
4. ⏳ 访问 https://vercel.com 并按照上述步骤部署

祝你部署顺利！🎉
