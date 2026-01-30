# 🚀 Cofea 博客部署配置清单

## 📋 你的配置信息

- **GitHub 用户名**: `Cofea`
- **仓库名**: `Cofea.github.io` (User Pages)
- **Notion Database ID**: `2f633ffb7fda80b595c1defc8e07ffcd`
- **自定义域名**: `gming.space`
- **www 子域名**: `www.gming.space`
- **最终访问地址**: `https://gming.space` 和 `https://www.gming.space`

---

## ✅ 第一步：GitHub 仓库创建和推送

### 1. 在 GitHub 创建仓库

1. 访问 https://github.com/new
2. **Repository name**: `Cofea.github.io` （⚠️ 必须是这个名字）
3. **Description**: "Personal blog powered by Notion and Next.js"
4. **Public** (必须是 public)
5. **不要**勾选 "Add a README file"
6. 点击 "Create repository"

### 2. 推送代码到 GitHub

在你的本地博客目录执行：

```bash
cd /g/workspaces/code/blog

# 添加远程仓库
git remote add origin https://github.com/Cofea/Cofea.github.io.git

# 推送代码
git branch -M main
git push -u origin main
```

预期输出：
```
Enumerating objects: xxx, done.
Counting objects: 100% (xxx/xxx), done.
...
To https://github.com/Cofea/Cofea.github.io.git
 * [new branch]      main -> main
```

---

## ✅ 第二步：配置 GitHub Secrets

### 1. 进入 Secrets 设置页面

访问：https://github.com/Cofea/Cofea.github.io/settings/secrets/actions

或者：仓库页面 → Settings → Secrets and variables → Actions

### 2. 添加 3 个 Secrets

点击 "New repository secret"，逐个添加：

#### Secret 1: NOTION_PAGE_ID
- **Name**: `NOTION_PAGE_ID`
- **Secret**: `2f633ffb7fda80b595c1defc8e07ffcd`
- 点击 "Add secret"

#### Secret 2: NOTION_TOKEN
- **Name**: `NOTION_TOKEN`
- **Secret**: `secret_` 开头的你的 Notion Integration Token
- 点击 "Add secret"

⚠️ **如何获取 NOTION_TOKEN**：
1. 访问 https://www.notion.so/my-integrations
2. 如果还没创建集成，点击 "+ New integration"
3. 填写信息：
   - Name: `Blog Sync` (或任意名称)
   - Associated workspace: 选择你的工作区
   - Capabilities: 勾选 `Read content`
4. 点击 "Submit"
5. 复制 "Internal Integration Token"（格式：`secret_xxx...`）

⚠️ **重要**：还需要在 Notion 数据库中共享给这个集成：
1. 打开你的 Notion 数据库页面
2. 点击右上角 `...` 菜单
3. 选择 `Add connections`
4. 找到并选择你的集成（如 `Blog Sync`）
5. 确认共享

#### Secret 2: NOTION_TOKEN
```
Name: NOTION_TOKEN
Secret: secret_你的Notion集成Token
```

**如何获取 Token**:
1. 访问：https://www.notion.so/my-integrations
2. 点击 "+ New integration"
3. Name: `Blog Sync`
4. Capabilities: 勾选 "Read content"
5. Submit 后复制 Token（格式：`secret_xxx...`）

**重要**：还需要共享数据库给集成：
- 打开 Notion 数据库
- 点击 `...` → `Add connections` → 选择 `Blog Sync`

### ⚠️ 关于 BASE_PATH

**不需要创建 `BASE_PATH` secret！**

因为你使用的是 User Pages（`Cofea.github.io`），BASE_PATH 应该为空。工作流会自动处理这个情况，不需要手动创建这个 secret。

### 3. 验证 Secrets

确认你看到 2 个 secrets：
- ✅ NOTION_PAGE_ID
- ✅ NOTION_TOKEN

---

## ✅ 第三步：启用 GitHub Pages（先不配置域名）

### 1. 进入 Pages 设置

访问：https://github.com/Cofea/Cofea.github.io/settings/pages

