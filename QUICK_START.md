# 🚀 快速部署指南

## 📌 你的配置信息

- **GitHub 用户名**: Cofea
- **仓库类型**: User Pages (推荐)
- **仓库名**: `Cofea.github.io`
- **Database ID**: `2f633ffb7fda80b595c1defc8e07ffcd`
- **域名**: `gming.space`
- **BASE_PATH**: 空（留空）

---

## ✅ 第一步：删除旧仓库（如果存在）

1. 访问：https://github.com/Cofea/Cofea.github.io/settings
2. 滚动到底部 "Danger Zone"
3. 点击 "Delete this repository"
4. 输入完整仓库名：`Cofea/Cofea.github.io`
5. 确认删除

---

## ✅ 第二步：创建新仓库并推送

### 1. 在 GitHub 创建仓库

1. 访问：https://github.com/new
2. **Repository name**: `Cofea.github.io`
3. **Public** ✓
4. 不勾选任何选项
5. Create repository

### 2. 推送代码

```bash
cd /g/workspaces/code/blog

# 移除旧的 origin（如果有）
git remote remove origin

# 添加新的 origin
git remote add origin https://github.com/Cofea/Cofea.github.io.git

# 推送
git push -u origin main
```

---

## ✅ 第三步：配置 GitHub Secrets

访问：https://github.com/Cofea/Cofea.github.io/settings/secrets/actions

点击 "New repository secret"，添加 **2 个 secrets**：

### Secret 1: NOTION_PAGE_ID
```
Name: NOTION_PAGE_ID
Secret: 2f633ffb7fda80b595c1defc8e07ffcd
```

### Secret 2: NOTION_TOKEN
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

如果你已经创建了 `BASE_PATH`，可以删除它：
1. 进入 Secrets 页面
2. 找到 `BASE_PATH`
3. 点击右侧的 "Remove" 按钮

---

## ✅ 第四步：启用 GitHub Pages

1. 访问：https://github.com/Cofea/Cofea.github.io/settings/pages
2. **Source**: 选择 `GitHub Actions`
3. ⚠️ **Custom domain**: **暂时不要填写**（先让博客部署成功）
4. 保存

---

## ✅ 第五步：首次部署（使用默认域名）

### 1. 手动触发部署

1. 访问：https://github.com/Cofea/Cofea.github.io/actions
2. 选择 "Deploy Blog to GitHub Pages"
3. 点击 "Run workflow" → "Run workflow"
4. 等待 2-5 分钟，看到绿色 ✓

### 2. 验证博客可访问

访问：**https://cofea.github.io/**

确认博客正常显示后，再进行下一步的 DNS 配置。

---

## ✅ 第六步：配置 DNS（在域名提供商处）

⚠️ **重要**：确认上一步博客已经可以通过 https://cofea.github.io/ 访问后，再配置 DNS。

### 1. 找到 DNS 管理界面

根据你在哪里购买的 `gming.space`：

#### 阿里云
1. 登录阿里云控制台
2. 进入 **云解析 DNS** → **域名解析**
3. 找到 `gming.space`，点击 **解析设置**

#### 腾讯云/DNSPod
1. 登录腾讯云控制台
2. 进入 **DNSPod** → **我的域名**
3. 找到 `gming.space`，点击进入

#### Cloudflare
1. 登录 Cloudflare Dashboard
2. 选择 `gming.space`
3. 进入 **DNS** → **Records**

### 2. 添加 DNS 记录

**方案 A：CNAME（推荐）**

删除现有的 `@` 和 `www` 记录，添加：

| 类型 | 主机记录/名称 | 记录值/目标 | TTL |
|------|-------------|-----------|-----|
| CNAME | @ | cofea.github.io | 600 |
| CNAME | www | cofea.github.io | 600 |

⚠️ **Cloudflare 用户注意**：必须关闭代理（点击云朵图标变成灰色），否则 GitHub Pages 无法验证。

