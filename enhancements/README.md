# V4 可选增强（试验版）

V3 主配置不变。本目录借鉴 LoonKissSurge 的模块化思路，不复制其脚本，不依赖其构建服务。

## 使用方法

Mihomo：将 `mihomo/ads.yaml` 和 `mihomo/httpdns.yaml` 作为 classical rule-provider 引入，behavior: classical。分别按需添加 `RULE-SET,Watson-Ads,REJECT` 和 `RULE-SET,Watson-HTTPDNS,REJECT`。建议先仅启用广告规则。规则须置于宽泛 GEOSITE 及 MATCH 之前，保留业务专用规则的优先级；如果某条广告域名已被上方规则覆盖，不要盲目调整金融或 AI 策略。

Shadowrocket：将对应 `.list` 作为可选 RULE-SET 使用，确认客户端支持该格式。不要直接导入 Surge 脚本。

HTTPDNS 拦截可能影响应用解析、登录或 CDN 调度，默认关闭；金融应用出现异常立即撤销。不得阻止通用 DoH/DoT 服务或修改全局 DNS。域名过滤不能保证去除同域广告。

候选域名仅为人工整理的小型初始清单，未做实时归属与客户端验证，不代表 LoonKissSurge 的完整规则；后续引入上游前需核查来源、许可、误伤与版本。参考：https://github.com/QingRex/LoonKissSurge 。不引入 MITM、远程脚本或自动更新。sing-box 转换及自动化验证留待客户端版本确认。
