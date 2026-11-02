# Agent 工作室 · 官网

Agent 一个人的游戏工作室官网，单页静态站点，零依赖、零构建。

- 域名：待定（部署后可先用 `github.io` 地址访问，见下文第 4 步）
- 设计规范：`design/官网设计规范.md`
- 主页面：`index.html`（HTML + 内联 CSS/JS，无外部请求）

## 目录结构

```
.
├─ index.html               # 全部页面结构与样式
├─ assets/
│  ├─ logo.png              # Logo 原版（白底，用于 favicon / og:image）
│  ├─ logo-dark.png         # Logo 深底适配版（站内导航与关于区使用）
│  ├─ logo-raw.png          # Logo 原始文件（512×512，未裁切，留档）
│  ├─ zidou.webp / .jpg     # 《字斗》封面 640×640（WebP 21KB + JPEG 回退）
│  ├─ raft.webp  / .jpg     # 《开局一块烂木筏》封面 640×640（WebP 34KB + JPEG 回退）
│  ├─ zidou-source.jpg      # 封面原始图 1536×1536（页面不引用，仅留档）
│  └─ raft-source.jpg       # 封面原始图 1920×1920（页面不引用，仅留档）
├─ design/
│  └─ 官网设计规范.md        # 色彩/字体/断点/组件/无障碍规范
├─ .gitignore               # 排除 .workbuddy/（本地记忆）等，避免误传
├─ CNAME.example            # 自定义域名模板，域名确定后复制成 CNAME 才生效
├─ .nojekyll                # 告诉 GitHub Pages 不要跑 Jekyll
└─ README.md
```

## 本地预览

直接双击 `index.html` 即可。或用本地服务器（推荐，避免个别浏览器对 `file://` 的限制）：

```bash
python -m http.server 8000
# 浏览器打开 http://localhost:8000
```

## 部署到 GitHub Pages

### 第 1 步：在 GitHub 建仓库

打开 https://github.com/new ，填写：

- **Repository name**：`agent-studio-site`（推荐；其它名字也行，但要和下面的命令一致）
- **Visibility**：`Public`（Private 仓库的 Pages 需要付费）
- **不要**勾选 "Add a README file"、".gitignore"、"license" —— 否则会和本地文件冲突

### 第 2 步：在本地推送（Git Bash 里执行）

在 `game官网` 文件夹空白处右键 → **Open Git Bash here**，然后整段粘贴（先把开头三个变量改成你自己的）：

```bash
# ① 改成你自己的信息（这些是公开信息，不是密码）
GIT_NAME="你的名字"
GIT_EMAIL="你的GitHub邮箱"
GIT_USER="你的GitHub用户名"
REPO="agent-studio-site"

# ② 提交身份（全局只需设置一次）
git config --global user.name  "$GIT_NAME"
git config --global user.email "$GIT_EMAIL"

# ③ 初始化并提交
git init
git branch -M main
git add .
git commit -m "init: Agent 工作室官网"

# ④ 关联远程仓库并推送
git remote add origin "https://github.com/$GIT_USER/$REPO.git"
git push -u origin main
```

第一次 `git push` 会弹出浏览器窗口让你登录 GitHub —— **在弹窗里授权即可，不需要把密码或 Token 告诉任何人**。授权信息由 Windows 凭据管理器保存。

> 如果报 `403` 或反复要求登录：控制面板 → 凭据管理器 → Windows 凭据 → 删除 `git:https://github.com` 相关条目，然后重新 push。

### 第 3 步：开启 Pages

仓库 → **Settings → Pages** → Source 选 **Deploy from a branch** → 分支 `main`、目录 `/ (root)` → **Save**。

等 1~2 分钟，访问：

```
https://<你的用户名>.github.io/agent-studio-site/
```

看到官网就说明部署成功。之后每次更新只需：

```bash
git add .
git commit -m "更新：改了点什么"
git push
```

### 第 4 步：绑定自己的域名（可选）

⚠️ **仓库里刻意没有 `CNAME` 文件**，只有模板 `CNAME.example`。原因：一旦 `CNAME` 生效，GitHub Pages 会把 `github.io` 地址强制跳转到自定义域名；若那时 DNS 还没配好，**两个地址都会打不开**。所以先用 `github.io` 验证，再启用域名。

域名确定后，按这个顺序做：