⚠️ 如果提示"根域名不支持 CNAME"或"主域名不能使用 CNAME 记录"，使用方案 B。

**方案 B：A 记录 + CNAME（备选）**

| 类型 | 主机记录/名称 | 记录值/目标 | TTL |
|------|-------------|-----------|-----|
| A | @ | 185.199.108.153 | 600 |
| A | @ | 185.199.109.153 | 600 |
| A | @ | 185.199.110.153 | 600 |
| A | @ | 185.199.111.153 | 600 |
| CNAME | www | cofea.github.io | 600 |

💡 **提示**：如果 DNS 提供商不支持同一域名的多条 A 记录，只添加第一条也可以。

### 3. 保存 DNS 配置

点击 **保存** 或 **确认**，DNS 记录已添加成功。

---

## ✅ 第七步：验证 DNS 配置

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

## ✅ 第八步：添加自定义域名（DNS 生效后）

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

## ✅ 第九步：验证部署

访问以下地址：

- **GitHub Pages**: https://cofea.github.io/
- **自定义域名**（DNS 生效后）: https://gming.space/

DNS 生效时间：10 分钟 - 24 小时

检查 DNS：https://dnschecker.org

---

## 📝 发布第一篇文章

1. 打开 Notion 数据库：https://www.notion.so/2f633ffb7fda80b595c1defc8e07ffcd
2. 点击 "+ New" 创建新页面
3. 填写：
   - Title: `欢迎访问我的博客`
   - Status: `Published`
   - Date: 今天的日期
4. 写一些内容
5. 保存

### 更新博客

**方式 1**: 手动触发
- GitHub Actions → "Run workflow"

**方式 2**: 等待自动同步
- 每天 08:00 北京时间自动同步

---

## 🔧 本地开发（可选）

### 创建环境变量

```bash
cd /g/workspaces/code/blog
cp .env.example .env.local
```

编辑 `.env.local`：
```env
NOTION_PAGE_ID=2f633ffb7fda80b595c1defc8e07ffcd
NOTION_TOKEN=secret_你的token
EXPORT=true
BASE_PATH=
NEXT_PUBLIC_LANG=zh-CN
NEXT_PUBLIC_LINK=https://gming.space
```

### 启动开发

```bash
npm install
npm run dev
```

访问：http://localhost:3000

### 测试构建

```bash
# Windows PowerShell
$env:EXPORT="true"
$env:BASE_PATH=""
npm run build

# 预览
npx serve out -p 3000
```

---

## 🐛 常见问题

### 问题 1: 仓库已存在
**解决**: 按照第一步删除旧仓库

### 问题 2: 构建失败 - Notion 数据库无法访问
**解决**:
- 检查数据库是否已共享给集成
- 确认 Token 正确

### 问题 3: 自定义域名无法访问
**解决**:
- 等待 DNS 生效（最多 24 小时）
- 检查 DNS 配置：https://dnschecker.org

### 问题 4: HTTPS 证书错误
**解决**:
- GitHub Pages 设置中取消勾选 "Enforce HTTPS"
- 等待 1 分钟后重新勾选

---

## 📋 检查清单

部署前确认：

- [ ] 删除旧的 Cofea.github.io 仓库（如果存在）
- [ ] 创建新仓库 Cofea.github.io
- [ ] 推送代码到 GitHub
- [ ] 配置 3 个 Secrets（NOTION_PAGE_ID, NOTION_TOKEN, BASE_PATH）
- [ ] Notion 数据库已共享给集成
- [ ] 启用 GitHub Pages（Source: GitHub Actions）
- [ ] 配置自定义域名（gming.space）
- [ ] 配置 DNS 记录
- [ ] 手动触发首次部署
- [ ] 验证站点可访问

---

## 📞 需要帮助？

查看完整文档：
- `README.md` - 通用部署指南
- `DEPLOYMENT_GUIDE_COFEA.md` - 你的详细配置指南

---

**完成后删除此文件，避免暴露个人信息！**
