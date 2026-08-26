# 正式部署：GitHub + Cloudflare + Supabase

这版已经固定为三层：

- GitHub：保存前端代码与版本
- Cloudflare Pages：发布固定网址
- Supabase：保存渠道数据并提供在线数据接口

## 当前已完成

- Supabase 数据库已建好
- 194 条渠道数据已导入
- Supabase `channels-api` 已上线
- 前端已改为直接读取云端渠道数据
- 若云端暂时不可用，页面会自动回退到本地数据快照

## GitHub

把整个项目目录上传到一个仓库，例如：

`market-dashboard`

仓库根目录就是当前目录，不需要 build 命令。

## Cloudflare Pages

连接 GitHub 仓库后：

- Framework preset：None
- Build command：留空
- Build output directory：`.`

发布后即可获得 `*.pages.dev` 地址。

## 数据更新

页面读取的接口：

`https://sshdtzygbjtrvdndnhil.supabase.co/functions/v1/channels-api`

所以以后数据库更新后，Cloudflare 页面无需重新部署，刷新即可读取最新数据。

## 下一阶段

将 `/admin/` 的 Excel 更新后台改为直接写入 Supabase。完成后日常流程会变成：

`新 Excel → /admin/ 上传 → 校验 → 发布 → 看板自动更新`