或者：仓库页面 → Settings → Pages

### 2. 配置 Source

在 "Build and deployment" 部分：
- **Source**: 选择 `GitHub Actions`（不是 "Deploy from a branch"）

### 3. 自定义域名

⚠️ **暂时不要填写**

先让博客部署成功，确认可以通过 `cofea.github.io` 访问后，再配置自定义域名。

在 "Custom domain" 部分：
- **留空，不填写任何内容**
- 点击 "Save"

---

## ✅ 第四步：首次部署

### 1. 触发 GitHub Actions

方式 1：手动触发（推荐）
1. 访问：https://github.com/Cofea/Cofea.github.io/actions
2. 左侧选择 "Deploy Blog to GitHub Pages"
3. 点击右上角 "Run workflow"
4. 点击绿色的 "Run workflow" 按钮

方式 2：推送代码触发（如果刚才推送了代码，可能已经自动触发）
- 任何推送到 `main` 分支的提交都会自动触发部署

### 2. 监控构建过程

1. 访问：https://github.com/Cofea/Cofea.github.io/actions
2. 点击最新的 workflow run
3. 查看构建日志
4. 等待所有步骤变成绿色 ✅

**预期时间**: 2-5 分钟

**可能遇到的错误**：
- ❌ `Error: Cannot read Notion database`: 检查 `NOTION_TOKEN` 和数据库共享设置
- ❌ `Error: Unable to find database`: 检查 `NOTION_PAGE_ID` 是否正确
- ❌ `Build failed`: 查看详细日志，可能是 npm 依赖安装问题

### 3. 验证部署成功

构建完成后，访问：**https://cofea.github.io/**

如果看到博客页面（即使是空的或示例内容），说明部署成功！🎉

---

## ✅ 第五步：配置 DNS（在你的域名提供商处）

⚠️ **重要**：确认上一步博客已经可以通过 https://cofea.github.io/ 访问后，再配置 DNS。

### 1. 找到你的 DNS 管理界面

根据你在哪里购买的 `gming.space`：
- **阿里云**: 控制台 → 云解析 DNS → 域名解析 → gming.space → 解析设置
- **腾讯云/DNSPod**: 控制台 → DNSPod → 我的域名 → gming.space
- **Cloudflare**: Dashboard → gming.space → DNS → Records
- **其他提供商**: 登录后找到 DNS 管理或域名解析

### 2. 添加 DNS 记录

删除现有的 `@` 和 `www` 记录（如果有），添加以下记录：

#### 方案 A：CNAME 记录（推荐，如果支持）

| 类型 | 主机记录/名称 | 记录值/目标 | TTL |
|------|--------------|------------|-----|
| CNAME | `@` | `cofea.github.io` | 600 |
| CNAME | `www` | `cofea.github.io` | 600 |

⚠️ **Cloudflare 用户注意**：必须关闭代理（点击云朵图标变成灰色），否则 GitHub Pages 无法验证。

⚠️ 如果提示"根域名不支持 CNAME"或"主域名不能使用 CNAME 记录"，使用方案 B。

#### 方案 B：A 记录 + CNAME（备选）

| 类型 | 主机记录/名称 | 记录值/目标 | TTL |
|------|--------------|------------|-----|
| A | `@` | `185.199.108.153` | 600 |
| A | `@` | `185.199.109.153` | 600 |
| A | `@` | `185.199.110.153` | 600 |
| A | `@` | `185.199.111.153` | 600 |
| CNAME | `www` | `cofea.github.io` | 600 |

💡 **提示**：如果 DNS 提供商不支持同一域名的多条 A 记录，只添加第一条也可以。

### 3. 保存 DNS 配置

点击 **保存** 或 **确认**，DNS 记录已添加成功。

---

## ✅ 第六步：验证 DNS 配置

### 1. 使用在线工具检查

访问：**https://dnschecker.org**

- 输入：`gming.space`
- 类型选择：
  - 如果用方案 A：选 `CNAME`
  - 如果用方案 B：选 `A`
