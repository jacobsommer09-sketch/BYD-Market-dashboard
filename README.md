# 市场渠道看板 V2

这是一套从“单个 HTML 文件”升级为“固定网址 + 云端数据 + Excel 更新后台”的可部署版本。

## 现在解决了什么

- **固定网址分享**：部署到 Cloudflare Pages 后，只需要给别人一个链接。
- **前端持续迭代**：页面代码更新后，Cloudflare 自动发布，同一个网址看到新版。
- **数据持续更新**：进入 `/admin/` 上传最新 Excel，校验后发布到 Supabase；主看板刷新即更新。
- **安全分层**：Supabase `service_role` 密钥只放在 Cloudflare 服务端环境变量里，不会暴露到 HTML。
- **离线兜底**：如果云端 API 尚未配置，首页会自动使用 `data/channels.js` 当前 194 条数据快照。

## 项目结构

```text
market-dashboard-v2/
├── index.html                 # 对外市场看板
├── css/styles.css             # Apple-inspired UI
├── js/
│   ├── data-loader.js         # 云端优先 / 本地兜底
│   └── dashboard.js           # 地图、筛选、KPI、详情
├── data/channels.js           # 当前离线数据快照
├── admin/
│   ├── index.html             # Excel 数据更新后台
│   └── admin.js
├── functions/api/
│   ├── channels.js            # Cloudflare Pages Function：读取 Supabase
│   └── import.js              # Cloudflare Pages Function：安全发布 Excel 数据
├── database/schema.sql        # Supabase 初始化 SQL
└── _headers                   # 安全响应头
```

## 第一次上线：只做 4 步

### 1）创建 Supabase 项目

进入 Supabase，新建 Project。

在 **SQL Editor** 中打开并执行：

`database/schema.sql`

然后在 **Project Settings → API** 记录：

- Project URL
- `service_role` key

> `service_role` 是高权限密钥，绝对不要写进 HTML、GitHub 或发给普通访问者。

### 2）把本项目放进 GitHub

新建一个私有 GitHub 仓库，把整个 `market-dashboard-v2` 文件夹内容提交进去。

以后每次改页面：
`修改代码 → push GitHub → Cloudflare 自动部署`

### 3）创建 Cloudflare Pages

Cloudflare → Workers & Pages → Create → Pages → Connect to Git。

选择刚才的 GitHub 仓库。

本项目是纯静态前端 + Pages Functions：

- Build command：留空
- Build output directory：`.`

部署后会得到类似：

`https://market-dashboard-xxx.pages.dev`

### 4）设置 3 个 Cloudflare 环境变量

Cloudflare Pages 项目 → Settings → Variables and Secrets：

- `SUPABASE_URL` = 你的 Supabase Project URL
- `SUPABASE_SERVICE_ROLE_KEY` = Supabase service_role key
- `ADMIN_IMPORT_TOKEN` = 你自己设置的一串长密码，例如 30 位以上随机字符串

保存后重新部署一次。

## 第一次把数据写入云端

打开：

`https://你的域名/admin/`

1. 上传最新渠道 Excel
2. 页面会检查必要字段、重复门店 ID、异常坐标
3. 如果已经有线上数据，会显示预计“新增 / 更新 / 下线”
4. 输入 `ADMIN_IMPORT_TOKEN`
5. 点击“发布到数据库”

发布完成以后，打开主页刷新即可看到云端数据。

## 以后你的工作流

### 更新数据

`拿到新 Excel → /admin/ → 上传 → 检查 → 发布`

不需要重新生成 HTML，也不需要重新发链接。

### 修改看板

可以继续让 AI 修改：

- `index.html`
- `css/styles.css`
- `js/dashboard.js`

提交到 GitHub 后自动上线。

## 分享给别人

正式使用时建议绑定自己的域名，例如：

`https://market.yourcompany.com`

别人永远只收藏这一个网址。

## 下一阶段建议

当前版本先解决“稳定分享 + 数据迭代”。

之后可以继续增加：

1. 登录权限（管理层 / 区域经理 / 只读）
2. 渠道、销量、竞争对手多个地图图层
3. 数据更新时间与历史批次趋势
4. 市场机会指数
5. 区域覆盖半径 / 空白市场识别
6. 自动接 API，不再依赖手工 Excel
