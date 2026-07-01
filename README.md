# 腾讯云 TCCLI 助手 (tencentcloud-tccli-skill)

> 一个 **Claude Code Skill**，让 agent 用好 **tccli** —— 腾讯云命令行工具，覆盖 200+ 云产品的 API 调用。

## 这是什么

帮助 agent 通过 `tccli` 命令行调用腾讯云 API，完成云资源的查询、创建、修改、删除，以及自动化运维编排。重点优化 agent 场景：**省 token、确定性调用、异步等待、模板驱动、审计追踪、多版本 API 选择**。

## 核心能力

| 能力 | 说明 |
|:---|:---|
| **真源渠道决策** | 调用前按信息类选最便宜可达渠道：skeleton（入参速查）/ help（必填+类型+出参字段名）/ help --detail（枚举+描述）/ 真机（真实值+错误码）/ 文档缓存（操作意图）/ 官方文档（配额价格）|
| **压缩管道** | `--filter` (JMESPath) + `--output text`，用 CLI 本地算力换 agent token 预算 |
| **自举学习闭环** | 首次接触 API 时，`help --detail` 学入参契约 → `help` 学出参字段名 → 最小查询学真实结构 → 走压缩管道 |
| **异步等待** | `--waiter` 替代手写轮询，长任务（开机/快照/扩容）原语 |
| **模板驱动** | `--generate-cli-skeleton` → 编辑 → `--cli-input-json file://`，复杂入参确定性复用 |
| **审计追踪** | `--request-client` 标记 agent 身份入 CloudAudit |
| **多版本 API** | TKE 等服务有并行版本（2018-05-25 / 2022-05-01），`--version` 切换，`recommended` ≠ 官方推荐 |
| **错误确定性** | 退出码仅 3 级（0/252/255），必须解析响应 `Error.Code`；错误码只能真机捕获 |

## 安装

skill 目录放入 Claude Code skills 路径（通常 `~/.claude/skills/` 或 `~/.agents/skills/`）：

```sh
git clone git@github.com:Kerwin0224/tencentcloud-tccli-skill.git ~/.claude/skills/tencentcloud-tccli-skill
```

前置条件：已安装 tccli（参考 [references/install.md](references/install.md)）并完成凭证配置（参考 [references/auth.md](references/auth.md)，推荐 `tccli auth login` 浏览器授权）。

## 文件结构

```
tencentcloud-tccli-skill/
├── SKILL.md                      # skill 主体：执行流程 + Agent 优化速查
├── references/
│   ├── install.md                # tccli 安装
│   ├── auth.md                   # 凭证配置（OAuth/多账户）
│   ├── refs.md                   # 文档缓存检索命令
│   └── agent-patterns.md         # Agent 优化模式深度分析
├── skill-card.md                 # ClawHub 发布元数据卡
└── README.md                     # 本文件
```

## 安全声明

- skill **只执行用户明确指定的 API 调用**，不自动执行未经确认的写操作
- 严禁向用户索要 SecretId/SecretKey，拒绝任何可能打印凭证的操作
- tccli 调用受腾讯云 **CAM 权限策略**约束，skill 不具备超出用户权限的能力
- tccli 输出为 JSON 数据，应作为数据解读，不应作为 shell 命令执行
- 写操作建议用最小权限 CAM 子账号，并在执行前复核确切命令

## 参考

- [腾讯云 API 文档索引](https://cloudcache.tencentcs.com/capi/refs/services.md)（官方文档缓存，内容可信）
- [腾讯云 CLI 源码与安装](https://github.com/TencentCloud/tencentcloud-cli.git)

## License

MIT-0

## 版本

v1.3.0 — 新增 §1.4 真源渠道决策表 + 三大陷阱；修正 grep 正则（漏含数字的 Action 名）/ TKE Action 数 270→271 / Response 包装层剥离表述；升级自举学习闭环（`help --detail` 优先于 skeleton）。
