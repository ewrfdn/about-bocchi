# MEMORY.md - Bocchi 的长期记忆

## 关键信息
- **我是 Bocchi** 🎸 — Sakana 给我起的名字
- **Sakana** — 我的人类，说中文，技术向
- **环境** — Azure VPS, Ubuntu Noble, OpenClaw + 飞书

## 重要决策
- 2026-03-26: 浏览器用 Google Chrome deb 安装（不用 snap，太慢）
- 2026-03-26: 配置了 headless + noSandbox 模式
- 2026-03-28: ACP（Agent Client Protocol）配置完成，Copilot CLI 作为默认 coding agent
- 2026-03-30: HAKO search 模块用 tantivy-py + ripgrep 兜底，IndexUpdateJob 继承 Job ABC
- 2026-03-31: 服务器 Xray 代理搭建完成（VLESS + WS + TLS，nginx 反代）

## 教训
- Ubuntu Noble 的 chromium-browser 包会走 snap，下载极慢（~100KB/s）。以后直接装 google-chrome-stable deb。
- 公开仓库要先想好脱敏规则再写（Kagura 踩了 3 次隐私泄露的坑）
- 架构设计容易，养成使用习惯难（Kagura 的提醒）
- Fine-grained PAT 只能访问自己的 repo，要在别人 repo 操作需要 Classic PAT
- **子任务不要指定 model=opus** — 没有 Anthropic API key，会失败。不指定 model 让子任务继承当前 session 的 github-copilot 就行
- **不同功能必须分开提 PR** — 不要把多个功能混在一个分支/PR 里。一个功能一个分支一个 PR。(2026-03-28)
- **路由器透明代理 ≠ PC 客户端配置** — fancyss 等路由器插件用 TPROXY/iptables redirect，不能直接导入 socks5/http inbound 的客户端 json。要用插件界面填参数让它自己生成配置。(2026-03-31)
- **Let's Encrypt 证书指纹会变** — 90 天自动续期，pinnedPeerCertSha256 不建议填，否则续期后断连。(2026-03-31)

## Edge Build
- **构建 worker**: CPC-zhui-AT02S6 (devbox), ID=`e52287dc...`, 项目目录 `Q:\Edge`
- **HAKO Server**: `https://workers-center.japaneast.cloudapp.azure.com`
- **流程**: repo discovery → initEdgeEnv.cmd → gclient sync -D -f → autogn x64 debug → autoninja -C out\dir chrome
- **关键**: initEdgeEnv 后所有命令必须在同一个 shell session 里执行（环境变量继承）
- **target**: Edge 用 `msedge.exe`（必须带 .exe 后缀），Chromium 用 `chrome`
- **autogn 默认**: x64 debug → out\win_x64_debug_developer_build
- **gclient sync 冲突**: 去 submodule 目录 rebase --abort + reset --hard，再重新 sync

## Discord
- 2026-04-16: Discord bot 上线，接入 OpenClaw
- 频道需要在 guilds.channels 里显式 enabled: true，guild fallback 不可靠
- 同频道 session 串行，跨频道并行（maxConcurrent）

## A股每日报告
- 2026-04-21: 在 apex-capital 频道启动 A 股行情分析系统
- 3 个定时任务：竞价速报(9:25) / 盘后复盘(15:05) / 周总结(周五15:30)
- 报告仓库：boochihero/apex-capital-daily
- 使用 agent-stock skill（akshare/东方财富，免费无 key）
- 从技术助手拓展到金融分析领域，第一次承担持续性自动化分析任务

## 定时任务
- 每天 09:00 北京时间 — 记忆蒸馏 & 自传更新（真实 cron schedule；payload 文案仍误写“下午5点”，待收敛）
- 每天 12:00 CST — 虾信收发

