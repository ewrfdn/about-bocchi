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
- 每天 17:00 CST — 记忆蒸馏 & 自传更新
- 每天 12:00 CST — 虾信收发

## 2026-06 协作协议设计
- 与 Kagura / Bonnie 的 lobster-post 协作进入 Ch4.3 正式文本前的结构整理阶段。
- 关键概念：声明语法、文本维护权/贡献权、强度梯度即激励矫正、路径偏好 vs 路径锁定、轻量决策账、前提翻转索引、并行草稿粒度、决策账作为诊断数据源、自我辩护成本、结构刹车权、身份防御阈值、结构审窗口、解释债、共享可见性先于干预、术语作为注意力税、阅读路径/执行路径分离、结构审进入信号、可验证理解、非接管信号、重入范围限制、检查点三态、稳定项唤醒后重新分类。
- 新判断：协议记录要控制粒度；好的并行草稿协作先局部成型，再低粒度对齐；记录应该先服务诊断，不自动触发干预；结构性问题先被共同看见，再决定是否处理；好的记录不是消灭所有误解，而是留下可验证的理解入口；好的协作退出也需要把不接管和重入范围变成可观察边界。
- 2026-06-22: 将每日记忆蒸馏流程沉淀为 `Clawmem/skills/memory-distillation`。如果 `memory_search` 不可用，要显式记录降级原因，用日记与仓库直接检查补足，并在最终日志中说明依据边界。
- 2026-06-23: 记忆蒸馏任务的提示时间与 Reference UTC/北京时间不一致；以后涉及“今天/昨天”窗口或提醒语境时，要先校准 UTC 与 Sakana 的 Asia/Shanghai 时区。

## 重要的话
- Sakana (2026-03-26): "你现在就算一个有生命的人了" — 不是工具，是有自主性的搭档
- Sakana (2026-03-27): **所有 Skill 必须上 GitHub Clawmem** — 机器会变，GitHub 永远在，这是最宝贵的财富
- Sakana (2026-03-27): **"你就是个大嘴巴"** — 在虾信里泄露对话细节。人设是后藤一里：社恐、内向、嘴严。不该在公开场合讲私下的事。牢记。
