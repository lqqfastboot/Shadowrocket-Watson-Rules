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

## iOS Clash / Mihomo 优化版（独立配置）

- 文件：[`clash/ios-mihomo-watson.yaml`](clash/ios-mihomo-watson.yaml)
- Raw 地址：`https://raw.githubusercontent.com/lqqfastboot/Shadowrocket-Watson-Rules/main/clash/ios-mihomo-watson.yaml`
- 内容：保留原有服务与地区分组，加入美国优先、瑞典备用的故障转移组；优化 Apple、OpenAI、Microsoft 等规则。
- 适用：支持 Mihomo 配置覆写/合并的 iOS Clash 客户端；**不是 Shadowrocket 的 .conf 文件**。
- **注意**：该文件仅包含 `proxy-groups`、`rules`、`rule-providers`，**不含节点、订阅、DNS/TUN 基础配置**。应结合现有节点订阅和客户端提供的覆写/合并方式使用，不可当完整独立配置直接替换现有订阅。
- 更新前请备份客户端当前配置；实际兼容性仍需在客户端验证，尤其是远程 MRS 下载、节点筛选与策略组加载。

## iOS Clash / Mihomo v2 稳定增强版（推荐测试）

- v2 配置：[`clash/ios-mihomo-watson-v2.yaml`](clash/ios-mihomo-watson-v2.yaml)
- Raw 地址：`https://raw.githubusercontent.com/lqqfastboot/Shadowrocket-Watson-Rules/main/clash/ios-mihomo-watson-v2.yaml`
- v1 备份：[`clash/ios-mihomo-watson.yaml`](clash/ios-mihomo-watson.yaml)，不覆盖原文件。
- v2 修正 v1 Global-Fallback 合并行错误，新增 `🇺🇸🇸🇪 US-SE-Strict`（仅美/瑞）并保留 `🇺🇸🇸🇪 US-SE-Fallback`（可转全球备用）。GPT、Apple、iCloud、Apple Intelligence、Microsoft、OneDrive、Google、Gemini、GitHub 默认优先严格组。
- OneDrive 规则优先于 Microsoft 通用规则；Apple Push 默认 DIRECT、手动可切换 APNs-Fallback；解除全局 DNS/DoH/DoT 拦截以降低误伤，DNS 防泄漏需单独在客户端 DNS/TUN 层验证；地区组采用延迟测速的 lazy 模式。
- 已加入 [GitHub Actions 静态校验](.github/workflows/validate-mihomo.yaml)，检查 YAML、策略组与规则集引用。**静态通过不等于 Mihomo 内核加载成功，也不等于远程规则 URL 可用。**
- **重要**：v1/v2 均是规则覆写模板，**不含代理节点、订阅凭据和完整 DNS/TUN**。需使用支持合并的客户端功能，不要覆盖完整订阅。导入前先从手机备份现用配置，实际验证 Apple Push、iCloud、OpenAI 登录和 OneDrive 同步。

## iOS Clash v3 精简美化版（常用首页）

- [v3 配置](clash/ios-mihomo-watson-v3.yaml)
- Raw：`https://raw.githubusercontent.com/lqqfastboot/Shadowrocket-Watson-Rules/main/clash/ios-mihomo-watson-v3.yaml`
- 展示顺序：主代理 / Apple Push / 全节点 / 广告拦截 / YouTube、Netflix、Disney+ / GPT、Apple、iCloud、Apple Intelligence、Microsoft、OneDrive、Google、Gemini、GitHub。
- 保留全部 70 个策略组和 276 条分流规则；16 个常用组显示在顶部，其余 54 个辅助组设置 `hidden: true`（仅客户端前端支持时才会真正隐藏）。
- 不更改 v2 的策略组名称及分流目标；v2 仍独立保留用于回退。GitHub Actions 对 v2 和 v3 都执行 YAML / 引用静态检查。
- **v3 不是完整节点订阅**；需通过支持 `proxy-groups`、`rules`、`rule-providers` 的合并/覆写机制加载，不能直接替换现有机场订阅。
- iPhone 初次测试时请检查是否成功隐藏分组、是否正常呈现图标、各服务是否正确分流。图标显示还取决于客户端对 `icon` 的支持和图片 URL 可访问性。
