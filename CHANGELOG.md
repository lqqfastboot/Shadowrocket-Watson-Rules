# Watson Clash / Mihomo 更新日志

本日志记录 `clash/ios-mihomo-watson-*.yaml` 的主要变化。**这些文件是覆写/合并模板，不是包含节点、DNS/TUN 的完整订阅。** Shadowrocket 的 `watson.conf` 属于独立配置体系。

## V2.4 — 2026-11-06（最新测试版）

- 新增 `⚡ TUIC-Auto`、`⚡ HY2-Auto`、`⚡ VLESS-Auto` 三个协议测速组，供 `PROXY-Gate` 手动选择。
- `🌍 Global-Auto` 测速间隔由 600 秒调整为 300 秒，容差由 50 ms 调整为 10 ms。
- 保留原有服务规则、规则集与默认分流目标；不覆盖旧版文件。
- **限制：** 协议组基于 `exclude-type` 排除其他已知协议；新协议或不同内核的支持情况需要复核。尚无完整实机测试结论。

## V2.3 — 2026-11-06

- 从 `US-SE-Fallback` 移除冗余的 `Global-Auto` 末级引用。
- 为 30 个地区/自动测速及故障转移组设置 `empty-fallback: REJECT`，并添加 5000 ms 超时、3 次失败阈值。
- 对相应 `generate_204` 检测组设置预期 HTTP 204。
- 保留原有规则；**空地区组与嵌套 fallback 仍需客户端测试**。

## V2.2 — 2026-11-05

- 新增可选 `🌍 Global-Auto` 全节点 `url-test` 组。
- 将其添加为现有服务策略组的可选项，不改变原有规则。

## V2.1 — 2026-11-05

- 移除全局 SMTP 与 BT/PT 端口直连。
- 缩小 Google 关键词匹配范围，优化美国/瑞典/越南回退顺序。
- 调整 PikPak 误拦截风险并检查 MRS 规则引用。

## V2 — 2026-11-05

- 修复原版故障转移引用，新增 `US-SE-Strict`。
- 调整 Apple Push、OneDrive、Microsoft 等服务策略和规则顺序。
- DNS/TUN 继续由客户端基础配置管理。

## V1 — 2026-11-04

- 初始 Mihomo 分流覆写模板，建立服务与地区策略组。

## 兼容性与验证

每次升级应至少检查：YAML 解析、策略组引用、规则集引用、远程 MRS 下载、空地区组行为、节点测速、ChatGPT 登录、YouTube 播放、Apple Push 与 OneDrive 同步。**静态校验通过不代表全部运行场景已经验证。**

返回 [README](README.md)。