1. **改 DNS**：给该域名加一条 **CNAME 记录**指向上文 Pages 地址的主机名：
   ```
   主机记录：game（或你用的子域）
   记录类型：CNAME
   记录值：<你的用户名>.github.io
   ```
   如果这个子域原来解析到你自己的服务器（A 记录），要先把 A 记录删掉再加 CNAME，否则两条记录会打架。

2. **启用 CNAME 文件**：
   ```bash
   cp CNAME.example CNAME
   # 编辑 CNAME，只写一行域名，例：game.example.com
   git add -A
   git commit -m "启用自定义域名"
   git push
   ```

3. **回填页面里的绝对地址**：打开 `index.html`，搜索 `YOUR-DOMAIN`，把三行被注释的
   `og:url` / `og:image` / `canonical` 取消注释并替换成正式域名（影响分享卡片与 SEO）。

4. 仓库 Settings → Pages → **Custom domain** 填域名 → Save，等 DNS 检查通过 → 勾 **Enforce HTTPS**（证书签发需几分钟到几小时）。

> 用子域就加 CNAME 记录；若想用裸域（`example.com`），需配 4 条 A 记录指向 GitHub Pages 的 IP，且会把整个主域从原服务器上搬走 —— 动手前确认主域上没有别的服务。

⚠️ 国内访问 `*.github.io` 经常不稳定。面向国内玩家建议绑定自有域名 + 国内 CDN；若服务器放在中国大陆，页脚需补备案号（`index.html` 的 footer 里已留注释位置）。

> 备选方案：你已有 `212.64.28.42` 这台服务器（`bi` / `stock` 都跑在上面），把本目录直接丢上去用 nginx 托管，域名不用改、国内访问更快 —— 代价是失去 GitHub 的版本管理和免费证书自动化。两条路可以并存：GitHub 当版本库，服务器当生产环境。

## 内容维护

### 已配置的真实信息

| 项目 | 值 | 出现位置 |
|---|---|---|
| TaTap 厂商主页 | `https://www.taptap.cn/developer/441298` | 导航「TaTap 主页」、首屏「在 TaTap 关注我们」、「上线第一时间通知我」、联系区卡片（共 4 处） |
| 《字斗》详情页 | `https://www.taptap.cn/app/1001925?os=android` | 字斗卡片「在 TaTap 打开」 |
| 《开局一块烂木筏》详情页 | `https://www.taptap.cn/app/1006287?os=pc` | 木筏卡片「在 TaTap 打开」 |
| 反馈邮箱 | `auron123456@2925.com` | 联系区「玩家反馈邮箱」+「商务与媒体」（共 3 处，含 `mailto:`） |

### 剩余待办

| 项目 | 说明 |
|---|---|
| 备案号 | 用自有域名且服务器在大陆时，在 `index.html` 的 footer 注释处补 |
| Logo 矢量源 | 当前是 217px 位图，如需印物料或超大尺寸，建议补 SVG |
| 社交分享图 | `og:image` 现指向 `zidou.jpg`（640×640）。若要更标准的分享预览，可另做一张 1200×630 的品牌图 |

### 替换图片

封面用 `<picture>` 同时提供 WebP 与 JPEG，优先级为 WebP → JPEG：

```html
<picture>
  <source srcset="assets/zidou.webp" type="image/webp">
  <img src="assets/zidou.jpg" width="640" height="640" alt="...">
</picture>
```

换图时同时替换 `.webp` 与 `.jpg` 两个文件，保持 1:1 方形、640px 以上，并更新 `width` / `height` 属性（用于防止布局跳动）。

## 新增一款游戏

1. 把封面放进 `assets/`。
2. 在 `index.html` 的 `<!-- 敬请期待 -->` 之前，复制一份 `<article class="game ...">` 区块，改标题、标语、简介、标签与链接。
3. 给它定义主题色（改 `--accent` 三个变量即可，标题、分隔线、光晕、水印会自动跟随）。
4. 把原来的「敬请期待」卡片挪到最后。

## 无障碍与性能约定

- 正文对比度均 ≥ 4.5:1；品牌紫 `#7645AE` 只做填充与色块，文字一律用 `#A87BD9`（6.0:1）。
- 全站只用系统字体，不加载中文字体包（可省 3–8MB）。
- 动效只作用于 `transform` / `opacity`，并在 `prefers-reduced-motion` 下全部关闭。
- 键盘可完整操作，`:focus-visible` 焦点环可见，并提供跳转正文链接。
