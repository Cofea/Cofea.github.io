# Notion + Next.js + GitHub Pages 博客

基于 [NotionNext](https://github.com/tangly1024/NotionNext) 的 Notion 驱动博客，使用 GitHub Pages 部署，支持自动同步和手动触发更新。

> 本项目基于 NotionNext 开源项目配置，专门针对 GitHub Pages 静态部署优化。完整的 NotionNext 文档请访问：[NotionNext 帮助手册](https://docs.tangly1024.com/)

## ✨ 特性

- 📝 使用 Notion 作为 CMS，所见即所得编辑
- 🚀 自动部署到 GitHub Pages
- 🔄 每日自动同步 Notion 内容 (00:00 UTC / 08:00 北京时间)
- 🎯 支持手动触发立即更新
- 🎨 多主题支持 (NotionNext 内置)
- 🌏 中英文双语支持
- 📱 响应式设计

## 🚀 快速开始

### 第一步：Notion 配置

#### 1. 创建 Notion 集成

1. 访问 [Notion Integrations](https://www.notion.so/my-integrations)
2. 点击 "+ New integration"
3. 填写信息：
   - Name: `Blog Sync` (或任意名称)
   - Associated workspace: 选择你的工作区
   - Capabilities: 勾选 `Read content`
4. 提交后复制 **Internal Integration Token** (格式: `secret_xxx...`)

#### 2. 创建 Notion 数据库

在 Notion 中创建一个数据库，包含以下属性：

| 属性名 | 类型 | 说明 |
|--------|------|------|
| Title | 标题 | 文章标题 (必需) |
| Status | 选择 | 发布状态 (Published/Draft) |
| Date | 日期 | 发布日期 |
| Tags | 多选 | 文章标签 |
| Summary | 文本 | 文章摘要 |
| Slug | 文本 | URL路径 (可选) |
| Cover | 文件 | 封面图片 (可选) |

#### 3. 共享数据库给集成

1. 打开创建的数据库页面
2. 点击右上角 `...` 菜单
3. 选择 `Add connections`
4. 找到并选择你创建的集成 (如 `Blog Sync`)
5. 确认共享

#### 4. 获取数据库 ID

从数据库页面的 URL 中复制 Database ID：

```
https://www.notion.so/{workspace}/{database_id}?v=xxx
                                  ↑ 这部分就是 Database ID
```

Database ID 是一个 32 位字符串，例如：`097e5f674880459d8e1b4407758dc4fb`

### 第二步：GitHub 仓库配置

#### 1. 选择部署模式

GitHub Pages 有两种部署模式，请根据需求选择：

##### 方案 A：User Pages（推荐用于主站点）

适用于：作为个人主站点或配置自定义域名

- **仓库名**: `<你的用户名>.github.io`（例如：`alice.github.io`）
- **访问地址**: `https://<你的用户名>.github.io/`
- **BASE_PATH**: 留空（不设置或设为空字符串）
- **优点**: URL 简洁，适合自定义域名
- **限制**: 每个 GitHub 账号只能有一个

##### 方案 B：Project Pages

适用于：项目站点或多个独立博客

- **仓库名**: 任意名称（例如：`blog`, `my-website`）
- **访问地址**: `https://<你的用户名>.github.io/<仓库名>/`
- **BASE_PATH**: `/仓库名`（例如：`/blog`）
- **优点**: 可创建多个项目站点
- **缺点**: URL 带子路径

#### 2. 创建 GitHub 仓库

1. 访问 https://github.com/new
2. 根据上面选择的方案填写 **Repository name**
3. 设置为 **Public**（必须）
4. 不要勾选任何初始化选项
5. 点击 "Create repository"

⚠️ **如果仓库已存在**：
- 访问 `https://github.com/<你的用户名>/<仓库名>/settings`
- 滚动到底部 "Danger Zone"
- 点击 "Delete this repository" 删除旧仓库
- 然后重新创建

#### 3. 推送代码到 GitHub

```bash
# 检查并移除已有的 origin（如果有）
git remote remove origin

# 添加新的远程仓库（替换为你的仓库地址）
git remote add origin https://github.com/<你的用户名>/<仓库名>.git

# 推送代码
git branch -M main
git push -u origin main
```

#### 4. 配置 Repository Secrets

在 GitHub 仓库页面：

1. 进入 `Settings` → `Secrets and variables` → `Actions`
2. 点击 `New repository secret`，添加以下 secrets：

| Secret 名称 | 值 | 说明 |
|-------------|-----|------|
| `NOTION_PAGE_ID` | 你的数据库ID | 第一步获取的 Database ID（32位字符串） |
| `NOTION_TOKEN` | `secret_xxx...` | 第一步获取的 Integration Token |
| `BASE_PATH` | 见下方说明 | 根据部署模式设置（可选） |

**BASE_PATH 配置规则**：

| 部署模式 | BASE_PATH 设置 | 说明 |
|---------|---------------|------|
| User Pages (`username.github.io`) | **不创建此 secret** | 工作流会自动使用空值 |
| Project Pages (如 `blog`) | `/blog` | 必须以 `/` 开头，值为仓库名 |
| 自定义域名 | **不创建此 secret** | 工作流会自动使用空值 |

⚠️ **重要**：
- GitHub Secrets 不允许创建空值
- 对于 User Pages 和自定义域名，**直接不创建** `BASE_PATH` secret 即可
- 只有 Project Pages 才需要创建 `BASE_PATH` secret

#### 5. 启用 GitHub Pages

1. 进入 `Settings` → `Pages`
2. 在 `Source` 下选择：**GitHub Actions**
3. 保存设置

#### 6. 配置自定义域名（可选）

如果要使用自定义域名（如 `example.com`）：

1. 在 GitHub Pages 设置中，填入你的域名
2. 在域名提供商的 DNS 管理中添加记录：
   - CNAME 记录：`www` → `<你的用户名>.github.io`
   - A 记录（根域名）：指向 GitHub Pages IP（见下方常见问题）
3. 等待 DNS 生效（最多 24 小时）
4. 勾选 "Enforce HTTPS"

**注意**: 本项目已包含 `public/CNAME` 文件配置，如果不使用自定义域名，请删除此文件。

### 第三步：首次部署

#### 方式 1：手动触发 (推荐)

1. 进入 GitHub 仓库的 `Actions` 标签
2. 选择 `Deploy Blog to GitHub Pages` 工作流
3. 点击 `Run workflow` → `Run workflow`
4. 等待构建完成 (约 2-5 分钟)

#### 方式 2：推送代码触发

任何推送到 `main` 分支的提交都会自动触发部署。

### 第四步：验证部署

构建完成后，根据你的部署模式访问：

**User Pages**:
```
https://<你的用户名>.github.io/
```

**Project Pages**:
```
https://<你的用户名>.github.io/<仓库名>/
```

**自定义域名**:
```
https://your-domain.com/
```

示例：
- User Pages: `https://alice.github.io/`
- Project Pages: `https://alice.github.io/blog/`
- 自定义域名: `https://example.com/`

## 📝 使用指南

### 发布新文章

1. 在 Notion 数据库中创建新页面
2. 填写标题、内容、标签等信息
3. 将 `Status` 设置为 `Published`
4. 等待自动同步 (每天 00:00 UTC) 或手动触发部署

### 手动触发更新

如果你想立即发布新内容而不等待自动同步：

1. GitHub 仓库 → `Actions` 标签
2. 选择 `Deploy Blog to GitHub Pages`
3. 点击 `Run workflow` → `Run workflow`

### 修改配置

#### 本地开发环境变量

创建 `.env.local` 文件 (不会提交到 Git)：

```bash
cp .env.example .env.local
```

编辑 `.env.local`，填入真实值：

```env
NOTION_PAGE_ID=your_database_id_here
NOTION_TOKEN=secret_your_token_here
EXPORT=true
BASE_PATH=
NEXT_PUBLIC_LANG=zh-CN
```

⚠️ **BASE_PATH 配置**：
- User Pages 或自定义域名：留空（`BASE_PATH=`）
- Project Pages：填写 `/仓库名`（如 `BASE_PATH=/blog`）

#### 博客站点配置

编辑 `blog.config.js` 修改博客标题、作者、主题等：

```javascript
const BLOG = {
  TITLE: '我的博客',
  AUTHOR: '你的名字',
  LANG: 'zh-CN',
  THEME: 'hexo', // 可选主题: medium, hexo, next, simple 等
  // ... 更多配置
}
```

完整配置说明见 [NotionNext 文档](https://docs.tangly1024.com/)

## 🔧 本地开发

### 安装依赖

```bash
npm install
```

### 启动开发服务器

```bash
npm run dev
```

访问 `http://localhost:3000`

### 生产构建测试

```bash
# Linux / Mac
EXPORT=true BASE_PATH= npm run build

# Windows PowerShell
$env:EXPORT="true"
$env:BASE_PATH=""
npm run build

# Windows CMD
set EXPORT=true
set BASE_PATH=
npm run build

# 或使用 cross-env (需要先安装: npm install -D cross-env)
npx cross-env EXPORT=true BASE_PATH= npm run build
```

⚠️ **注意**：如果使用 Project Pages，将 `BASE_PATH=` 改为 `BASE_PATH=/仓库名`

生成的静态文件在 `out/` 目录

### 本地预览构建结果

```bash
npx serve out -p 3000
```

## 🕐 自动同步说明

### 定时任务

- **触发时间**: 每天 00:00 UTC (北京时间 08:00)
- **工作流文件**: `.github/workflows/deploy.yml`

### Keep-Alive 机制

GitHub Actions 会在 60 天无活动后禁用定时任务。本项目包含 `keep-alive.yml` 工作流，每月运行一次以保持活跃。

如果定时任务被禁用，你需要：

1. 进入 `Actions` 标签
2. 找到被禁用的工作流
3. 点击 `Enable workflow`

## 🎨 主题切换

NotionNext 支持多种主题，编辑 `blog.config.js`：

```javascript
const BLOG = {
  THEME: 'hexo', // 可选: medium, hexo, next, simple, nobelium 等
  // ...
}
```

完整主题列表和预览见 [NotionNext 主题文档](https://docs.tangly1024.com/theme)

## 📚 进阶配置

### 自定义域名

⚠️ **推荐流程**：先完成基本部署，确认博客可以通过 `username.github.io` 访问后，再配置自定义域名。

#### 完整配置流程

##### 第一步：先部署到默认域名

1. **不要**在 GitHub Pages 设置中填写自定义域名
2. 完成基本部署，确认 `https://username.github.io` 可以访问
3. 确认博客内容正常显示

##### 第二步：配置 DNS 记录

在你的域名提供商（如阿里云、腾讯云、Cloudflare）的 DNS 管理中添加记录。

**方案 A：CNAME 记录（推荐）**

| 类型 | 名称/主机 | 值/目标 | TTL |
|------|-----------|---------|-----|
| CNAME | `@` 或根域名 | `<你的用户名>.github.io` | 600 |
| CNAME | `www` | `<你的用户名>.github.io` | 600 |

⚠️ **Cloudflare 用户注意**：
- 必须关闭代理（点击云朵图标变成灰色）
- 否则 GitHub Pages 无法验证域名

⚠️ **如果提示"根域名不支持 CNAME"**：使用方案 B

**方案 B：A 记录 + CNAME（备选）**

如果你的 DNS 提供商不支持根域名 CNAME：

| 类型 | 名称/主机 | 值/目标 | TTL |
|------|-----------|---------|-----|
| A | `@` | `185.199.108.153` | 600 |
| A | `@` | `185.199.109.153` | 600 |
| A | `@` | `185.199.110.153` | 600 |
| A | `@` | `185.199.111.153` | 600 |
| CNAME | `www` | `<你的用户名>.github.io` | 600 |

⚠️ **注意**：这 4 个 IP 是 GitHub Pages 的官方 IP 地址。如果提供商不支持多条 A 记录，只添加第一条也可以。

##### 第三步：验证 DNS 配置

**使用在线工具检查**：

1. 访问：https://dnschecker.org
2. 输入你的域名（如 `example.com`）
3. 类型选择：
   - 方案 A：选 `CNAME`
   - 方案 B：选 `A`
4. 查看全球各地的解析结果

**期望结果**：
- **CNAME**: 应该显示 `<你的用户名>.github.io`
- **A 记录**: 应该显示 GitHub Pages 的 IP 地址之一

**DNS 生效时间**：
- ⚡ 最快：10-30 分钟
- 🕐 一般：2-6 小时
- 🐌 最慢：24-48 小时

**判断是否生效**：
- 在 dnschecker.org 上，如果全球**大部分地区**（绿色 ✓）都显示正确结果
- 说明 DNS 已经基本生效，可以进行下一步

**本地验证命令**（可选）：

```bash
# Windows PowerShell
nslookup example.com

# 使用 Google DNS 检查
nslookup example.com 8.8.8.8

# Linux/Mac
dig example.com
```

##### 第四步：创建 CNAME 文件

⚠️ **等待 DNS 全球生效后**，再执行此步骤！

在仓库根目录创建 `public/CNAME` 文件：

```bash
echo "example.com" > public/CNAME
```

或者手动创建文件，内容为：
```
example.com
```

⚠️ **注意**：
- 不要包含 `https://` 或 `www.`
- 只写域名本身，如 `example.com`
- 文件名必须是大写的 `CNAME`

提交并推送：
```bash
git add public/CNAME
git commit -m "feat: Add custom domain"
git push origin main
```

##### 第五步：在 GitHub Pages 设置中添加域名

⚠️ **确认 DNS 已在 dnschecker.org 上全球生效后再操作！**

1. 访问仓库的 `Settings` → `Pages`
2. 在 **Custom domain** 中输入你的域名（如 `example.com`）
3. 点击 **Save**
4. 等待 DNS 检查（几秒到几分钟）

**可能的结果**：

✅ **成功**：显示 "DNS check successful"
- 继续下一步启用 HTTPS

❌ **失败**：显示 "DNS check unsuccessful" 或 "NotServedByPagesError"
- DNS 还没完全生效，继续等待
- 使用 dnschecker.org 检查更多地区是否生效
- 稍后再试

##### 第六步：启用 HTTPS

DNS 检查成功后：

1. 勾选 **Enforce HTTPS**
2. 等待 1-5 分钟让 GitHub 自动签发 SSL 证书
3. 证书签发完成后，HTTPS 自动生效

⚠️ **如果遇到 HTTPS 证书错误**：
1. 取消勾选 "Enforce HTTPS"，保存
2. 等待 1 分钟
3. 重新勾选 "Enforce HTTPS"，保存
4. 等待 GitHub 重新签发证书

##### 第七步：验证访问

所有配置完成后，以下地址都应该能访问：

- ✅ `https://example.com/`
- ✅ `https://www.example.com/`
- ✅ `https://username.github.io/`（会自动重定向到自定义域名）

#### 故障排查

**问题 1：DNS check unsuccessful**
- **原因**：DNS 还没生效或配置错误
- **解决**：
  - 在 dnschecker.org 检查 DNS 是否全球生效
  - 确认 DNS 记录配置正确
  - 耐心等待，最多可能需要 24-48 小时

**问题 2：HTTPS 证书错误**
- **原因**：GitHub 还没签发证书或证书有问题
- **解决**：
  - 确保 DNS 已完全生效
  - 取消勾选 "Enforce HTTPS"，等待 1 分钟后重新勾选
  - 等待 5-10 分钟让证书签发完成

**问题 3：访问自定义域名显示 404**
- **原因**：CNAME 文件缺失或内容错误
- **解决**：
  - 检查 `public/CNAME` 文件是否存在
  - 确认文件内容只包含域名（无 http:// 前缀）
  - 重新提交并推送代码

**问题 4：Cloudflare 用户无法验证**
- **原因**：Cloudflare 代理干扰验证
- **解决**：
  - 在 Cloudflare DNS 记录中，点击云朵图标
  - 将橙色云朵（代理开启）改为灰色云朵（DNS only）
  - 保存后等待验证

#### 最佳实践

1. **先测试再上线**：
   - 先用 GitHub Pages 默认域名测试
   - 确认一切正常后再配置自定义域名

2. **耐心等待 DNS**：
   - DNS 传播需要时间，不要着急
   - 使用 dnschecker.org 监控进度

3. **保留默认域名访问**：
   - 配置自定义域名后，默认域名会自动重定向
   - 但仍然保持可用性，方便调试

### 添加评论系统

NotionNext 支持多种评论系统，编辑 `.env.local` 或 GitHub Secrets：

```env
# Giscus (推荐 - GitHub Discussions)
NEXT_PUBLIC_COMMENT_GISCUS_REPO=username/repo
NEXT_PUBLIC_COMMENT_GISCUS_REPO_ID=your_repo_id
NEXT_PUBLIC_COMMENT_GISCUS_CATEGORY_ID=your_category_id
```

配置方法见 [Giscus 官网](https://giscus.app/)

### 添加 Google Analytics

编辑 `.env.local` 或 GitHub Secrets：

```env
NEXT_PUBLIC_ANALYTICS_GOOGLE_ID=G-XXXXXXXXXX
```

## 🐛 常见问题

### 1. 部署后看到 404 页面

**原因**: `BASE_PATH` 配置不正确

**解决方案**:
- **User Pages** (`username.github.io`): 确保 `BASE_PATH` 为空
- **Project Pages**: 确保 `BASE_PATH=/仓库名`（例如：`/blog`）
- **自定义域名**: 确保 `BASE_PATH` 为空
- 检查 GitHub Secrets 配置是否正确
- 清空浏览器缓存后重试

### 2. 仓库已存在错误

**错误信息**: `repository already exists in this account`

**解决方案**:
1. 访问 `https://github.com/<你的用户名>/<仓库名>/settings`
2. 滚动到页面底部 "Danger Zone"
3. 点击 "Delete this repository"
4. 输入仓库完整名称确认删除
5. 重新创建仓库并推送代码

### 3. 图片无法显示

**原因**: 静态导出时图片优化被禁用或 Notion 图片权限问题

**解决方案**:
- 已配置 `unoptimized: true`，应该能正常显示
- 确认 Notion 数据库和图片是公开访问的
- 推荐使用 Unsplash、Cloudinary 等公共图床
- 避免使用 Notion 内部图片链接（需要登录才能访问）

### 4. 样式或资源 404

**原因**: `basePath` 和 `assetPrefix` 配置问题

**检查步骤**:
1. 确认 `.env.local` 或 GitHub Secrets 中 `BASE_PATH` 正确
2. 确认 `next.config.js` 中的配置与部署模式匹配
3. 清空构建缓存重新构建：
   ```bash
   rm -rf .next out
   npm run build
   ```
4. 检查浏览器控制台的网络请求，查看资源路径是否正确

### 5. 构建超时或失败

**原因**: 文章数量太多、依赖安装慢或 Notion API 限流

**解决方案**:
- 使用 `npm ci` 而不是 `npm install`（工作流已配置）
- 检查 Notion API 是否限流（每秒最多 3 次请求）
- 在 `.github/workflows/deploy.yml` 中增加 `timeout-minutes: 30`
- 减少单次构建的文章数量
- 检查 Actions 日志了解具体错误

### 6. 自动同步未触发

**检查步骤**:
1. GitHub Actions 是否被禁用：
   - `Settings` → `Actions` → `General`
   - 确保 "Allow all actions and reusable workflows" 已启用
2. 工作流是否被禁用：
   - 访问 `Actions` 标签查看工作流状态
   - 点击被禁用的工作流，点击 "Enable workflow"
3. Secrets 是否正确配置：
   - 检查 `NOTION_PAGE_ID` 和 `NOTION_TOKEN` 是否填写
4. 查看 Actions 日志了解错误详情

### 7. Notion 数据库无法访问

**错误信息**: `Error: Could not find database` 或 `Error: Unauthorized`

**解决方案**:
1. 确认 Notion Integration 已创建
2. 确认数据库已共享给 Integration：
   - 打开 Notion 数据库
   - 点击右上角 `...` → `Add connections`
   - 选择你的 Integration
3. 确认 `NOTION_PAGE_ID` 是数据库 ID（不是页面 ID）
4. 确认 `NOTION_TOKEN` 以 `secret_` 开头

### 8. DNS 配置后自定义域名无法访问

**解决方案**:
1. 使用 https://dnschecker.org 检查 DNS 是否全球生效
2. 等待 DNS 传播（最多 24-48 小时）
3. 确认 `public/CNAME` 文件内容正确（只包含域名）
4. 确认 GitHub Pages 设置中已填入自定义域名
5. 尝试清空浏览器 DNS 缓存：
   - Windows: `ipconfig /flushdns`
   - Mac: `sudo dscacheutil -flushcache`
   - Linux: `sudo systemd-resolve --flush-caches`

### 9. HTTPS 证书错误

**解决方案**:
1. 确保 DNS 已正确配置并完全生效
2. 在 GitHub Pages 设置中：
   - 取消勾选 "Enforce HTTPS"
   - 保存并等待 1 分钟
   - 重新勾选 "Enforce HTTPS"
3. 等待几分钟让 GitHub 重新签发证书
4. 如果仍有问题，删除自定义域名配置，等待 1 小时后重新添加

### 10. GitHub Pages 显示 "DNS check unsuccessful"

**错误信息**: `Domain does not resolve to the GitHub Pages server (NotServedByPagesError)`

**原因**: DNS 还没有配置或还没有完全生效

**正确的配置顺序**:
1. **先不填写**自定义域名，完成基本部署
2. 确认 `https://username.github.io` 可以正常访问
3. 在域名提供商处配置 DNS 记录
4. 使用 https://dnschecker.org 验证 DNS 全球生效
5. **等待 DNS 全球大部分地区生效后**，再在 GitHub Pages 设置中添加自定义域名

**解决方案**:
1. 如果已经填写了自定义域名，先删除它
2. 删除本地的 `public/CNAME` 文件并推送
3. 确认博客可以通过 `username.github.io` 访问
4. 去域名提供商处配置 DNS（见上方"自定义域名"章节）
5. 在 dnschecker.org 上验证 DNS 生效
6. **等待全球大部分地区显示正确结果后**，重新添加自定义域名

**耐心等待**:
- DNS 传播需要时间：10 分钟 - 48 小时
- 不要在 DNS 还没生效时就添加自定义域名
- 使用 dnschecker.org 实时监控 DNS 状态

## 📖 参考资源

- [NotionNext 官方文档](https://docs.tangly1024.com/)
- [Notion API 文档](https://developers.notion.com/)
- [Next.js 静态导出](https://nextjs.org/docs/pages/building-your-application/deploying/static-exports)
- [GitHub Actions 文档](https://docs.github.com/en/actions)
- [GitHub Pages 文档](https://docs.github.com/en/pages)

## 🙏 致谢

本项目基于以下优秀的开源项目：

- [NotionNext](https://github.com/tangly1024/NotionNext) - 核心博客框架
- [Next.js](https://nextjs.org/) - React 框架
- [Tailwind CSS](https://tailwindcss.com/) - CSS 框架
- [Notion](https://www.notion.so/) - 内容管理系统

特别感谢 NotionNext 的作者 [tangly1024](https://github.com/tangly1024) 和所有贡献者！

## 📄 License

本项目继承 NotionNext 的 MIT License。详见原项目 [LICENSE](https://github.com/tangly1024/NotionNext/blob/main/LICENSE)

---

**Star NotionNext**: 如果这个项目对你有帮助，请给 [NotionNext](https://github.com/tangly1024/NotionNext) 点个 ⭐️
