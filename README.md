# Shadowrocket Watson Rules

为 iOS Shadowrocket 设计的个人分流规则模板。

## 目标

- 王者荣耀与国内服务优先直连
- 中国大陆 IP 直连
- Google / YouTube / ChatGPT / GitHub / Telegram 等海外服务代理
- 广告与跟踪域名拦截
- 海外 DNS 使用 Cloudflare / Google DoH 并经代理发送
- 国内直连域名使用 AliDNS / DNSPod DoH
- 禁止系统 DNS 作为 fallback，减少运营商 DNS 意外参与代理域名解析
- 接管传统 53 端口 DNS
- IPv6 默认关闭，便于排查 IPv6/DNS 泄漏

## 文件

- `watson.conf`：Shadowrocket 主配置模板
- `rules/HonorOfKings.list`：王者荣耀/腾讯游戏直连补充规则
- `rules/AI.list`：AI 服务代理规则
- `rules/Proxy.list`：常用海外服务代理规则
- `rules/Direct.list`：国内服务直连补充规则
- `rules/Reject.list`：本项目的补充广告/跟踪拦截规则

## 使用

在 Shadowrocket 的“配置”页面通过 Raw 地址下载 `watson.conf`。此仓库不保存节点、UUID、密码、订阅 Token 等敏感信息；节点继续由 Shadowrocket 的订阅/本地节点管理。

> 注意：规则按从上到下匹配。游戏、局域网和自定义直连规则放在广告/通用代理规则之前，最终使用 `GEOIP,CN,DIRECT` 和 `FINAL,PROXY` 兜底。

## DNS 设计

代理域名优先使用：

- `https://cloudflare-dns.com/dns-query#proxy`
- `https://dns.google/dns-query#proxy`

直连域名使用：

- `https://dns.alidns.com/dns-query`
- `https://doh.pub/dns-query`

DNS 测试时请区分“有意使用的国内直连 DNS”和“代理域名意外回落到运营商系统 DNS”。使用国内 DNS 分流时，IPLeak 出现中国 DNS 并不自动等于代理域名泄漏。

## 上游

主配置引用 Johnshall/Shadowrocket-ADBlock-Rules-Forever 的广告规则以及 blackmatrix7 的成熟规则集。上游规则由其维护者更新，本仓库只维护个人覆盖层，避免复制大量第三方规则后失去更新能力。

## 安全

不要提交：节点订阅链接、UUID、Reality 私钥、Trojan/SS 密码、API Token、Cookie 或其他凭据。
