# Dnsflare

可视化的修改 Cloudflare Zone 的解析地址


## 原因
Cloudflare 非常鸡贼的 ban 掉了 `externallyManaged` 用户访问 Cloudflare 控制台编辑 DNS Record 的功能, 然后有些 Partner 又已经跑了 ~~不是~~, 但是又眼馋 pro

### 优点
所有请求由本地浏览器产生, 服务端仅进行 CORS 处理

Partner 无法直接添加 A 记录 (据说), 而且 Partner API 在开启 2FA 的情况下无法使用

使用 Github Pages + Cloudflare Worker 进行 serverless 部署~~(这下除了域名炸了应该没有别的炸法了吧)~~

## 使用
到 [Cloudflare 的 API Token 设定](https://dash.cloudflare.com/profile/api-tokens) 中新建一个 Token, 给这个 Token 以下权限

- Zone.DNS 写权限 (用于写入 DNS 记录)
- Zone.Zone 读权限 (用于读取域名列表)

然后访问 [serverless](https://dns.cloudflare.nimaqu.com/) 来登录到面板(severless)

## 还是想用 cname 接入？

[使用这个](https://partner.cloudflare.nimaqu.com/) 走 Partner API 进行域名接入(非 severless, 炸了看心情修) ~~眼馋 Pro 也没用了，Plesk 方式已经没了~~

## License
Open sourced under the MIT license.