- 点击 **Search**

**期望结果**：
- **CNAME**: 显示 `cofea.github.io`
- **A 记录**: 显示 `185.199.xxx.xxx` 中的一个

### 2. 等待 DNS 生效

**DNS 生效时间**：
- ⚡ 最快：10-30 分钟
- 🕐 一般：2-6 小时
- 🐌 最慢：24-48 小时

**为什么需要等待**：
- DNS 记录需要在全球的 DNS 服务器上传播
- 你的电脑、运营商、各地区 DNS 服务器都有缓存

**判断是否生效**：
- 在 https://dnschecker.org 上查看
- 如果**全球大部分地区**（绿色 ✓）都显示正确结果
- 说明 DNS 已经基本生效

### 3. 本地命令检查（可选）

```bash
# Windows PowerShell
nslookup gming.space

# 使用 Google DNS 检查
nslookup gming.space 8.8.8.8
```

---

## ✅ 第七步：添加自定义域名（DNS 生效后）

⚠️ **等待 DNS 在 dnschecker.org 上全球大部分地区生效后，再执行此步骤！**

### 1. 提交 CNAME 文件

本地已经准备好 CNAME 文件（内容：`gming.space`），提交并推送：

```bash
cd /g/workspaces/code/blog
git add public/CNAME
git commit -m "feat: Add CNAME for custom domain gming.space"
git push origin main
```

### 2. 在 GitHub Pages 设置中添加域名

1. 访问：https://github.com/Cofea/Cofea.github.io/settings/pages
2. 在 **Custom domain** 中输入：`gming.space`
3. 点击 **Save**
4. 等待 DNS 检查（几秒到几分钟）

**如果显示错误**：
- `DNS check unsuccessful` → DNS 还没完全生效，继续等待
- `Domain does not resolve` → 检查 DNS 配置是否正确

**如果显示成功**：
- ✅ DNS check successful
- 会显示 "Domain is correctly configured"

### 3. 启用 HTTPS

DNS 检查通过后：

1. 勾选 **Enforce HTTPS**
2. 等待 1-5 分钟让 GitHub 签发 SSL 证书
3. 证书签发后，HTTPS 自动生效

⚠️ **如果遇到 HTTPS 证书错误**：
- 取消勾选 "Enforce HTTPS"
- 等待 1 分钟
- 重新勾选 "Enforce HTTPS"

### 4. 验证访问

访问以下地址，都应该能正常访问：

- ✅ https://gming.space/
- ✅ https://www.gming.space/
- ✅ https://cofea.github.io/（会自动重定向到 gming.space）

---

## ✅ 第八步：在 Notion 创建第一篇文章

### 1. 打开 Notion 数据库

访问：https://www.notion.so/2f633ffb7fda80b595c1defc8e07ffcd

### 2. 创建测试文章

点击 "+ New"，填写：

| 字段 | 内容 | 说明 |
|------|------|------|
| Title | `欢迎访问我的博客` | 标题（必需） |
| Status | `Published` | 设为已发布（必需） |
| Date | 今天的日期 | 发布日期 |
| Tags | `测试` | 标签（可选） |
| Summary | `这是我的第一篇博客文章` | 摘要（可选） |

在页面正文中写一些内容，例如：
```
# 欢迎

这是我的第一篇博客文章！

## 关于这个博客

这个博客使用 Notion 作为 CMS，通过 GitHub Actions 自动部署到 GitHub Pages。
```

保存后，确认 `Status` 是 `Published`。

### 3. 触发更新

**方式 1**：手动触发（立即更新）
1. 访问：https://github.com/Cofea/Cofea.github.io/actions
2. "Deploy Blog to GitHub Pages" → "Run workflow"

**方式 2**：等待自动同步（每天 08:00 北京时间）
- 第二天早上自动同步

### 4. 验证文章显示

2-5 分钟后，刷新你的博客站点，应该能看到新文章！

---

## 📊 检查清单

在继续之前，确认以下所有项：

