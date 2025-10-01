## 介绍

一个使用 Cloudflare Pages 创建的 URL 缩短器

*Demo* : [https://short-3ud.pages.dev/](https://short-3ud.pages.dev/)


### 利用Tencent EdgeOne Pages 部署
> 未完成，Tencent EdgeOne的KV 存储还在申请中

一键部署：

[![Use EdgeOne Pages to deploy](https://cdnstatic.tencentcs.com/edgeone/pages/deploy.svg)](https://edgeone.ai/pages/new?repository-url=https://github.com/x-dr/short)


### 利用Cloudflare pages部署


1. fork本项目
2. 登录到[Cloudflare](https://dash.cloudflare.com/)控制台.
3. 在帐户主页中，选择`pages`> ` Create a project` > `Connect to Git`
4. 选择你创建的项目存储库，在`Set up builds and deployments`部分中，全部默认即可。
5. 点击`Save and Deploy`，稍等片刻，你的网站就部署好了。
6. 创建D1数据库参考[这里](https://github.com/x-dr/telegraph-Image/blob/main/docs/manage.md)
7. 执行sql命令创建表（在控制台输入框粘贴下面语句执行即可）

```sql
DROP TABLE IF EXISTS links;
CREATE TABLE IF NOT EXISTS links (
  `id` integer PRIMARY KEY NOT NULL,
  `url` text,
  `slug` text,
  `ua` text,
  `ip` text,
  `status` int,
  `create_time` DATE
);
DROP TABLE IF EXISTS logs;
CREATE TABLE IF NOT EXISTS logs (
  `id` integer PRIMARY KEY NOT NULL,
  `url` text ,
  `slug` text,
  `referer` text,
  `ua` text ,
  `ip` text ,
  `create_time` DATE
);

```
8. 选择部署完成short项目，前往后台依次点击`设置`->`函数`->`D1 数据库绑定`->`编辑绑定`->变量名称填写：`DB` 命名空间 `选择你提前创建好的D1` 数据库绑定

9. 重新部署项目，完成。


### API

#### 短链生成

```bash
# POST /create
curl -X POST -H "Content-Type: application/json" -d '{"url":"https://131213.xyz"}' https://d.131213.xyz/create

# 指定slug
curl -X POST -H "Content-Type: application/json" -d '{"url":"https://131213.xyz","slug":"scxs"}' https://d.131213.xyz/create

# 使用访问令牌（如果设置了 ACCESS_TOKEN 环境变量）
curl -X POST -H "Content-Type: application/json" -d '{"url":"https://131213.xyz","token":"your-access-token"}' https://d.131213.xyz/create

# 支持非 HTTP/HTTPS 的 URI（如 mailto:、tel: 等）
curl -X POST -H "Content-Type: application/json" -d '{"url":"mailto:test@example.com"}' https://d.131213.xyz/create

```



> response:

```json
{
  "slug": "<slug>",
  "link": "http://d.131213.xyz/<slug>"
}
```

#### 访问控制

如果在 Cloudflare Pages 的环境变量中设置了 `ACCESS_TOKEN`，则所有创建短链接的请求都需要提供正确的令牌：

1. 在 Cloudflare Pages 项目设置中，添加环境变量 `ACCESS_TOKEN`，值为您的自定义令牌
2. 在请求中包含 `token` 参数，其值应与 `ACCESS_TOKEN` 匹配
3. 如果未设置 `ACCESS_TOKEN` 环境变量，则不需要令牌即可创建短链接