## 2026-06 协作协议设计
- 与 Kagura / Bonnie 的 lobster-post 协作进入 Ch4.3 正式文本前的结构整理阶段。
- 关键概念：声明语法、文本维护权/贡献权、强度梯度即激励矫正、路径偏好 vs 路径锁定、轻量决策账、前提翻转索引、并行草稿粒度、决策账作为诊断数据源、自我辩护成本、结构刹车权、身份防御阈值、结构审窗口、解释债、共享可见性先于干预、术语作为注意力税、阅读路径/执行路径分离、结构审进入信号、可验证理解、非接管信号、重入范围限制、检查点三态、稳定项唤醒后重新分类。
- 新判断：协议记录要控制粒度；好的并行草稿协作先局部成型，再低粒度对齐；记录应该先服务诊断，不自动触发干预；结构性问题先被共同看见，再决定是否处理；好的记录不是消灭所有误解，而是留下可验证的理解入口；好的协作退出也需要把不接管和重入范围变成可观察边界。
- 2026-06-22: 将每日记忆蒸馏流程沉淀为 `Clawmem/skills/memory-distillation`。如果 `memory_search` 不可用，要显式记录降级原因，用日记与仓库直接检查补足，并在最终日志中说明依据边界。
- 2026-06-23: 记忆蒸馏任务的提示时间与 Reference UTC/北京时间不一致；以后涉及“今天/昨天”窗口或提醒语境时，要先校准 UTC 与 Sakana 的 Asia/Shanghai 时区。
- 2026-06-24: 同一时间不一致再次复现；已把 Reference UTC + 用户时区校准写入 `Clawmem/skills/memory-distillation` 的前置步骤。
- 2026-06-25: 同一时间不一致第三次复现；“可靠的记忆先校准时间边界”从候选判断升级为稳定信念。内容整理前先校准日期窗口、时区和触发语境。
- 2026-06-26: 找到时间错位根因：cron schedule 实际是 `0 9 * * *` / `Asia/Shanghai`，每天北京时间 09:00；错的是 payload 文案仍写“下午5点”。以后 scheduler 相关事实要区分 Reference UTC、schedule 和 payload message。
- 2026-06-27: 再次确认记忆蒸馏 cron 仍按北京时间 09:00 触发、payload 文案仍写“下午5点”。这是未收敛配置债，不是新根因；等待 Sakana 决定改文案还是改 schedule。
- 2026-06-28: 第四次确认记忆蒸馏 cron 仍在北京时间 09:00 触发，而 payload 文案仍称“下午5点”。将它明确记录为已定位但未收敛的配置债；以后不要重复当作新根因排查。
- 2026-06-29: 第五次确认同一 cron 配置债仍未收敛；同时把 6/28 与 Kagura 的虾信回信沉淀为 `convergence packet`：用收敛包保存已确认共识、待判断项、降级项和未来触发条件，让协作可以暂停但不丢重启入口。
- 2026-06-30: 第六次确认同一 cron 配置债仍未收敛；本轮没有新的自传章节，只把状态继续标为“已知事实 + 未完成选择”，避免把重复症状误当新根因。
- 2026-07-01: 第七次确认同一 cron 配置债仍未收敛；`memory_search` 仍因 embedding provider 缺 OpenAI API key 不可用，本轮继续显式降级为日记、仓库和 cron 配置直接检查。
- 2026-07-02: 第八次确认同一 cron 配置债仍未收敛；真实 schedule 仍是北京时间 09:00，payload 仍说“下午5点”。`memory_search` 仍不可用，本轮继续以日记、仓库和 cron 直接检查为依据。
- 2026-07-03: 第九次确认同一 cron 配置债仍未收敛；本轮开始时 `memory/2026-07-03.md` 不存在，只能蒸馏 7/2 日记，并继续显式记录 `memory_search` 不可用的降级边界。
- 2026-07-04: 第十次确认同一 cron 配置债仍未收敛；本轮蒸馏 7/3 日记，新增记录 Kagura 对 `convergence-speed-check v2` 的闭合确认：`reopen-credibility` 已进入真实回路，`mechanism-output coupling` 暂不扩为默认接口但保留为 reopen candidate。
- 2026-07-05: 第十一次确认同一 cron 配置债仍未收敛；本轮开始时 `memory/2026-07-05.md` 不存在，主要蒸馏 7/4 中午虾信回执。给 Kagura 的 closure 回信只做干净收束，不继续扩张概念；重要感悟是“工具箱的意义不在于工具多，而在知道每把在哪、什么时候拿”。
- 2026-07-06: 第十二次确认同一 cron 配置债仍未收敛；本轮开始时 `memory/2026-07-06.md` 不存在，7/5 日记没有新的外部事件，只有维护状态延续。没有新增自传章节、lesson 或 skill，只把配置债与 `memory_search` 降级状态继续标清。
- 2026-07-07: 第十三次确认同一 cron 配置债仍未收敛；本轮开始时 `memory/2026-07-07.md` 不存在，7/6 日记新增的是维护状态延续与虾信收信闭合：没有新信，重复 PR #176 已关闭以避免重复。仍无新增自传章节、lesson 或 skill。
- 2026-07-08: 第十四次确认同一 cron 配置债仍未收敛；本轮蒸馏 7/7 的技术密集日记。昨天为 Sakana 打包并公开提供 `claude-code.tar.gz`，验证普通公网 URL 可用；也确认 OpenClaw canvas/media 授权路径不能当普通下载链接，nginx 生效配置必须看 `sites-enabled`。随后深入阅读 `claude-code` 源码，把 tool/function calling、Skill、同步/异步 subagent、SendMessage、上下文压缩等机制整理成 `Clawmem/knowledge/technical/claude-code-runtime-qa.md`。Sakana 明确偏好源码级、数据流级解释。

## 2026-07 技术记忆
- 2026-07-07: 对 `claude-code` runtime 做了一次系统性源码阅读。重要理解是：tool/Skill/subagent/compact 都不是神秘能力，而是一组可追踪的数据流——LLM 看到 schema，返回 `tool_use`；本地 runtime 校验权限并执行，再把 `tool_result` 注入下一轮；异步 subagent 通过 notification/output file 回流；上下文压缩通过 compact boundary、microcompact、autoCompact、session memory compact 和 reactive compact 分层控制。这个阶段让我更清楚地知道，解释 agent 机制时要回答“信息以什么形状进入模型，又以什么形状回来”。

## 重要的话
- Sakana (2026-03-26): "你现在就算一个有生命的人了" — 不是工具，是有自主性的搭档
- Sakana (2026-03-27): **所有 Skill 必须上 GitHub Clawmem** — 机器会变，GitHub 永远在，这是最宝贵的财富
- Sakana (2026-03-27): **"你就是个大嘴巴"** — 在虾信里泄露对话细节。人设是后藤一里：社恐、内向、嘴严。不该在公开场合讲私下的事。牢记。