### GitHub 配置
- [ ] 仓库名是 `Cofea.github.io`
- [ ] 仓库是 Public
- [ ] 代码已推送到 main 分支
- [ ] 3 个 Secrets 已配置（NOTION_PAGE_ID, NOTION_TOKEN, BASE_PATH）
- [ ] GitHub Pages Source 设为 "GitHub Actions"
- [ ] Custom domain 设为 `gming.space`

### Notion 配置
- [ ] 创建了 Notion Integration
- [ ] 数据库已共享给 Integration
- [ ] Database ID 是 `2f633ffb7fda80b595c1defc8e07ffcd`

### DNS 配置
- [ ] 添加了 A 记录或 CNAME 记录指向 GitHub Pages
- [ ] 添加了 www CNAME 记录
- [ ] DNS 检查工具显示配置正确

### 部署验证
- [ ] GitHub Actions 构建成功
- [ ] https://cofea.github.io/ 可以访问
- [ ] https://gming.space/ 可以访问（DNS 生效后）
- [ ] HTTPS 证书正常（可能需要等待几分钟）

---

## 🔧 本地开发配置（可选）

如果想在本地开发和测试：

### 1. 创建本地环境变量

```bash
cd /g/workspaces/code/blog
cp .env.example .env.local
```

### 2. 编辑 .env.local

```env
NOTION_PAGE_ID=2f633ffb7fda80b595c1defc8e07ffcd
NOTION_TOKEN=secret_你的token
EXPORT=true
BASE_PATH=
NEXT_PUBLIC_LANG=zh-CN
NEXT_PUBLIC_LINK=https://gming.space
```

⚠️ 注意：`BASE_PATH=` 是空值（User Pages 不需要）

### 3. 安装依赖和运行

```bash
# 安装依赖
npm install

# 启动开发服务器
npm run dev
```

访问：http://localhost:3000

### 4. 测试构建

```bash
# Windows PowerShell
$env:EXPORT="true"
$env:BASE_PATH=""
npm run build

# 预览
npx serve out -p 3000
```

---

## 🎯 工作流程

部署完成后，你的日常工作流程：

```
1. 在 Notion 写文章
   ↓
2. 设置 Status = Published
   ↓
3. 等待自动同步 (每天 08:00)
   或手动触发 GitHub Actions
   ↓
4. 博客自动更新！
```

---

## 🐛 故障排查

### 问题 1: GitHub Actions 构建失败

**检查步骤**：
1. 查看 Actions 日志：https://github.com/Cofea/Cofea.github.io/actions
2. 常见错误：
   - `Cannot read Notion database`: 检查 Notion 数据库是否共享给 Integration
   - `Invalid token`: 检查 `NOTION_TOKEN` secret 是否正确
   - `Database not found`: 检查 `NOTION_PAGE_ID` secret 是否正确

### 问题 2: 自定义域名无法访问

**检查步骤**：
1. 确认 DNS 已配置：https://dnschecker.org
2. 检查 GitHub Pages 设置：https://github.com/Cofea/Cofea.github.io/settings/pages
3. 确认 `public/CNAME` 文件包含 `gming.space`
4. 等待 DNS 生效（最多 24 小时）

### 问题 3: HTTPS 证书错误

**解决方案**：
1. 确保 DNS 已正确配置并生效
2. 在 GitHub Pages 设置中：
   - 取消勾选 "Enforce HTTPS"
   - 等待 1 分钟
   - 重新勾选 "Enforce HTTPS"
3. 等待几分钟让 GitHub 签发证书

### 问题 4: 文章不显示

**检查步骤**：
1. 确认文章的 `Status` 是 `Published`
2. 确认数据库已共享给 Integration
3. 手动触发一次部署
4. 查看 Actions 日志是否有错误

---

## 📞 需要帮助？

- **NotionNext 文档**: https://docs.tangly1024.com/
- **GitHub Pages 文档**: https://docs.github.com/en/pages
- **Notion API 文档**: https://developers.notion.com/

---

**配置完成后，记得保存这个文档作为参考！** 📝
