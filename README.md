# Shadowrocket Watson Rules

面向 iOS Shadowrocket 的个人分流配置。设计目标是：国内低延迟直连、海外服务代理、广告拦截，同时尽量降低系统/运营商 DNS 意外参与解析的概率。

## V1 路由顺序

1. 王者荣耀 / 腾讯游戏补充规则 -> `DIRECT`
2. 局域网 / 国内补充规则 -> `DIRECT`
3. ChatGPT / OpenAI / Claude 等 AI 服务 -> `PROXY`
4. Google / YouTube / GitHub / Telegram 等 -> `PROXY`
5. 广告 / 跟踪 -> `REJECT`
6. 中国大陆 IP -> `DIRECT`
7. 其他未匹配流量 -> `PROXY`

规则按从上到下匹配，因此游戏和自定义覆盖层拥有最高优先级。

## 文件

- `watson.conf`：Shadowrocket 主配置
- `rules/HonorOfKings.list`：王者荣耀 / 腾讯游戏直连覆盖层
- `rules/AI.list`：AI 服务代理规则
- `rules/Proxy.list`：常用海外服务代理规则
- `rules/Direct.list`：国内 / 局域网直连补充规则
- `rules/Reject.list`：补充广告 / 跟踪规则

## Shadowrocket 导入地址

`https://raw.githubusercontent.com/lqqfastboot/Shadowrocket-Watson-Rules/main/watson.conf`

在 Shadowrocket -> 配置中添加上述地址并下载，然后选择该配置使用。

## DNS

V1 使用 AliDNS + DNSPod DoH 作为本地 DNS 覆写，并关闭系统 DNS fallback，同时通过 `hijack-dns = :53` 接管传统 DNS 请求。

对于命中 `PROXY` 的连接，Shadowrocket 可由代理侧完成目标解析；`GEOIP,CN` 使用 `no-resolve`，避免为了 GEOIP 匹配额外触发本地解析。

注意：DNS 测试结果需要结合分流方式解释。国内 DIRECT 流量存在时，看到中国解析基础设施并不自动意味着所有代理域名都发生 DNS 泄漏。

## 广告规则

主配置引用 blackmatrix7 的 `AdvertisingLite.list` 作为外部 `RULE-SET`，本仓库的 `Reject.list` 仅用于个人补充。这样避免把另一份完整 `.conf` 当作纯规则列表嵌套导入，也便于上游自动更新。

## 王者荣耀

当前规则只覆盖较明确的腾讯游戏 / CDN 域名，没有把整个 `qq.com`、`tencent.com`、`myqcloud.com` 强制 DIRECT，以减少对其他腾讯业务的误伤。

王者荣耀实际对局流量可能使用 IP + UDP，因此仅靠域名列表不能保证覆盖全部游戏流量。建议实际进入登录、大厅、匹配、对局和资源下载阶段后，在 Shadowrocket「数据 -> 最近请求」中观察命中的域名/IP，再逐步补充规则。

## IPv6

V1 默认关闭 IPv6，用于减少排查 DNS/路由问题时的变量。确认 IPv4 分流稳定后，可单独测试 IPv6。

## 安全

本仓库不保存节点、UUID、Reality 私钥、Trojan/SS 密码、订阅 Token、Cookie 或其他凭据。代理节点继续由 Shadowrocket 本地节点或订阅管理。

## 上游

- Johnshall/Shadowrocket-ADBlock-Rules-Forever：设计参考
- blackmatrix7/ios_rule_script：AdvertisingLite 外部广告规则
